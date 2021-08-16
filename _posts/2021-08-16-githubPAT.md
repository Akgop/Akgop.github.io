---
title: \[Github] 깃허브 PAT macOS에서 CLI 기반 인증
categories: 
  - github
tags:
  - personal access token
  - macOS
  - keychain
toc: true
---

## 깃허브 로그인 오류?

```bash
remote: Support for password authentication was removed on August 13, 2021 ~~~
~~~ returned error on 403
```

위 코드처럼 갑자기 push가 안된다. 찾아보니 21년 8월 13일부터 기존 id, password 를 기반으로 한 로그인 방식의 지원이 종료되었다고 한다.

어떻게 해야 할까?

## PAT(Personal Access Token) 발급

이 내용은 다른 블로그들에서 잘 나와 있기 때문에 간단히 텍스트로 설명하자면, 내 계정의 세팅 → 디벨로퍼 세팅 → Personal access token → 새로 발급

## 발급은 받았는데 사용을 어떻게 하는가?

나는 발급을 분명히 받았는데 `git push` 를 해도 계속 이전 방식으로 로그인하면서 실패했다.

그 이유는, macOS의 keychain에서 해당 **비밀번호를 캐싱**하고 있었기 때문이다.

정확한 내용은 아래 링크 참조

[Updating credentials from the macOS Keychain](https://docs.github.com/en/get-started/getting-started-with-git/updating-credentials-from-the-macos-keychain)

위 링크의 내용을 간단히 요약하자면 Keychain Access 방식과 command line 방식이 있다.

### Keychain Access 방식

1. finder를 통해 keychain access 들어가서 `github.com` 검색
2. "인터넷 암호"를 삭제하거나 수정한다.

    ![0816keychain](/assets/images/github/0816keychain.png)

    (수정하는 방법) 암호 보기 누르고, 발급받은 토큰을 복붙하고 저장하자.

### Command line 방식

```bash
$ git credential-osxkeychain erase
host=github.com
protocol=https
> [Press Return]
```

위 명령어를 통해 직접 키체인을 삭제하는 방법이다. 아무것도 안뜨면 잘 설정이 된 것이라고 한다. 다음에 깃허브에 access할때 password를 입력하라고 뜨면, 그때 복붙해둔 토큰을 입력하면 된다.

## 결론

개인적으로는 Keychain Access 방식을 사용해서 직접 암호를 수정해주었더니 오류가 해결되었다.