---
title: "블로그 구축기 2 - Theme Custormizing"
date: 2019-03-01T12:37:41+09:00
categories: ["code"]
subcategories: ["web"]
tags: ["hugo", "go templates", "theme", "custormizing"]
draft: true
nextp: ""
prevp: "블로그 구축기 1 - Hugo + github.io"
---

이제 자신이 만든 블로그를 감상해보자. 마음에 드는가? 물론 마음에 쏙 들수도 있지만 아무래도 자기가 직접 만든 것이 아니라 남이 만든 테마를 가져다 쓰는 것이기 때문에 맘에 안드는 점이 있을 수도 있다. 따라서 테마를 우리 입맛대로 고치는 custormizing 과정이 필요하다.

# Theme overriding

테마를 수정할 때 가장 중요한 점은 **절대로 `\themes\` 안의 코드를 수정하지 않는 것이다.** 나중에 문제가 생겼을 때 원본이 있어야 복구할 수 있을 뿐만 아니라 테마를 만들 사람에 대한 예의의 의미도 있다. 대신 hugo에서는 theme overriding을 지원한다.

`\themes\<테마 이름>\`안의 구조를 살펴보자. `archetypes`, `layouts`, `static` 등 원래 프로젝트 폴더와 비슷한 구조로 이루어져 있는 것을 볼 수 있을 것이다. 즉, 테마도 하나의 작은 hugo 프로젝트이다. 그렇다면 원래 프로젝트의 `layouts`와 theme 안의 `layouts` 중에서 과연 어떤 것을 반영할까? 예를 들어  `layouts\partials\header.html`을 적용해야 한다고 하면 다음과 같은 알고리즘이 돌아간다.

 1. `\layouts\partials\header.html`이 존재하면 `\layouts\partials\header.html`을 적용한다.
 1. 만약에 존재하지 않으면 `\themes\<테마 이름>\layouts\partials\header.html`을 적용한다.

즉, 어떤 파일을 적용할 때 그 파일이 프로젝트 폴더에 존재하면 테마 폴더보다 프로젝트 폴더의 파일을 먼저 적용한다. 따라서 `\themes\` 안의 코드를 수정하지 않아도 그 파일을 프로젝트 폴더에 복사한 후 수정하면 이러한 theme overriding을 통해 테마를 수정할 수 있다.

우선 프로젝트 폴더 내의 각 요소들이 어떤 역할을 하고 어떤 작용을 해서 블로그를 만들어내는지 알아야 이를 이용해서 수정을 할 수 있으므로 이에 대해 먼저 알아보자.

# 기본 구조

프로잭트를 처음 만들었을 때 여러 폴더들이 같이 생성되었을 것이다. 이 폴더들이 어떤 역할을 하는지 살펴보자.

 - **archetypes** : 이 폴더 안에는 `default.md`가 들어있다. 이 `default.md`를 보면 front matter만 써있는데, 이 front matter가 `$ hugo new <파일 이름>`를 해서 만들었을 때 생기는 문서에 기본으로 생성되는 front matter가 된다.
 - **content** : 블로그에 올릴 글들이 저장되는 곳이다. 뒤에서 설명하겠지만, 이 폴더이 있는 모든 파일/폴더는 블로그에서 페이지로 생성된다.
 - **data** : 이 폴더에는 말 그대로 이런저런 데이터를 저장해둘 수 있다. 따라서 json이나 csv파일 등을 저장해서 가져다 쓸 수 있다. 나의 경우에는 하드코딩 방지용으로 리스트 목록이나 주소같은 것들을 저장해서 가져다 쓴다.
 - **layouts** : 여기에서 블로그에 실제로 적용되는 html들을 코딩한다. 앞으로 제일 많이 건드릴 곳이다.
 - **static** : 여기에 있는 파일들은 빌드 후에도 그대로 옮겨진다. 따라서 주로 css나 이미지 등을 저장해둔다.

# 내부 코딩 기법

대략적인 구조를 파악했으면 이제 코딩을 할 차례이다.

![](/images/blog_construct/html0.png#center50)

개발자들 사이에서 존재하는 밈 중에 "HTML is not a programming language"라는 것이 있다. 그러나 html 속에서도 이중 중괄호(`{{ }}`)를 통해 Go Templates을 코딩할 수 있다. 기존의 코드들을 보면 대충 어떤 식인지 볼 수 있을 것이다. [Hugo Document](https://gohugo.io/documentation/)를 보면 이를 이용해서 어떻게 코딩할 수 있는지 자세하게 나와있다. 그중에 많이 쓰이고 유용한 몇개만 살펴보자.

## Context

개인적으로 생각하기에 가장 잘 이해해야 하는 부분이다. 우선 context를 사용하기 위해서는 항상 `.`으로 시작한다. 여기서 `.`은 나의 현재 context를 의미한다. 이제 맴버변수에 접근하는 것 처럼 이 뒤에 내가 사용할 변수명을 쓰면 된다. 예를 들어 현재 페이지의 제목을 알고 싶다면 `{{ .Title }}`을 하면 된다.

이 context는 반복문에 들어갈 때 그 반복문의 대상으로 바뀐다. 예를 들어 다음 코드를 보자.

```
<ul>
{{ range .Params.tags }}
    <li>
        <a href="/tags/{{ . | urlize }}">{{ . }}</a>
    </li>
{{ end }}
</ul>
```

이 코드는 태그들을 나열하는 코드이다. 처음의 `.`은 기본 페이지의 context였기 때문에 `.Params.tags`를 통해 태그들의 리스트에 접근할 수 있다. 그러나 `{{ range }}` 안에 들어가면 `{{ end }}`를 만날 때까지 `.`은 각각의 태그들로 바뀐다. 따라서 `<li>` 태그 안에서는 `.`으로 접근하면 페이지가 아니라 각 태그들이 나오기 때문에 위와 같이 `<a>` 태그를 써서 리스트를 만들 수 있다.

그렇다면 이렇게 반복문 안에 들어와 있을 때 상위태그에 접근해야하는 상황이 오면 어떻게 해야할까? 예를 들어 위의 코드에서 각 리스트 옆에 원래 페이지의 제목을 넣고 싶다고 하자. 이는 두가지 방법으로 구현할 수 있다.

 - **상위 context를 변수로 정의해준다.** 변수는 변수명 앞에 `$`를 붙여서 쓸 수 있고, 변수 지정은 `:=`을 쓰면 된다. 이렇게 정의한 변수는 `{{ range }}`안에 들어가도 변하지 않는다.

```
{{ $title := .Title }}
<ul>
{{ range .Params.tags }}
    <li>
        <a href="/tags/{{ . | urlize }}">{{ . }}</a> - {{ $title }}
    </li>
{{ end }}
</ul>
```

 -  **전역 context `$.`을 사용한다.** `$.`은 어디에서 사용하던 항상 그 페이지의 최상위 context를 나타낸다. 따라서 이를 이용하면 다음과 같이 구현할 수 있다.

```
<ul>
{{ range .Params.tags }}
    <li>
        <a href="/tags/{{ . | urlize }}">{{ . }}</a> - {{ $.Title }}
    </li>
{{ end }}
</ul>
```

## if, else

평소에 C++이나 Python밖에 안 써봐서 그럴수도 있지만, Go templates의 `if`는 약간 생소했다. 여기서의 `if`는 조건문을 [중위표기법](https://en.wikipedia.org/wiki/Infix_notation)이 아니라 [전위표기법](https://en.wikipedia.org/wiki/Polish_notation)을 쓴다. 예를 들어 `a`와 `b`가 같은 경우를 원한다면, C++을 비롯한 많은 언어에서 이런 형식을 택한다.

```
if(a == b) {
    ...
}
```

그러나 Go templates의 경우 아래와 같이 나타낸다.

```
{{ if eq $a $b }}
    ...
{{ end }}
```

이렇게 `eq` 라는 조건의 연산자를 피연산자 사이에 넣는 것이 아니라 앞으로 빼는 형식을 택한다. 이는 여러 조건문을 합칠 때도 마찬가지이다.

```
{{ if and (or expr1 expr2) expr3) }}
```

만약에 `esle if`를 쓰고 싶다면 다음과 같이 쓰면 된다.

```
{{ if expr1 }}
    ...
{{ else if expr2 }}
    ...
{{ end }}
```

## Where

`where`는 [SQL](https://ko.wikipedia.org/wiki/SQL)에서의 `where`와 같이 범위를 필터링 해주는 역할을 한다. 예를 들어 카테고리가 `web`인 페이지만 뽑고 싶다면

```
{{ range where .Site.Pages "Params.categories" "web" }}
    ...
{{ end }}
```

를 하면 된다. 여기서 더 다양한 조건을 줄 수도 있는데, 이때는 위와 달리 중위표현식 형식을 쓴다. 예를 들어 카테고리가 `web`이 아닌 페이지만 뽑고 싶다면

```
{{ range where .Site.Pages "Params.categories" "!=" "web" }}
    ...
{{ end }}
```

처럼 구현할 수 있다. 여기서 `!=` 부분에는 `>=`, `in`, `intersect` 등이 올 수 있다.

## Index

대부분의 언어에서 배열이나 리스트의 원소에 `[]`을 써서 접근할 수 있었다. 예를 들어 `a`라는 배열에 0번째 원소에 접근하려면 `a[0]`을 쓰면 된다. 그러나 Go templates에서는 이러한 방식을 지원하지 않는다. 대신 `index`라는 문법이 존재한다.

예를 들어 우리가 `a[1][2][3]`에 접근하고 싶다고 하자. 이를 Go templates에서 하려면

```
{{ index $a 1 2 3 }}
```

을 해야한다. 이는 map도 마찬가지이다. 예를 들어 `cities["seoul"]`에 접근하고 싶다면

```
{{ index $cities "seoul" }}
```

처럼 쓰면 된다.

## printf, humanize, urlize, title 

이 함수들은 다 문자열을 다루는 함수들이다. 우선 `printf`부터 살펴보자. 예를 들어 제목 앞에 "This is " 라는 문자열을 붙이고 싶다고 하자. 이는 다음과 같이 쓸 수 있다.

```
{{ printf "This is %s" .Title }}
```

Go templates에서 `|`을 쓰면 `|`의 앞에 있는 변수를 `|`뒤에 있는 함수의 첫번째 인자로 쓰는 효과를 준다. 따라서 아래 코드는 위와 같은 동작을 한다.

```
{{ .Title | printf "This is %s" }}
```

`humanize`는 코드화된(?) 문자열을 사람이 읽기 좋은 문자열로 만들어주는 기능이다. 주로 `urllize`된 문자열을 복호화(?)하는데 사용한다.

```
{{ "this-is-title" | humanize }} => "This is title"
```

이와 반대로 `urlize`는 일반적인 문자열을 url에서 쓸 수 있는 문자열로 바꿔준다.

```
{{ "This is title" | urlize }} => "this-is-title"
```

`title`은 말 그대로 제목에서 쓰기 좋게 각 단어의 첫글자만 대문자로, 나머지는 소문자로 바꿔준다.

```
{{ "This is title" | urlize }} => "This Is Title"
```

# Partial

Go templates에서는 Partial이라는 기능을 제공하는데, 이는 공통적으로 자주 쓰이는 코드를 부분화, 즉 모듈화하여 재사용성을 높이는 기능이다. Partial을 이용하면 크게 두가지 장점이 있다.

 - **코드의 재사용성이 증가한다.** 예를 들어 블로그의 header, navigator, footer같은 것들은 모든 페이지에 들어가는 요소이다. 하지만 그렇다고 모든 html마다 같은 코드를 복사해서 붙여넣으면 비효율적일 뿐만 아니라 나중에 고쳐야 할 부분이 생기면 모든 코드를 찾아가야 하기 때문에 유지보수하기도 힘들다. 하지만 partial 기능을 이용하면 partial 코드를 만들어 둔 후 불러와서 적용하기만 하면 같은 코드를 여러곳에 쓸 필요도 없고, 나중에 수정할때도 partial 코드만 수정하면 자동으로 다 반영된다.
 - **모듈화가 가능하다.** 코드를 기능별로 모듈화해서 관리하는 기법은 이미 많이 쓰이는 방법이다. 위의 경우처럼 여러곳에서 사용하는 경우가 아니더라도, 나중에 유지보수할 때 긴 코드에서 해당하는 부분을 찾는 수고를 덜어줄 뿐만 아니라 공동작업을 하는 경우 충돌이 날 확률도 줄여주기 때문에 이처럼 코드를 모듈화해서 분리시켜두는것을 권장한다.

Partial 코드들은 `\layouts\partials\`안에 들어있다. 예를 들어 내가 header를 partial로 만들어서 관리하고 싶다고 하자. 그러면 `\layouts\partials\header.html`에 코드를 작성한 뒤, 코드를 넣을 곳에

```
{{ partial "header" . }}
```

을 넣으면 된다. 여기서 파일 이름 다음에 나오는 `.`은 해당 partial에서 사용할 context를 의미한다.

여기서 재밌는 점은 이 partial 코드들은 각각 완전히 독립된 개체로서 동작하는 것이 아니라 그냥 코드를 박아넣는다. 예를 들어 내 `\layouts\partials\header.html`를 보면

```
<!DOCTYPE html>
<html>
  <head>
    ...
  </head>
  <body>
    <div class="wrap">
      <div class="navbar">
        ...
      </div>
      <div class="container">
