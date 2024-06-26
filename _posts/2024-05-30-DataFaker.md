---
title: "Data Faker를 사용한 더미 데이터 생성 기록"
author: yeon
date: 2024-05-30 00:34:00 +0800
categories: [DEV, Spring]
tags: [DataFaker]
---

응급환자의 적절한 배치를 위한 모니터링 시스템 프로젝트 중 환자 생체 더미 데이터를 DB에 담아야 했습니다.

이를 해결하는 과정을 기록해보도록 할게요!

> IDE : Intellij   
> Java : 17   
> Springboot : 3.2.5   
> DB : MySQL   

# javafaker?

인터넷에서 찾아보니 자바 언어를 이용해 더미 데이터의 임의의 타입과 값을 생성하도록 도와주는 javafaker 라이브러리를 알게되었습니다.

로직 작성하는 데 큰 어려움이 없어보여서 사용하기로 했습니다.

## javafaker 라이브러리 추가

먼저 build.gradle의 dependencies에 라이브러리를 로드하도록 합시다!

```gradle
// 더미 데이터 생성을 도와주는 라이브러리
implementation 'com.github.javafaker:javafaker:1.0.2'
```

## 예시 파일 작성

이후 DummyDataGenerator 클래스 파일을 만들고 아래와 같은 예시 파일을 작성했습니다.

```java
import com.github.javafaker.Faker;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Random;

public class DummyDataGenerator {

    private static final String DB_URL = "jdbc:mysql://{DB_HOST}/{DB_NAME}";
    private static final String USER = "{DB_USERNAME}";
    private static final String PASS = "{DB_PASSWORD}"

    public static void main(String[] args) {
        Faker faker = new Faker();
        Random random = new Random();

        try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String sql = "INSERT INTO patient_vitals (patient_id, blood_pressure, heart_rate, body_temperature, timestamp) VALUES (?, ?, ?, ?, ?)";
            PreparedStatement pstmt = conn.prepareStatement(sql);

            for (int i = 0; i < 100; i++) {
                int patientId = random.nextInt(100) + 1;
                String bloodPressure = random.nextInt(41) + 80 + "/" + (random.nextInt(21) + 60);
                int heartRate = random.nextInt(41) + 60;
                double bodyTemperature = 36.0 + (37.5 - 36.0) * random.nextDouble();
                java.sql.Timestamp timestamp = new java.sql.Timestamp(faker.date().past(365, java.util.concurrent.TimeUnit.DAYS).getTime());

                pstmt.setInt(1, patientId);
                pstmt.setString(2, bloodPressure);
                pstmt.setInt(3, heartRate);
                pstmt.setDouble(4, bodyTemperature);
                pstmt.setTimestamp(5, timestamp);

                pstmt.addBatch();
            }

            pstmt.executeBatch();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

예시 코드를 작성해서 실행해보려고 했더니..

![DummyDataGenerator 오류.png](/assets/img/DummyDataGenerator/DummyDataFaker1.png)

오류가 나길래 오류를 확인해본 결과

```java
import com.github.javafaker.Faker;
```

이 부분을 불러오지 못하고 있었고, 이는 javafaker 라이브러리를 불러오지 못한다는 말이었습니다.

build.gradle 파일에 문제가 있나 확인을 해보았지만, 아무런 문제가 없어보였습니다.

인텔리제이의 캐시 무효화도 해보았지만, 해결되지 않았습니다.

열심히 삽질을 하던 도중..!

[이와 같은 글을 보게 되었습니다.](https://github.com/DiUS/java-faker/issues/733)

![Issue #733](/assets/img/DummyDataGenerator/DummyDataFaker2.png)

javafaker는 스프링 부트 2.7.0 이상은 지원이 되지 않는다고 합니다. :(

저는 스프링 부트 3.2.5를 사용하고 있었고, Java 17을 사용중이기에! 결국, 저는 이미지에 보이는 datafaker를 찾아보았습니다.

# datafaker
[datafaker 깃허브 주소입니다.](https://github.com/datafaker-net/datafaker)


![github:datafaker](/assets/img/DummyDataGenerator/DummyDataFaker3.png)

datafaker는 javafaker의 지원과 더불어 추가 기능을 가진 저와 같이 더미 데이터를 생성해야 하는 상황을 위해 만들어진 라이브러리 같습니다. :)

이를 사용하기 위해서는 datafaker 2.x의 최소 요구 사항은 Java 17 이상, Java 17 이외는 Datafaker 1.x를 사용하면 됩니다.

java 언어 말고도 ruby, perl, python, php, javascript 여러 언어들을 지원하는 것까지 확인할 수 있습니다.

## datafaker 라이브러리 추가
build.gradle에 datafaker 라이브러리를 추가합니다.

```gradle
// 더미 데이터 생성 라이브러리
implementation 'net.datafaker:datafaker:2.2.2'
```

## 예시 파일 작성

```java
import com.smhrd.namnam.entity.AdmissionInfo;
import com.smhrd.namnam.entity.PatientInfo;
import jakarta.persistence.EntityManager;
import jakarta.persistence.EntityManagerFactory;
import jakarta.persistence.Persistence;
import net.datafaker.Faker;

