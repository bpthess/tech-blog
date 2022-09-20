---
layout: post
title: Jekyll & Liquid 언어 사용기
categories: jekyll,liquid,markdown
author: tyLee
date: "2022-08-18 09:00:00 +0900"
---

-- ruby를 처음 접해보더라도 프로그래밍 언어를 조금 다뤄봤다면 어렵지 않게 적용할 수 있다고는 하지만 필자는 그렇지 않았다.  
그래도 Jekyll과 친해지기 위해 Liquid 언어를 파악해보기로 했다.

<br>
<br>
## 유지 보수 관련

{% raw %}

> Static Website이므로 Database 등은 사용할 수 없으나 정적 웹 방식을 채택함으로써 빠른 속도를 확보하고 백엔드 코드에 대한 부담감 없앴다는 장점에서 Jekyll을 택하였다.

로컬 환경에서 Jelyll을 사용하여 웹사이트를 작성/테스트하고 GitHub repository에 웹리소스를 push하면 매우 간단히 Website를 Hosting할 수 있는 구조이다.  
필자는 jekyll theme를 사용해서 기술블로그를 만들었다.

기술 블로그 장점

- Less Complexity(복잡성 감소)
- Speed(속도)
- Scalability(확장성)
- easy Maintenance(쉬운 유지 보수)

<br>
## 구조

-- 우선 폴더 구조를 파악해보기로 했다.

```javascript
_data (콘텐츠 타이틀 분리)
 └ format.yml
    ...
_drafts (시간 정보가 관련 초안)
 └ (date.naming).markdown
    ...
_includes (중복 사용 html)
 └ header.html
    ...
_layouts (뼈대를 구성하는 html)
 └ home.html
 └ post.html
 _posts (보여질 내용)
 └ (date.naming).markdown
    ...
_productions (보여질 html)
 └ home.html
 └ post.html
_sass (scss)
 └ (titleName).markdown
    ...

```

-- 하지만 구조 파악은 시작에 불과했다.

<br>
## Jekyll은 Liquid 언어를 사용

-- Jekyll은 생소한 Liquid 언어로 데이터를 전달받고 있다.  
-- 전체적인 폴더구조는 이해 했으나 markdown에서 Liquid 문법으로 데이터를 주고 받는 포맷으로 되어 있어서 다소 어떤 방식으로 접근해야할지 고민했다.  
-- liquid 문법을 익히면서 기억에 남았던 문법 요약

```markdown
{　% raw %　} 와 {　% endraw %　} // Liquid 태그를 그대로 보여줄 수 있게 Escape하는 방법이다.  
{　% raw %　} { { page.(title) } } {　% endraw %　} // Object로 해당 대상을 불러온다.  
assign // 변수를 새로 만들고 할당한다.  
include // 디렉토리에 있는 파일을 불러오는 기능이다.  
for문과, 반복할 인자가 없을때 실행할 else 그리고 C계열 언어에서 많이 봤던 continue와 break까지 지원한다.
```

<br>

## 커스터마이징 & 렌더링

구조를 만든 뒤 남은 작업은 본격적으로 커스터마이징하는 일이었다.  
-- HTML, CSS는 논외로 하고 Jekyll 블로그를 만들다보면 Liquid 를 사용할 일이 생각보다 많다. Liquid는 위에 소개한 바와 같이 Shopify에서 개발한 Ruby 기반의 Template Language이다. 처음 보는 문법이라 생각보다 쉽지는 않았지만, 문법이 오히려 직관적이여서 이해가 안되지는 않았다.

```markdown
// Relative URL
/assets/style.css
/my-baseurl/assets/style.css

https://bpthess.github.io/assets/style.css
http://example.com/my-baseurl/assets/style.css
```

-- 각각 자신이 레이아웃으로 사용할 파일들에서 변수로 사용될 수 있다.  
-- 정의된 여러 가지 변수 중 layout 변수를 보면 post.html이라고 적혀있다. 즉, 이 마크다운 파일은 post.html 파일을 레이아웃으로 사용하려고 한다. 아까 위에서 언급한 post.html의 {{ content }}에 포스트가 올 것이라는 예측이 정확하게 맞았다.

<br>
## 포스팅

-- 포스팅을 하려면, jekyll의 디렉토리 구조에서 \_post디렉토리에 2022-08-18-first-posting.md와 같은 형태의 파일이름으로 아래 같이 작성할 수 있다.

```markdown
---
layout: post
title: Jekyll
category: liquid
---
```

{% endraw %}

-- 블로그 글을 마크다운 파일로 작성한 후 해당 디렉터리 코드에 layout: post.html 이라고 적은 후 post.html을 이용하여 렌더링해주면 된다고 한다.
