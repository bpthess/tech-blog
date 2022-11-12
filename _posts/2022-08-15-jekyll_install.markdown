---
layout: post
title: "Jekyll과 GitHub Pages를 활용한 기술 블로그 만들기"
categories: jekyll,liquid,markdown
author: tyLee
date: "2022-08-15 09:00:00 +0900"
---

-- 기술블로그와 릴리즈 노트를 운영하는 여러 방식들 중에 Jekyll + GitHub Page로도 구현 할 수 있다는 걸 알게되었다. Jekyll로 쉽고 빠르게 만들 수 있다고 하지만, 과정은 왜 이리도 어려운 걸까? 모든 것이 새로웠던 Jekyll로 기술블로그를 만들어 봤는데 쉽게 되지는 않았던 과정을 적어보았다.

<br>
<br>
## 과정 중 에러
-- Jekyll을 이용하여 설치하던 중 오류가 발생하였다.

에러코드: <code>install MSYS2 and MINGW develepment toolchain. Installation failed: pacman failed</code>  
<img src="{{'/assets/img/error_msys2.png' | relative_url}}" width="50%" style="margin: 0 auto;">

MSYS2 설치가 안되어 있거나 릴리즈 업데이트 되서 오류가 나는 걸로 나오는데 나는 전자에 속한다.  
 아래 링크에서 재설치 후 해결하였다.

> <https://www.reddit.com/r/ruby/comments/e1e1kh/not_able_to_install_rubydevkit_2651_64_on_windows/>

<br>
<br>
에러코드: <code>'require': cannot load such file -- webrick (LoadError)</code>  
<img src="{{'/assets/img/error_webrick.png' || relative_url}}" width="60%" style="margin: 0 auto;">
이런 경우 bundle add webrick으로 webrick을 추가해 주고 다시 실행하면 된다.  
이유는 ruby 3.0.0부터 webrick이 기본으로 포함된 gem에서 빠졌기 때문이다.

> <https://junho85.pe.kr/1850/>

<br>
<br>
에러코드: <code>empty or else try again with with `--force` to proceed and overwrite any files.</code>   
<img src="{{'/assets/img/error_force.png' || relative_url}}" width="60%" style="margin: 0 auto;">
```html
jekyll new $REPOSITORY_NAME.github.io -f
```
해결 방법은 위 내용을 입력하면 된다. <code>-f</code>flag는 --force의 약자로 해당 디렉토리가 비어 있지 않기 때문에 주는 옵션이다. 만약 생략한다면 위와 같은 에러가 발생한다.   
> <https://cjy-tech.github.io/make-blog-with-jekyll-and-github_pages/>

<br>
<br>
<img src="{{'/assets/img/clear.png' || relative_url}}" width="60%" style="margin: 0 auto;">

긴 삽질 끝에 Jekyll 블로그를 로컬 화면에 띄울 수 있게 되었다.

<br>
## 회고
-- 기술 블로그를 만든 후 포스팅하기까지 "글쓰기" 라는 행위를 통해 그동안 머리속에만 존재하는 어떤 것들을 정리 하고 시각화 할 수 있기 때문에, 내가 아는것과 모르는 것을 분명하게 구분 할 수 있고 그로인해 앞으로 해야 할 것이 무언인지 명확하게 나타낼 수 있게 됨으로 써 포스팅의 장점을 몸소 체험할 수 있는 좋은 기회였다.

<br>
<br>
 레퍼런스   
 > <https://zeddios.tistory.com/1222>   
 > <https://zeddios.tistory.com/1223?category=682196>   
 > <https://velog.io/@minji-o-j/jekyll-%EC%98%A4%EB%A5%98-%ED%95%B4%EA%B2%B0>