import java.sql.Timestamp;
import java.util.UUID;
import java.util.concurrent.TimeUnit;

public class AdmissionInfoGenerator {

    public static void main(String[] args) {
        // Create EntityManagerFactory
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistence_unit_name");
        EntityManager em = emf.createEntityManager();

        Faker faker = new Faker();

        try {
            em.getTransaction().begin();

            PatientInfo patientInfo = em.find(PatientInfo.class, "patient_id"); // patient_id

            AdmissionInfo admissionInfo = new AdmissionInfo();
            admissionInfo.setAdmissionId(UUID.randomUUID().toString()); // admission ID
            admissionInfo.setPatientInfo(patientInfo); // Set the reference to PatientInfo
            admissionInfo.setAdmissionState(faker.options().option("Admitted", "Discharged")); // Admission state
            admissionInfo.setAdmissionInTime(Timestamp.from(faker.date().past(30, 0, TimeUnit.DAYS).toInstant())); // Random past timestamp
            admissionInfo.setAdmissionOutTime(Timestamp.from(faker.date().future(30, 0, TimeUnit.DAYS).toInstant())); // Random future timestamp
            admissionInfo.setAdmissionResultWard(faker.address().streetName()); // Random ward name

            em.persist(admissionInfo);

            // Commit the transaction
            em.getTransaction().commit();
        } catch (Exception e) {
            e.printStackTrace();
            if (em.getTransaction().isActive()) {
                em.getTransaction().rollback();
            }
        } finally {
            em.close();
            emf.close();
        }
    }
}
```

이와 같이 예시 코드를 작성했습니다.

그런데,,

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("persistence_unit_name");
```

이 코드 줄에 들어갈 persistence_unit_name이 무엇인 줄 몰라서 찾아봤습니다.

JPA를 사용할 때 persistence.xml 파일에서 persistence-unit 태그의 name 속성값이 들어가면 된다고 하는데 저는 persistence.xml 파일을 작성한 적이 없어 알고보니 Spring data JPA를 사용했기 때문에 persistence.xml 파일을 찾을 수 없었고, JpaRepository를 사용하면 된다는 것까지 알 수 있었습니다.

이에 결국 완성된 코드는

