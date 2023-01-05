---
title:  "[M1 Mac]Ruby 3.0 liveload 실행 시 eventmachine 컴파일 에러 문제 해결하기"
date:   2023-01-05 00:09:23
categories: [GitHub Blog]
tags: [GitHub Blog]
---
---

## Jekyll 로컬 실행 방법
Github 블로그에서 새로운 게시물 작성 후 푸시 하기 전에 로컬에서 확인해 보고 싶은 경우가 있다.  
로컬에서 지킬을 실행하기 위해서는 아래와 같은 명령어를 사용하면 된다.
```shell
bundle exec jekyll serve
```
### liveload로 실행하기
만약 수정할 것이 많거나 style 등을 수정할 때면 바뀐 상태를 즉각적으로 확인하고싶다면  
liveload 옵션을 추가해서 아래와 같은 명령어 실행하면 된다.
```shell
bundle exec jekyll serve --liveload
```

<br/><br/>

## LiveLoad 실행 시 eventmachine 컴파일 에러 해결
### eventmachine 설치하기
liveload를 실행하기 위해서는 eventmachine이라는 라이브러리가 필요하다.
eventmachine은 다음 두 가지 방법으로 설치할 수 있다.
#### RubyGems로 설치
```shell
gem install eventmachine
```
#### Bundle를 사용하는 경우
Gemfile에 다음 코드를 추가한다.
```shell
gem 'eventmachine'
```

라이브러리를 설치한 후에 `bundle install` 명령어를 사용하게 되면 잘 동작하는듯 하다가 다음과 같은 에러가 발생한다.
![img.png](/assets/post/2023-01-04-solved-M1-Mac-Ruby3-eventmachine-error/img.png)  
<br/>
그리고 더 아래쪽에 보면 다음과 같은 로그를 볼 수 있다.
![img.png](/assets/post/2023-01-04-solved-M1-Mac-Ruby3-eventmachine-error/img2.png)  
로그를 읽어보면 openssl이라는 헤더파일이 존재하지 않아서 문제가 생기는 것 같다.  
그래서 homebrew를 사용해서 openssl을 설치해주었다. 명령어는 다음과 같다.
```shell
brew install openssl
```
openssl을 설치한 후에 `bundle install` 을 다시 실행해봤지만 여전히 동작하지 않는다.  
~~보통 이런건 한번에 해결되지 않더라~~  

그래서 구글링을 해보니 다음과 같은 깃허브 이슈를 찾을 수 있었다.  
[eventmachine does not compile with ruby 3.0.0preview1 on macOS #932](https://github.com/eventmachine/eventmachine/issues/932)  
내용을 요약하자면 다음과 같다.
```shell
gem install eventmachine -- --with-openssl-dir=/opt/homebrew/opt/openssl@1.1
```
이거 치면 됨.  

위 링크를 참고해서 동작하지 않았던 이유를 간단히 요약해보자면  
macOS 10.14버전 이후에는 OpenSSL헤더와 연결 가능한 라이브러리가 제거돼서 추가로 설치가 필요하다고 한다.

