---
title:  "jekyll-theme-chirpy 테마 적용시 포스팅 화면 CSS 누락 문제 해결하기"
date:   2023-01-05 15:30:17
categories: [GitHub Blog]
tags: [GitHub Blog, Jekyll]
---
> 이 포스팅에는 오류 해결을 위해 씨름했던 제 이야기가 포함돼있으니  
> 오류 해결법만 보시려면 오른쪽 목차를 이용해 요약 부분으로 가주시기 바랍니다😅
{: .prompt-info }

 
# 테마를 변경한 이유
기존에 쓰던 테마가 이쁘긴 하지만 반응형도 잘 안돼있고, 가독성이 좋지 않아서 블로그 테마를 변경하기로 마음먹었다.
Github에 Jekyll이라고 검색한 후 Fork 순으로 정렬해서 테마를 찾았다.  
가장 많은 fork 수가 많은 테마는 [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes) 였지만 내 취향이 아니라서 패스!  
그래서 다음으로 fork수가 많은 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)테마를 선택했다.  
이 테마를 선택한 이유는 튜토리얼도 상세하게 나와있고, 콜아웃, 동영상 임베드 등 추가기능을 많이 지원하기 때문이다.

# Jekyll chirpy 테마 적용  

![img2.png](/assets/img/2023-01-05-jekyll-post-css-ps/img.png)
_Jekyll chirpy 테마를 적용한 모습_

README.md에 나와있는 절차대로 잘 따라하니 정상적으로 테마가 적용됐다.  
~~근데 세팅할때 한번에 성공하면 뭔가 불안하단말이지~~  
역시나 문제가 발생했다. 테마 적용 후 오류는 없는지 확인하려고 포스팅 화면을 들어갔더니,,,,
![img3.png](/assets/img/2023-01-05-jekyll-post-css-ps/img2.png)
_포스팅 페이지_
홈 화면에서는 잘 적용되던 CSS가 어째선지 포스팅 화면에서는 적용되지 않았다.  
개발자모드를 켜서 HTML 코드를 확인해보니 head 부분이 비어있었다.  
지난번 테마를 적용할 때는 전체 CSS가 적용되지 않았는데 이번에는 포스팅만 적용이 안되다니,,  
참고로 전체 CSS가 적용되지 않을 때는 다음과 같은 방법을 쓰면 해결할 수 있다.  
[GitHub Blog Jekyll 테마 적용 시 css 적용 안되는 문제 해결하기](https://milk717.github.io/posts/Jekyll-theme-css-problem/)  
## 포스팅 화면 CSS 오류 해결 
현 상태로 깃헙에 push했을 때 build 오류도 안나고 별다른 에러도 발생하지 않았다.  
하지만 포스트 화면 CSS만 누락돼서 정말 막막했다.  
이것저것 구글링도 해보고 `config.yml`파일과 `pages-deploy.yml.hook`파일을 수도 없이 건드려 봤지만 해결하지 못했다.  
![img4.png](/assets/img/2023-01-05-jekyll-post-css-ps/img4.png)
_문제를 해결하기 위한 발버둥_  
<br/>
도저히 문제를 모르겠어서 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)에서 다시 clone을 받아온 후 로컬에서 실행해봤는데
![img5.png](/assets/img/2023-01-05-jekyll-post-css-ps/img5.png)
_왜 되는거지?!?_
<br/>
너무나도 잘 돌아갔다. 그래서 clone 받아온 프로젝트의 `config.yml`파일을 복붙한 후에 WebStorm을 사용해서 Diff 보기 기능으로 다른 점을 찾아봤다.  
![img6.png](/assets/img/2023-01-05-jekyll-post-css-ps/img6.png)
_Diff 기능을 사용해서 변경된 부분 찾기_
<br/>
`layout: img`라고 돼있는 부분을 `layout: post`라고 변경하니 드디어!!! 정상적으로 동작했다.
![img7.png](/assets/img/2023-01-05-jekyll-post-css-ps/img7.png)
_CSS가 정상적으로 적용된 모습_
<br/>
하지만 이번에는 이미지가 뜨지 않았다. 개발자 모드를 켜서 이미지 경로를 보니 baseURL이 cdn 서버로 돼있었다.
![img8.png](/assets/img/2023-01-05-jekyll-post-css-ps/img8.png)
_잘못된 이미지 경로_

[jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 블로그에 나와있는 튜토리얼을 보니  
`config.yml`파일에서 이미지 cdn server baseUrl을 설정할 수 있다고 나와있었다.[CDN URL 설정하기](https://chirpy.cotes.page/posts/write-a-new-post/#cdn-url)  
그래서 `config.yml`에서 `img_cdn: 'https://chirpy-img.netlify.app'`이 부분을 주석처리 해주었더니  
이미지가 정상적으로 로드되었다.
![img9.png](/assets/img/2023-01-05-jekyll-post-css-ps/img9.png)
_드디어 정상 동작!!_

# 후기
이제서야 정상적으로 블로그를 운영할 수 있게 됐다. ㅜㅜ  
Tistory나 Velog를 사용했다면 이런 시련은 겪지 않아도 됐고, 이런 간단한 문제 때문에 시간을 날린 것도 속상하지만, 시간낭비는 아니라고 생각한다.   
개발 공부를 열심히 하는 것도 중요하지만 해결책이 보이지 않아 답답하고 힘든 상황에서 포기하지 않는 마음을 기르는 것도 개발 공부의 일종이라고 생각한다.

# 요약
## 📌깃허브 블로그 전체에 CSS가 적용되지 않는다면 아래 게시물을 참고!  
[GitHub Blog Jekyll 테마 적용 시 css 적용 안되는 문제 해결하기](https://milk717.github.io/posts/Jekyll-theme-css-problem/)  
## 📌메인화면은 CSS가 적용되는데 포스트 화면에서만 CSS가 적용되지 않는다면  
`_config.yml` 파일에서 아래 부분을 img -> post로 변경할 것!
![img6.png](/assets/img/2023-01-05-jekyll-post-css-ps/img6.png)
## 📌이미지가 로딩되지 않는다면 `_config.yml`에서 `img_cdn: 'https://chirpy-img.netlify.app'`부분을 주석처리 하기  
자세한 내용은 [chirpy 테마의 Demo 블로그에 나와있는 게시글을 참고할 것](https://chirpy.cotes.page/posts/write-a-new-post/#cdn-url)  

