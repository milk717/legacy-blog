---
title:  "GitHub Blog Jekyll 테마 적용 시 css 적용 안되는 문제 해결하기"
date:   2023-01-02 01:13:23
categories: [GitHub Blog]
tags: [GitHub Blog, Jekyll, M1 Mac]
---
_config.yml 파일의 url과 baseurl을 변경해주어야 한다.
jekyll-uno 테마를 기준으로 초기 설정은 다음과 같이 되어있다.

![img.png](/assets/img/2023-01-02-Jekyll-theme-css-problem/img.png)

저 부분을 다음과 같이 변경해주 css가 정상적으로 적용된다.
url: https://[자신의 깃허브 아이디].github.io/
![img_1.png](/assets/img/2023-01-02-Jekyll-theme-css-problem/img_1.png)

테마마다 다른 것 같지만 jekyll-uno 테마 기준으로 baseUrl을 비워두었을 때만 css가 적용되었다.

_config.yml을 변경한 후 레포지토리의 상단 탭 메뉴중
Settings > Pages 에 들어가면 현재 배포 상황을 확인할 수 있다.
![img_2.png](/assets/img/2023-01-02-Jekyll-theme-css-problem/img_2.png)