```java
import com.smhrd.namnam.entity.*;
import com.smhrd.namnam.repository.*;
import net.datafaker.Faker;
import org.springframework.stereotype.Component;

import java.math.BigDecimal;
import java.sql.Date;
import java.sql.Timestamp;
import java.time.LocalDateTime;
import java.util.List;
import java.util.Random;

@Component
public class DataLoader {

	private final AdmissionInfoRepository admissionRepo;
    private final PatientVitalInfoRepository patientVitalRepo;
    private final Faker faker = new Faker();
    private final Random random = new Random();

    public DataLoader(AdmissionInfoRepository admissionRepo, PatientVitalInfoRepository patientVitalRepo) {
    	this.admissionRepo = admissionRepo;
        this.patientVitalRepo = patientVitalRepo;
    }

    // 스프링 실행 시 더미 데이터 생성해주는 메소드
    public String generateData() {
        try {
            patientVitalGenerator();
            return "더미 데이터 생성 성공!";
        } catch (Exception e) {
            System.out.println(e);
            return "더미 데이터 생성 실패..";
        }
    }

    // 입실번호별 생체정보 생성 메소드
    public void patientVitalGenerator() {
        List<AdmissionInfo> allAdmissions = admissionRepo.findAll();
        LocalDateTime startTime = LocalDateTime.now();

        for (AdmissionInfo selectedAdmission : allAdmissions) { // admission_id(입실 식별자) 선택
            for (int i = 1; i <= 10; i++) { // admission_id(입실 식별자)당 환자 바이탈 수 생성
                PatientVitalInfo patientVital = new PatientVitalInfo();

                // admission_id(입실 식별자, FK)
                patientVital.setAdmissionInfo(selectedAdmission);
                // 환자 성별
                patientVital.setPatientVitalSex(faker.options().option("남", "여"));
                // 체온(°C)
                patientVital.setPatientVitalTemperature(BigDecimal.valueOf(faker.number().randomDouble(1, 36, 38)));
                // 심박수(bpm)
                patientVital.setPatientVitalHr(faker.number().numberBetween(50, 110)); // 표준 범위 ± 오차 범위
                // 호흡수(min)
                patientVital.setPatientVitalRespiratoryRate(faker.number().numberBetween(10, 22)); // 표준 범위 ± 오차 범위
                // 산소포화도(%%)
                patientVital.setPatientVitalSpo2(BigDecimal.valueOf(faker.number().randomDouble(1, 93, 102))); // 표준 범위 ± 오차 범위
                // 수축혈압(mmHg)
                patientVital.setPatientVitalNibpS(faker.number().numberBetween(80, 130)); // 표준 범위 ± 오차 범위
                // 이완혈압(mmHg)
                patientVital.setPatientVitalNibpD(faker.number().numberBetween(50, 90)); // 표준 범위 ± 오차 범위
                // 고통 정도
                patientVital.setPatientVitalPain(faker.number().numberBetween(1, 10));
                // 호소 증상
                patientVital.setPatientVitalChiefComplaint(faker.lorem().sentence());
                // 위험도
                patientVital.setPatientVitalAcuity(faker.number().numberBetween(1, 5));
                // 바이탈을 잰 시간
                LocalDateTime createAt = startTime.plusMinutes(10L * i);
                patientVital.setPatientVitalCreatedAt(Timestamp.valueOf(createAt));

                patientVitalRepo.save(patientVital);
            }
        }
    }

}
```

> TMI. 위에 작성한 코드는 한 테이블에 대한 더미 데이터를 생성한 코드인데.. 모든 Table에 더미 데이터를 생성하느라 오늘 약 6시간 이상은 더미 데이터 생성과 관련해서 프로젝트를 진행한 것 같습니다,, 그래도 덕분에 Spring data JPA에서 Entity들을 어떻게 처리하는 지에 대해 조금 알게되어 기쁘네요. :)

이러한 Spring data JPA를 이용한 더미 데이터 생성 DataLoader 클래스를 작성했고, @Component 어노테이션을 붙여 Spring container가 관리할 수 있게 만들어주었습니다.

이후 DataLoader 컴포넌트를 스프링부트를 실행하면 DataLoader의 generateData() 메소드를 자동으로 실행해 더미 데이터를 생성하기까지 완성을 했습니다.

```java
import com.smhrd.namnam.config.DataLoader;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@SpringBootApplication
public class ProjectApplication implements CommandLineRunner {

	// 더미 데이터 생성 클래스
	@Autowired
	private DataLoader dataLoader;

	// 스프링 부트 실행
	public static void main(String[] args) {
		SpringApplication.run(ProjectApplication.class, args);
	}

	// 더미 데이터 생성 실행
	@Override
	public void run(String... args) throws Exception {
		dataLoader.generateData();
	}

}
```

스프링부트의 첫 시작인 ProjectApplication 클래스 파일에 위와 같이 CommandLineRunner 인터페이스를 implements 해주고 전에 만들어두었던 DataLoader 클래스를 주입시켜준 다음 run 메소드로 generateData() 메소드를 실행하도록 작성해주면 됩니다!

아직 CommandLineRunner에 대해서는 학습이 부족하기 때문에 다음에 기회가 된다면 글을 작성해보도록 하겠습니다.

긴 글 읽어주셔서 감사합니다!