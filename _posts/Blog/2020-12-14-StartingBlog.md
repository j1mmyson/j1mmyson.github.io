---
title: "[개발자 블로그] Jekyll의 Chirpy테마로 깃허브 블로그 만들기"
date: 2020-12-14 +0800
categories: [Blog]
tags: [blogging, chirpy, jekyll]
---

이글의 베이스는 Chipy 공식 홈페이지의 [**Getting Started**](https://chirpy.cotes.info/posts/getting-started/)글이며 번역 + a 느낌으로 써내려보려고한다.

### Installation

1. 깃허브에서 [Chirpy Repo](https://github.com/cotes2020/jekyll-theme-chirpy)를 Fork 따오자. 그 후 Fork따온 레포명을 `{GithubUserName}.github.io`로 변경해주자. (깃허브 user name이 j1mmyson이라면 `j1mmyson.github.io`처럼)

2. 터미널을 켜고 Fork따온 레포를 로컬로 가져오자.

   원하는 위치로 이동하여 아래 명령어를 실행하면된다.<br>
   `$ git clone https://github.com/USERNAME/USERNAME.github.io.git -b master --single-branch`

### Setting up the local environment

로컬에서 블로그를 운영하려면 몇가지 설치가 필요하다.<br>
[Jekyll 설치 가이드](https://jekyllrb.com/docs/installation/)에서 자신의 운영체제에 맞게 설치를 완료하면 된다.
제대로 설치가 되었다면

```bash
$ ruby -v
$ gem -v
$ gcc -v  ## g++ -v, make -v
```

위 명령어들을 실행했을 때 버전이 뜨는것을 확인할 수 있을것이다.
3가지가 제대로 설치되었다면 블로그 레포경로로 이동하여 `$ bundle install`명령을 실행하면 모든 dependencies들이 자동으로 설치될것이다.
혹시 위 명령어에서 ``` can`t find bundler ```와 같은 에러가 발생하였다면 

- Update RubyGems: <br>`$ gem update --system` 을 통해 루비젬을 업데이트해주거나
- Install the exact Bundler: <br>`$ gem install bundler -v "$(grep -A 1 "BUNDLED WITH" Gemfile.lock | tail -n 1)"` 명령어로 설치하는 방법이 있다.

자 이제 모든 requirement를 설치하였다.

### Usage

#### Initialization

이제 프로젝트 폴더로 이동하여 아래 명령어를 통해 Initialization을 해준다.<br>
`$ bash tools/init.sh`

> Note: 깃허브 페이지에 배포하는 것을 원치않는다면 위 명렁어 뒤에 `--no-gh`옵션을 붙여 실행하면된다.

위 명령어를 실행하면 아래와 같은 작업들이 수행된다.

1. 특정 파일과 폴더를 삭제한다.
   - `.travis.yml`
   - `_posts` 폴더 속 파일들
   - `docs` 폴더
2. `--no-gh`옵션을 붙여주었다면 `.github`폴더가 삭제될것이다. 그렇지 않다면, `.github/workflows/pages-deploy.yml.hook`의 `.hook`확장자 파일을 삭제함으로써 Github Action workflow를 셋업할 수 있다. 그 후에 `.github`폴더의 다른 파일, 폴더들을 삭제하면 된다.
3. 자동으로 commit을 생성한다.

### Configuration

`_config.yml`파일로 가서 여러가지 설정값들을 고정해주어야 한다. 아래값들이 대표적인(중요한?) 설정값들이다. `_config.yml`파일에서 아래에 해당하는 값들을 바꾸어주면된다.

- `url`: 블로그의 주소를 넣어주면 된다.<br>
  e.g. `url: 'https://j1mmyson.github.io'`
- `avatar`: 대표사진으로 쓰일 이미지 파일을 넣으면된다. 일반적으로 이미지 파일은 `/assets/img/`폴더에 넣고 불러쓴다.<br>
  e.g. `avatar: /assets/img/byungwook.png`
- `timezone`: [Find your timezone](http://www.timezoneconverter.com/cgi-bin/findzone/findzone)에서 자신이 속한 지역을 찾아 넣으면 된다.<br>
  e.g. `timezone: Asia/Seoul`
- `theme_mode`: 블로그의 테마모드를 설정해주는 값. 다크와 라이트중 선택할 수 있다.<br>
  e.g. `theme_mode: dark`

### Run Locally

포스팅을 배포하기전에 내 로컬에서 포스팅 혹은 변경사항이 제대로 적용되었는지 아래 명령어를 통해 확인해볼 수 있다.

`$ bundle exec jekyll s`

위 명령어를 실행하고 [localhost:4000](http://localhost:4000)으로 접속하여 확인할 수 있다.

### Deploy on Github Pages

1. 아무 변경사항을 `origin/master`(최근에 master -> main으로 바뀌었다고는 하던데..) 푸시해주어 Github Actions workflow를 발동?(trigger) 시킨다. 빌드가 제대로 완료되었다면 레포의 브랜치에 `gh-pages`브랜치가 생성된것을 확인할 수 있을것이다.
2. 레포의 `setting`으로 들어가서 아래로 쭈욱 내리면 Github Pages에 대한 설정이 나온다.
   ![gh-pages](../../../assets/img/gh-pages.PNG)
   위 스크린샷처럼 **Branch**를 `gh-pages`로 변경해준뒤 `save`를 클릭해주자.

**끝났다!** <br>
이제 `_post/`폴더에서 markdown파일을 작성하여 깃허브에 푸시해주면 블로그에 포스팅이 업데이트되는것을 확인할 수 있을것이다.