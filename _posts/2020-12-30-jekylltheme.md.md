---
title: Github.io 블로그 Jekyll 테마 적용하여 쉽게 생성하기 (Window 10)
categories: github.io
layout: archive
tags: [jekyll, github.io, minimal-mistakes]
---

## Jekyll 테마(minimal-mistake) 적용하여 Github.io블로그 쉽게 생성하기

- Ruby와 Git, Github Desktop은 설치되어 있어야 한다.
- 빠뜨린게 있을 수 있습니다. 지적 부탁드립니다!

---

### 테마 선택 & Fork

---
<a href="https://github.com/topics/jekyll-theme" class="btn btn--primary">Jekyll Themes Pages1</a>
<a href="http://jekyllthemes.org/" class="btn btn--primary">Jekyll Themes Pages2</a>

위와 같이 이미 만들어진 테마를 선택한다.

---

해당 포스트는 "minimal-mistake" 테마 위주로 설명한다.

<a href="https://mmistakes.github.io/minimal-mistakes/" class="btn btn--primary">Minimal Mistakes</a>
<a href="https://github.com/mmistakes/minimal-mistakes" class="btn btn--primary">Minimal Mistakes Github</a>

---

원하는 테마를 고른 후 해당 repository를 fork한다.

- 주의할 점!

    repository를 fork한다면 fork까지는 잔디가 생기지만
    이 후 commit시 잔디가 생기지 않는다

---

### 불필요한 파일 삭제

---


해당 repository의 settings에서 "Repository name"을 "깃허브이름.github.io"로 변경한다.

해당 repository를 clone하여 local repository로 다운로드 한다.

```bash
$ git clone "repository 주소"
```

---

연동 완료 후 불필요한 파일 삭제

목록은 아래와 같다.

- .editorconfig
- .gitattributes
- .github
- /docs
- /test
- CHANGELOG.md
- minimal-mistakes-jekyll.gemspec
- README.md
- screenshot-layouts.png
- screenshot.png

docs에는 샘플 데이터가 있으므로 참고하고 싶다면 삭제하지 않아도 되지만 개인 블로그에 샘플 포스트가 표시될 수 있다.

---

### _config.yml 파일 수정

---

취향에 맞게 설정

개인적으로는 "title", "name", "description"부터 바꾸는 것을 추천한다.

이후 자세한 내용은 아래 링크에서 참고하며 수정할 수 있다.

[Quick-Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)

---

### Gemfile 설정

---

> Gem?

- Gem은 Ruby에서 지원하는 패키지 시스템, 필요한 프로그램을 관리할 수 있도록 도와준다
- 다른 언어에서의 라이브러리의 개념으로 볼 수 있다.

> Gemfile?

- 다양한 Gem을 등록하고 있는 파일

> Bundle?

- Gemfile에 정의된 Gem들의 의존성을 파악해서 올바른 Gem을 사용할 수 있게 해주는 명령어

```ruby
$ gem install bundler
```

위 명령어를 통해 bundler를 설치

---

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 3.5"
gem "minimal-mistakes-jekyll"
gem "kramdown-parser-gfm"
group :jekyll_plugins do
    gem "jekyll-archives"
    gem "jekyll-paginate"
    gem "jekyll-sitemap"
    gem "jekyll-gist"
    gem "jekyll-feed"
    gem "jemoji"
    gem "jekyll-algolia"
end
```

현재 사이트에 적용되어있는 Gemfile

추가로 각자 필요한 것을 검색하여 추가해나가면 된다.

```ruby
$ bundle install
```

위 명령어를 통해 gemfile의 내용들을 설치한다.

---

### _data / navigation.yml 파일 수정

---

상단 네비게이션바 수정

Quick-Start Guide는 #을 통해 주석처리

```ruby
- title: "Categories"
  url: /categories/
```

카테고리를 상단 네비게이션바에 표시

```ruby
- title: "Tags"
  url: /tags/
```

입맛에 맞게 추가한다.

---

### _Pages 디렉토리 추가

---

앞서 url을 설정했으나 layout이 아직 없는 상태, 따라서 layout을 입힌다.

가장 상위 디렉토리에 _pages라는 이름으로 디렉토리를 추가한다.

그리고 하위 파일들을 만든다.

- category-archive.md
- tags-archive.md

```ruby
---
title: "제목"
layout: categories
permalink: /categories/
---
```

"이름" 자리에 표시될 이름을 설정하고 저장한다.

Tags도 같은 방법으로 진행한다.

---

### 새로운 글 포스팅 하기

---

_posts 디렉토리가 없다면 최상위 디렉토리 생성한다.

YYYY-MM-DD-제목.md 형식으로 포스팅을 생성한다.

```ruby
---
title: Github.io 블로그 테마 적용하여 생성하기 (Window 10)
categories: github.io
layout: archive
tags: [jekyll, github.io, minimal-mistakes]
---
```

헤더에 config가 적용 될 수 있도록 위와 같이 추가한다.

여러 샘플들을 참고하며 진행하면 된다.

---