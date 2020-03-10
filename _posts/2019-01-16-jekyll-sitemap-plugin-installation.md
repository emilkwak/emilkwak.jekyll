---
layout: post
cover: 'assets/images/office01.jpg'
navigation: True
title: Jekyll 블로그 사이트맵(sitemap) 설치(플러그인) 방법
date: 2019-01-16 22:30:00
tags: tech jekyll
subclass: 'post tag-speeches'
logo: 'assets/images/pooh03.png'
author: emil
categories: tech
---

개인 블로그(홈페이지 등)의 페이지들이 Google 검색에 잘 노출되게 하기 위해 Google Search Console에 사이트맵(sitemap)을 제출해 두면 좋습니다. 바닐라 Jekyll에는 없지만, Jekyll 플러그인을 설치하면 Jekyll 블로그의 사이트맵을 빌드 때마다 자동 생성(xml)할 수 있습니다.

아래와 같이 우선 사이트맵 플러그인을 설치합니다.
```
sudo gem install jekyll-sitemap
```
Gemfile에 플러그인 설치 명령어를 추가해 둡니다.
```
sudo nano Gemfile

... (파일 하단에 아래와 같이 추가) ...
gem 'jekyll-sitemap'
```
설정 파일 플러그인 부분에 설치된 플러그인을 추가합니다.
```
sudo nano _config.yml

... (파일 내 plugins 부분을 아래와 같이 편집 - 내용 추가) ...
plugins [..., jekyll-sitemap]
```
이제 Jekyll 빌드를 다시 한 후 서버 구동해 보시지요.
```
sudo bundle exec jekyll build --verbose
sudo bundle exec jekyll serve --host 0.0.0.0 --port 7070
```
이제 `your.jekyll.blog.url/sitemap.xml`로 사이트맵 접속이 가능합니다. 이 주소를 Google Search Console에 사이트맵으로 제출하세요. 그러면 블로그 포스팅이 늘어날 때 Google이 보다 효율적으로 주소를 긁어 갈 수 있게 됩니다.