```

이렇게 열어놓은 태그를 다 닫지도 않고 끝난다. 그리고 `\layouts\partials\footer.html`를 보면

```
      </div>
      <div class="blank"></div>
    </div>
    <footer class="footer">
      <div class="share">
        ...
      </div>
    </footer>
  </body>
</html>
```

이렇게 `header.html`에서 연 `html`, `body`, `div` 등을 `footer.html`에서 닫는다. 일반적으로 보면 위험한 코딩일수도 있지만, 모든 코드를 항상 header로 시작해서 footer로 끝난다는 제약 하에서 이렇게 재밌는 코딩을 할 수도 있다.

# Shortcode

Shortcode는 우리가 글을 쓰는 md([마크다운](https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4)) 내에서 html 코드를 넣기 위해 사용한다. 물론 마크다운은 기본적으로 html을 랜더링해준다. 그러나 이렇게 shortcode를 만드는 이유는 위와 같이 재사용성을 높이기 위해서이다. 게다가 shortcode는 Go templates를 쓸 수 있기 때문에 context를 사용할 수 있다.

Shortcode들은 `\layouts\shortcodes\`에 들어있다. 예시를 위해 간단하게 `<h1>` 태그로 텍스트를 출력하는 `h1txt`를 만들어보자. 우선 `\layouts\shortcodes\h1txt.html`을 작성한다.

```
<h1>{{ .Get 0 }}</h1>
```

그 다음에 마크다운 안에서 다음과 같이 쓰면 된다. shortcode는 `{{</* */>}}`로 감싸서 사용한다.

```
{{</* h1txt "asdf" */>}}
```

여기서 `.Get`은 이 shortcode에 들어오는 인자를 뜻한다. 따라서 `.Get 0`는 첫번째 인자를 의미하므로 `asdf`가 되어 `<h1>asdf</h1>`와 같은 역할을 하게 된다.

Shortcode에 인자를 넘겨주는 방법은 두가지가 있다. 첫번째는 위에서 처럼 위치를 통해 순서대로 넣어주고, `.Get index`를 통해 받는 것이다. 두번째 방법은 태그의 속성처럼 이름을 지정하는 것이다. 예를 들어 위의 코드의 경우

```
<h1>{{ .Get "text" }}</h1>
```

```
{{</* h1txt text="asdf" */>}}
```

처럼 쓸 수 있다. 만약에 shortcode에서 저 속성 이름 부분이 이미 `""` 안에 있어야 하는 경우도 있다. 예를 들어 색깔을 받는 경우

```
<p style="color:{{ .Get "text" }}"> </p>
```

처럼 써야할 경우도 있다. 이러면 위와 같이 `""`를 탈출하게 되어 이상하게 동작할 수 있다. 이럴 경우엔 `""` 대신에 아래처럼 해주면 된다.

```
<p style="color:{{ .Get `text` }}"> </p>
```

Hugo에서는 figure, gist, highlight, instagram, youtube 등 기본적으로 내장된 shortcode들을 제공한다. 이들의 자세한 사용법은 [여기](https://gohugo.io/content-management/shortcodes/)에서 보면 된다.

# 각 페이지는 어떻게 생성되는가

이제 코딩하는 방법에 대해 어느정도 알았으면 각 페이지에 대해 알아보면서 사용해보자. 만약 내가 메인페이지를 고치고 싶다면 메인페이지를 주관하는 html이 어디에 있는지 알아야 한다. 이를 위해 각 페이지가 어디로부터 어떻게 생성되는지 알아보자.

## `\content\` 내부 파일들

`\content\` 내부에 있는 각 md 파일들은 각각 하나의 글이므로 각각 하나씩 페이지가 생성된다. 이때 페이지의 주소는 `\content\` 아래의 경로이다. 예를 들어 내 파일이 `\content\post\blog-construct-2.md`이고 내 블로그 기본주소가 [https://ialy1595.github.io](https://ialy1595.github.io)라면 저 글의 주소는 [https://ialy1595.github.io/post/blog-construct-2](https://ialy1595.github.io/post/blog-construct-2)가 된다.

이 md 파일들을 html로 만들어서 페이지가 되도록 해주는 것이 `\layouts\_default\single.html`이다. 이 context에서 쓸 수 있는 변수들은 다음과 같다.

 - **.Title** : 글의 제목
 - **.Content** : 글의 본문을 마크다운으로 랜더링 한 내용
 - **.TableOfContents** : 목차와 같이 #, ## 등으로 지정한 제목들을 나열해준다.
 - **.Params** : 글의 frontmatter을 가져다 쓸 수 있다. 예를 들어 `.Params.tags`는 이 글의 태그 리스트를 나타낸다.

## `\content\` 내부 폴더들

## Taxonomies

# 내가 한 ~~삽질~~작업들