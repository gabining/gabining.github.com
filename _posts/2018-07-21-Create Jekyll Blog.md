---
layout: post
title: Create Jekyll Blog
excerpt: "Jekyll Blog 생성하기"
categories: [Jekyll Blog]
comments: true
---



# Jekyll 로 블로그 만들기



#### [기본 설치 ]

1. Ruby 설치하기(For Window Users)

   [Ruby+Devkit 2.4.4-2(x64)](https://rubyinstaller.org/downloads/) 설치

2. Git Bash로 Jekyll 설치

   ```
   gem install jekyll
   ```

3. Jekyll 생성(해당 경로에 지킬을 생성하겠다는 코드)

   ```
   jekyll new .
   ```

   **<u>정상적으로 동작하지 않는 경우</u>** → 의존성 갖는 패키지 추가 설치 필요

   ```
   # 오류뜨는 패키지 모두 추가
   gem install bundler 
   gem install minima 
   gem install jekyll-feed 
   gem install tzinfo-data 
   ```

   

4. 자신의 github에 "내깃허브명.github.com" repository 생성

5. 자신의 local에 "내깃허브명.github.com" 디렉토리 생성



#### [새로운 페이지 생성하기]

1. Git Bash로 새로운 페이지 생성하기

   * 계정 주소에 맞는 페이지 생성하기

     ```
     jekyll new 내깃허브명.github.com
     ```

     ![](C:\Users\Kavin\Desktop\jeykll install.png)

   * 위에서 생성한 디렉토리로 이동

     ```
     cd  ~/내깃허브명.github.com
     ```

   * 서버 구동하기

     ```
     jekyll serve --watch
     
     # 테마 설치 해봤을 때
     bundle exec jekyll serve
     ```

   * 주소창에  `localhost:4000`입력 후 페이지 구동 여부 확인(성공 시 아래 이미지가 뜨게 된다.)

     ![](C:\Users\Kavin\Desktop\blog_success.png)

#### [깃허브 연동하기]

1. 내 repository URL 복사

2. Git Bash 상에서 repository 연결/push

   ```
   # respository connect
   git init
   git remote add origin "repository url" # ""는 입력하지 않음
   git remote -v
   
   # commit / push
   git add .
   git commit -m "Create Blog"
   git push -u origin master
   ```



#### [Jekyll Theme 설정]

1. Jekyll Theme 다운로드 및 압축 해제

   [Jekyll Theme](http://jekyllthemes.org) 다운로드 후 압축 해제하여 local의 내 블로그 디렉토리에 모두 붙여넣는다

2. Git Bash에서 빌드(경로: 내 블로그 디렉토리)

   ```
   # 둘 중 하나 이용
   bundle exec jekyll serve
   jekyll serve
   ```

   * 루비의 버전 문제시에 **bundle update** 해주기
   * Gemfile Error 발생시 **(sudo) bundle install** 입력

3. `localhost:4000` 입력 후 테마가 바뀌었는지 확인

   * _config.yml 파일에서 baseurl을 "" 으로 설정

4. Git Bash에서 내 respository로 파일 푸쉬

   ```
   git add .
   git commit -m "Change Theme"
   git push -u origin master
   ```



#### [참고 - git push error]

* github(변동사항 있음) → local

  ```
  git pull --rebase origin master
  git push -f origin master (-f는 강제실행)
  ```

  

* local(변동사항 있음) → github

  ```
  git add .
  git commit -m ""
  git push -u origin master
  ```






