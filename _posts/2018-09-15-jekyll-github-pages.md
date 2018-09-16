---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Jekyll 정적 웹사이트 Github Pages에 호스팅하기
date: 2018-09-15 00:00:00
tags: tech jekyll git
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

* 설치 환경: Ubuntu(Linux)
* Ruby 개발환경 설치
```
sudo apt-get install ruby-dev
gem install jekyll bundler
```

* Jekyll 테마 복제(clone)받기
```
git clone https://github.com/jekyller/jasper.git
```
	- 이 경우 `jasper`라는 디렉토리가 생겨 그 곳에 소스와 파일들이 내려받아짐.
	- Jekyll 테마는 [jekyllthemes.io](https://jekyllthemes.io/)와 같은 곳에서 찾아 내려받거나 Github에서 복제받을 수 있음.

* 의존성 있는 Ruby 라이브러리 일괄 설치하기
```
cd jasper
sudo bundle install
```
	- 복제받은 테마의 `Gemfile.lock` 파일을 참조해 의존성 있는 모든 Ruby 라이브러리를 일괄 설치함.

* Jekyll 서버 로컬로 구동하기
```
cd jasper
sudo nano _config.yml
sudo bundle exec jekyll serve
sudo bundle exec jekyll serve --host 0:0:0:0 --port 7070
```
	- `_config.yml`의 baseurl을 다음과 같이 변경(이렇게 해야 테마 assets(이미지, 레이아웃 등)가 적용되지 않거나 깨져 적용되는 현상을 없앨 수 있음.)
		> (변경 전) baseurl: /some/path/
		> (변경 후) baseurl: /
	- `jekyll serve`를 `bundle`과 함께 실행하는 이유: `bundle install`로 설치한 루비 라이브러리들이 적용되는 환경에서 실행하기 위함.
	- `--host`, `--port`: Jekyll 서버를 구동할 IP 주소와 포트를 명시함. 별도 명시하지 않을 경우 localhost:4000으로 구동함.
	- `--host 0.0.0.0`: 모든 IP 주소 허용 - 외부 공개를 위한 설정

* (참고) 서버 실행 없이 Jekyll 빌드만 하기
```
sudo bundle exec jekyll build --verbose
```
	- `jekyll serve`를 하면 자동으로 빌드까지 한 후 서버를 구동함.
	- `jekyll build`는 서버 구동 없이 빌드만 시킴.
	- `--verbose`: 빌드 과정(빌드 대상, 결과) 로그를 표시하면서 빌드함.

* (참고) 특정 포트가 이미 점유된 경우, 점유한 프로세스 강제 종료하기
```
sudo lsof -i :7070
sudo kill 582
```
	- 7070 포트를 점유하고 있는 프로세스의 ID인 582를 확인해 `kill`함.

* 나의 Github Pages로 배포하기 위한 준비
```
git remote -v
git remote set-url origin https://github.com/emilkwak/emilkwak.github.io
```
	- `git remote -v`를 보면, 현재 복제받은 Github 주소를 그대로 origin으로 갖고 있음을 확인할 수 있음. 이대로 둔 채 `git push`하게 되면 '남의 repository'로 배포하는 의도치 않은(?) 시도를 하게 됨.
	- `git remote set-url`을 통해 origin이 나의 Github Pages용 repository를 향하도록 함.
	- 이 경우 emilkwak.github.io라는 repository가 나의 Github에 이미 생성되어 있음을 전제로 함.

* (참고) origin을 유지한 채 origin2를 새로 만들어 사용하기
```
git remote add orign2 https://github.com/emilkwak/emilkwak.github.io
```

* 나의 Github Pages로 동기화하기
```
git add .
git commit -m "something to say"
git push origin master
git push -f origin master
```
	- `git push -f`: `git pull`을 통한 동기화 없이 강제로 배포(overwrite)하게 함.
