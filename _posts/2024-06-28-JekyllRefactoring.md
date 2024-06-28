---
title: "GitHub Pages(Jekyll Blog) 리팩토링"
author: yeon
date: 2024-06-28 22:16:00 +0900
categories: [ECT, Jekyll]
tags: [Refactoring]
---

[Jekyll Chirpy(v6.0.1) 테마를 활용한 GitHub 블로그 만들기(2023.6 기준)](https://jjikin.com/posts/Jekyll-Chirpy-%ED%85%8C%EB%A7%88%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0(2023-6%EC%9B%94-%EA%B8%B0%EC%A4%80)/)

위의 블로그를 참고하여 전에 Jekyll 테마를 minimal-mistake 테마에서 Chirpy 테마로 바꾸었다.

# 바꾸게 된 이유

이유는 크게 3가지 이유로 블로그를 개편하기로 했다.

1. 위의 블로그를 참고하여 커스터마이징하기 편하다.
2. 왼쪽, 오른쪽 여백이 없고, 가독성이 좋아보이는 내가 선호하는 UI/UX이다.
3. 기술 관련 글을 작성하기 위해 새로운 마음을 잡기 위해서이다.

# bundle 명령어

참고한 블로그에서 rbenv로 ruby 3.0.2 버전을 설치 후 `bundle` 명령어를 사용해 모듈 설치를 해야 했다.

하지만, 나는 3.0.2 버전에서 `bundle` 명령어가 에러를 내서 3.3.3 버전으로 진행하였다.

![ruby 3.3.3](/assets/img/JekyllRefactoring/rubyversion.png)

# 에러

localhost:4000 환경에 블로그가 나오는 것을 보고 깃허브에 올려 블로그 리팩토링 작업을 마무리하려고 했는데..

![husky error](/assets/img/JekyllRefactoring/huskyerror.png)

`husky` `commit-msg` `commitlint` 와 같은 생소한 단어와 함께 에러 메시지를 보았다.

## commitlint 규칙

```shell
Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint
```

해당 사이트를 들어가 보았더니 `commitlint`라는 커밋 메세지에 규칙을 지정해주는 것이 있다는 것을 알았다.

![commitlint](/assets/img/JekyllRefactoring/commitlint.png)

```shell
git commit -m "refactor: gitignore update"
```

커밋 메세지를 이와 같은 규칙으로 적어주니 `commit`이 되었다.

이후 `push`를 통해 깃허브 저장소까지 업데이트 성공!