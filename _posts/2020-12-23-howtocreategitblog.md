---
title: Github.io 블로그 만들기 (Window 10)
categories: github.io
--- 

### Jekyll을 사용하여 Github.io블로그와 연동해보기
---------------------------------------

1. Git, Github Desktop을 설치한다.
* <https://desktop.github.com/>
2. Ruby를 설치한다. (Default로 설치)
* <https://rubyinstaller.org/downloads/> (with DevKit으로)
<img src="C:\Users\User\Desktop\BlogImgSrc\githubioimg\to\ruby1.png" width="450px" height="300px" alt="ruby1"></img><br/>
3. 설치가 완료되면 아래 명령어 입력
```
$ gem install jekyll bundle
```
4. 번들 설치 후 블로그 생성
```
$ jekyll new 블로그이름
```
5. jekyll serve로 확인해보기
```
$ jekyll serve
```
6. github.io와 연결
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