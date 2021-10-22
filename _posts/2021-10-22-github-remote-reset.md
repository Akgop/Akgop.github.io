---
title: \[Github] 원격 저장소 커밋 되돌리기
categories: 
  - github
tags:
  - git
  - github
  - reset
toc: true
---

알고리즘 스터디 하는 중, 서로 코드 리뷰를 하기 위해 각자 브랜치를 따서 PR을 해야 하는데, 실수로 main에 바로 코드를 푸시한 상황이 발생했다. 우리가 모두 해당 레포지토리에 administrator 권한을 갖고 있었기에 언젠간(?) 발생할 실수였다.

이를 해결해보자.

![reset1](/assets/images/github/reset1.png)

우리 팀원이 실수로 메인에 커밋을 했다. 되돌려보자!

우선 원격에 있는 정보를 가져와야 한다. 

```bash
$ git remote update
```

사실 여기서 바로 `git push —-force` 를 해도 된다. 지금 내 상태는 실수로 진행한 커밋이 없는 기존 main 상태와 똑같기 때문에 이 상태를 그대로 덮어씌워도 된다. 하지만, 본인이 실수로 커밋한 상태를 재현하기 위해서, `git pull`을 해서 실수로 커밋한 상태를 가져왔다.

이제 어떻게 실수로 한 커밋을 지워보자.

`git reset`에는 3가지 옵션이 있다.

```bash
$ git reset --soft 
$ git reset --mixed   // default
$ git reset --hard
```

[Git - Reset 명확히 알고 가기](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Reset-%EB%AA%85%ED%99%95%ED%9E%88-%EC%95%8C%EA%B3%A0-%EA%B0%80%EA%B8%B0)

자세한 내용은 위 링크를 참조하자. 시간이 되면 따로 정리해야겠다.

아무튼 지금 우리 상황에서 필요한건 해당 커밋을 아예 없던 것으로 되돌리고 엎어야 했기 때문에 `--hard` 옵션을 붙여서 되돌렸다. `HEAD~1`은 HEAD로부터 상대적으로 1칸 떨어져있는 지점의 커밋으로 되돌린다는 뜻이다. 해당 위치에 특정 커밋명을 대입해서 그 이후에 있던 커밋을 모두 없던 것으로 만들 수 있다.

![reset2](/assets/images/github/reset2.png)

![reset3](/assets/images/github/reset3.png)

그렇게 잘못된 커밋은 로컬 환경에서 지웠다. 이제 이 상태를 원격으로 push해야 한다. 이때 그냥 하면 pull을 먼저 해서 동기화 하라고 하기 때문에 강제로 push해주는 `—-force` 옵션을 사용한다.

![reset4](/assets/images/github/reset4.png)

그런데 강제(?) 코드리뷰를 위해서 main 브랜치에 프로텍션을 걸어놓은 것이 문제가 되었다. 아래 순서대로 진행해서 `--force`를 사용할 수 있게 하자.

![reset5](/assets/images/github/reset5.png)

- Setting → branch → Protection Rule Edit → Allow force pushes

![reset6](/assets/images/github/reset6.png)

깔끔하게 완-료