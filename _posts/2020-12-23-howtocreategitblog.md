---
title: Github.io 블로그 생성하기 (Window 10)
categories: github
tag: page
---
## 테마 적용은 내일 마저 포스팅 하기 

0. Git, Github Desktop을 설치한다.
1. Ruby를 설치한다.
```
https://rubyinstaller.org/downloads/ (with DevKit으로)
```
2. 설치가 완료되면 아래 명령어 입력
```
gem install jekyll bundle
```
3. 번들 설치 후 블로그 생성
```
jekyll new 블로그이름
```
4. jekyll serve로 확인해보기
5. github.io와 연결
```
내 깃허브 이름.github.io로 repository생성
내 jekyll 블로그 디렉토리로 이동, 아래 명령어 순서대로 입력
$ git init
$ git add .
$ git config --global user.name "깃허브 이름"
$ git config --global user.email "깃허브 이메일"
$ git config --list 로 name과 email확인
$ git commit -m "커밋이름"
$ git remote add origin "repository주소" //(HTTPS, SSH등)
$ git push --set-upstream origin main //(원래는 main대신 master였는데 2020년 10월쯤 master라는 단어의 부적절함 때문에 main으로 바뀌었다고 함/ master로 한다면 새로운 branch가 생성되므로 조심)
```