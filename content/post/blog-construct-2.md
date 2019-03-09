---
title: "블로그 구축기 2 - Theme Custormizing"
date: 2019-03-01T12:37:41+09:00
categories: ["code"]
subcategories: ["web"]
tags: ["hugo", "go templates", "theme", "custormizing"]
references: [
  "https://github.com/lubang/hugo-hello-programmer-theme",
  "https://gohugo.io/documentation",
  "https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3",
  "https://yihui.name/en/2018/07/latex-math-markdown/"
]
draft: true
nextp: ""
prevp: "블로그 구축기 1 - Hugo + github.io"
---

이제 자신이 만든 블로그를 감상해보자. 마음에 드는가? 물론 마음에 쏙 들수도 있지만 아무래도 자기가 직접 만든 것이 아니라 남이 만든 테마를 가져다 쓰는 것이기 때문에 맘에 안드는 점이 있을 수도 있다. 따라서 테마를 우리 입맛대로 고치는 custormizing 과정이 필요하다.

# Theme overriding

테마를 수정할 때 가장 중요한 점은 **절대로 `\themes\` 안의 코드를 수정하지 않는 것이다.** 나중에 문제가 생겼을 때 원본이 있어야 복구할 수 있을 뿐만 아니라 테마를 만들 사람에 대한 예의의 의미도 있다. 대신 hugo에서는 theme overriding을 지원한다.

`\themes\<테마 이름>\`안의 구조를 살펴보자. `archetypes`, `layouts`, `static` 등 원래 프로젝트 폴더와 비슷한 구조로 이루어져 있는 것을 볼 수 있을 것이다. 즉, 테마도 하나의 작은 hugo 프로젝트이다. 그렇다면 원래 프로젝트의 `layouts`와 `\theme\` 안의 `layouts` 중에서 과연 어떤 것을 반영할까? 예를 들어  `layouts\partials\header.html`을 적용해야 한다고 하면 다음과 같은 알고리즘이 돌아간다.

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

처럼 쓸 수 있다. 만약에 shortcode에서 저 속성 이름 부분이 이미 `" "` 안에 있어야 하는 경우도 있다. 예를 들어 색깔을 받는 경우

```
<p style="color:{{ .Get "text" }}"> </p>
```

처럼 써야할 경우도 있다. 이러면 위와 같이 `" "`를 탈출하게 되어 이상하게 동작할 수 있다. 이럴 경우엔 `" "` 대신에 아래처럼 `` ` ` ``로 감싸주면 된다.

```
<p style="color:{{ .Get `text` }}"> </p>
```

Hugo에서는 figure, gist, highlight, instagram, youtube 등 기본적으로 내장된 shortcode들을 제공한다. 이들의 자세한 사용법은 [여기](https://gohugo.io/content-management/shortcodes/)에서 보면 된다.

# 각 페이지는 어떻게 생성되는가

이제 코딩하는 방법에 대해 어느정도 알았으면 각 페이지에 대해 알아보면서 사용해보자. 만약 내가 메인페이지를 고치고 싶다면 메인페이지를 주관하는 html이 어디에 있는지 알아야 한다. 이를 위해 각 페이지가 어디로부터 어떻게 생성되는지 알아보자.

## `\content\` 내부 파일들

`\content\` 내부에 있는 각 md 파일들은 각각 하나의 글이므로 각각 하나씩 페이지가 생성된다. 이때 페이지의 주소는 `\content\` 아래의 경로이다. 예를 들어 내 파일이 `\content\post\blog-construct-2.md`이고 내 블로그 `baseURL`이 [https://ialy1595.github.io](https://ialy1595.github.io)라면 저 글의 주소는 [https://ialy1595.github.io/post/blog-construct-2](https://ialy1595.github.io/post/blog-construct-2)가 된다.

이 md 파일들을 html로 만들어서 페이지가 되도록 해주는 것이 `\layouts\_default\single.html`이다. 이 context에서 쓸 수 있는 변수들은 다음과 같다.

 - **.Title** : 글의 제목
 - **.Content** : 글의 본문을 마크다운으로 랜더링 한 내용
 - **.TableOfContents** : 목차와 같이 #, ## 등으로 지정한 제목들을 나열해준다.
 - **.Params** : 글의 frontmatter을 가져다 쓸 수 있다. 예를 들어 `.Params.tags`는 이 글의 태그 리스트를 나타낸다.

## `\content\` 내부 폴더들

예를 들어 `\content\` 내부 구조가 다음과 같다고 가정해보자.

```
content
  ├ post
  │  ├ post1.md
  │  ├ post2.md
  │  └ post3.md
  ├ project
  │  ├ project1.md
  │  └ project2.md
```

그렇다면 `<baseURL>/post`의 주소로 페이지가 생기고, 이 페이지에는 `post1`, `post2`, `post3`들의 리스트가 나온다. 마찬가지로 `<baseURL>/project` 주소에는 `project1`, `project2`의 리스트가 생긴다. 이 리스트들은 `\layouts\_default\list.html`에 의해 생성된다.

`\layouts\_default\list.html`의 context를 보면 `.Pages`라는 변수가 있는데, 이 변수는 해당 상황에서 들고있어야 할 페이지들을 담고있다. 예를 들어 `<baseURL>/post`에 `list.html`이 적용되는 경우 `.Pages`는 `post1`, `post2`, `post3`의 페이지 변수를 가지고 있다.

페이지 변수에서 쓸만한 변수들은 다음과 같다.

 - **.Title** : 글의 제목
 - **.Permalink** : 해당 글의 주소
 - **.Summary** : 해당 글의 요약(앞부분 일부). Summary 길이는 `config.toml`에서 `summaryLength = 30`과 같이 조절할 수 있다.
 - **.Data.Format** : 글을 작성한 날짜를 지정한 포멧으로 보여준다. 포멧 종류는 [여기](https://gohugo.io/functions/format/#hugo-date-and-time-templating-reference)에 나와있다.

 예를 들어 글의 제목과 작성한 날과 간단 요약을 보여주고, 클릭하면 해당 글로 넘어가는 목록을 만들고 싶다면 다음과 같이 하면 된다.

```
<ul>
{{ range .Pages }}
  <li>
    <div>
      <a href="{{ .Permalink }}">{{ .Title }} | {{ .Date.Format "Mon Jan 2 2006" }}</a>
      <div>
        {{ .Summary }}
      </div>
    <div>
  </li>
{{ end }}
</ul>
```

하지만 이 목록은 `\content\` 안의 최상위 폴더에만 해당된다. 예를 들어 `\content\` 내부 구조가

```
content
  ├ post
  │  ├ foo
  │  │  ├ foo1.md
  │  │  └ foo2.md
  │  ├ post1.md
  │  ├ post2.md
  │  └ post3.md
```

처럼 생겼다고 해보자. 이 때 `<baseURL>/post/foo`에 접속하면 `foo1`, `foo2`의 리스트가 안보이고 404 페이지가 뜰 것이다. 만약에 최상위 폴더가 아닌 폴더에도 리스트를 만들고 싶다면 해당 폴더에 `_index.md`를 만들면 된다. 예를 들어 `foo`의 리스트를 만들고 싶다면 `\content\post\foo\_index.md`를 만들면 된다.

이렇게 만든 `_index.html`도 md이기 때문에 frontmatter와 내용을 가진다. `\layouts\_default\list.html`에서 `.Title`을 쓰면 `_index.html`의 제목이 나오고, `.Content`를 쓰면 `_index.html`의 내용이 나온다. 따라서 `_index.html`을 이용해서 리스트 페이지를 상황에 맞게 꾸밀 수 있다. 이는 물론 최상위 폴더에도 `_index.html`을 만들어서 적용할 수 있다.

## Taxonomies

Go templates에서는 기본적으로 category와 tag라는 두개의 taxonomy, 즉 분류를 제공한다. `<baseURL>/categories`에 들어가면 내가 가지고 있는 카테고리들의 목록이 나온다. 마찬가지로 `<baseURL>/tags`에 들어가면 태그들의 목록이 나온다. 이 목록들은 내가 따로 알려주지 않아도 글을 만들 때 frontmatter에 적어둔 카테고리와 태그들로 자동으로 생성된다.

카테고리 목록의 화면은 `\layouts\categories\terms.html`에 의해 생성된다. 물론 태그 목록들은 `\layouts\tags\terms.html`에서 생성된다. 위의 `list.html`에서는 페이지들의 리스트가 `.Pages`에 담겨있었지만 `terms.html`에서는 `.Data.Pages`에 담겨있다. 따라서 해당 목록들은 다음과 같이 구현할 수 있다.

```
<ul>
{{ range .Data.Pages}}
  <li>  
    <a href="{{ .Permalink }}">{{ .Title }}</a>
  </li>
{{ end }}
</ul>
```

여기서 `.Permalink`는 해당 카테고리 항목의 리스트에 연결된다. 예를 들어 `<baseURL>/categories/web`에는 카테고리가 `web`인 글들의 리스트가 나온다. 이 리스트 페이지는 `\layouts\_default\list.html`을 따른다.

## 기타 페이지들

위의 분류에 속하지 않는 특수 페이지가 두개가 더 있다. 이 두 페이지는 모두 `\layouts`에 들어있다.

 - **main page** : 기본 URL에서 보여지는 페이지로, `\layouts\index.html`에 의해 생성된다.
 - **404 page** : 잘못된 URL로 접속할 경우 보여지는 페이지로, `\layouts\404.html`에 의해 생성된다.

# 내가 한 ~~삽질~~작업들

사실 코딩이란 것은 이런 이론적인 설명보다도 직접 해보면서 시행착오를 겪고 오류를 해결해나가는 과정에서 익혀진다. 그런 의미애서 내가 한 작업들을 정리해봤다.

## Logo, Favicon, Color Palette

네이버 하면 초록색, 페이스북 하면 파란색, 카카오 하면 노란색이 떠오른다. 또한 대부분의 기업들이 고유의 색은 없더라도 고유의 로고는 가지고 있다. 물론 내 블로그가 기업은 아니지만, 나만의 블로그를 만드는 김에 고유의 색과 로고를 만들어보고 싶었다.

로고를 만드는 작업을 하기 전에 내가 이 블로그에 어떤 글들을 쓸지 먼저 생각해 보았다. 우선 전공 관련 글은 당연히 들어갈 것이다. 전공분야 외에 내가 또 좋아하면서 깊게 공부해본 분야는 칵테일이다. 완전 전문가 수준은 아닐지라도 조주기능사를 따면서 3년정도 세미나를 할 정도는 공부를 했고, 꽤 성공적인 창작칵테일도 많이 만들어봤다. 따라서 내 창작칵테일 이야기나 바 후기같은 글을 쓰는 것도 재밌겠다는 생각을 했다.

따라서 **코딩**과 **칵테일**을 모두 나타낼 수 있는 로고를 만들기로 결심했다. 이렇게 두가지 정보를 담고 있지만 로고 자체는 간단해야한다. 내가 미니멀리즘을 지향하기도 하지만 이 로고를 그대로 favicon으로 만들고 싶었기 때문이다.

![](/images/blog_construct/favicon0.PNG#center30)

favicon이란 탭부분에서 왼쪽에 표시된 작은 아이콘을 뜻한다. favicon을 지정하기 위해서는 헤더 부분에

```
<link rel="icon" href="{{ "/favicon.ico" | relURL }}" type="image/x-icon">
```

을 추가하고 `\static\favicon.ico`로 내가 지정할 favicon파일을 저장하면 된다. 이 때 favicon 크기는 16 X 16 이다. 실제로 보여지는 크기가 매우 작기 때문에 어차피 많은 정보를 담지 못한다. 이러한 favicon에도 내가 만든 로고를 그대로 쓰기 위해서는 기본 로고도 간단해질 수 밖에 없었다. 이러한 조건 하에 아래와 같은 로고를 만들 수 있었다.

![](/images/blog_construct/logo0.png#center50)

이제 블로그 테마 색을 정할 차례이다. 기본 색은 내가 좋아하는 보라색 계열로 가닥을 잡았다. 일반적으로 색을 표현할 때 RGB 방식이 많이 쓰인다. 그러나 색을 찾고 조금씩 바꿔보는 작업을 하기에는 [HSL 혹은 HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) 방식이 더 유용하다. 만약에 좀 더 밝거나 어두운 색을 원한다면 밝기(L 혹은 V)값을 조절하면 되고, 좀 더 뚜렷하거나 탁한 색을 원한다면 채도(S)값을 조절하면 된다. 이 과정에서 [W3Schools의 colors picker](https://www.w3schools.com/colors/colors_picker.asp)가 HSL의 각각의 요소를 바꾼 색을 쉽게 볼 수 있어서 색을 선정하는데에 많은 도움을 받았다. 이를 통해 기본색은 <font color="#8C8CD9">■</font>#8C8CD9, 진한 색은 <font color="#6666FF">■</font>#6666FF로 정했다.

그 다음은 배경색을 정해야 할 차례였다. 기본 바탕의 경우에는 그냥 무난하고 깔끔하게 흰색을 사용하기로 했다. 그러나 검은색은 대체해야할 필요가 있었다. 위에서 정한 보라색이 워낙 다채로운 색이라 완전히 무채색인 검은색을 쓰면 약간 안어울리는 느낌을 줄 수 있기 때문이다. 따라서 어두운 계열이면서 위의 보라색과 블로그의 느낌에 맞는 색을 찾은 것이 <font color="#36414A">■</font>#36414A였다. 만약 더 어두운 색이 필요하면 <font color="#151A1E">■</font>#151A1E를 쓰기로 했다. 따래서 내가 사용한 color palette는 다음과 같다.

![](/images/blog_construct/palette0.png#center75)

나의 경우에는 이렇게 직접 color palette를 만들었지만, 직접 만들기는 귀찮은데 이쁜 color palette를 쓰고 싶다면 [adobe color](https://color.adobe.com/ko/explore/?filter=most-popular&time=month)나 [colourlovers](https://www.colourlovers.com/palettes/search)를 참고하면 많은 color palette들이 나와있다.

## Hover line effect

나만의 시그니쳐한 블로그를 만드는 것이 목적이기 때문에 위에서처럼 로고나 color palette는 직접 만들었지만, 내가 디자인을 좋아하기는 해도 전문 디자이너는 아니기 때문에 다른 사람들의 의견이나 조언을 듣는 것은 매우 중요하다. 이런 조언을 얻기 좋은 사이트를 하나 추천하자면 [Medim](https://medium.com/)이다. Medium은 블로그 서비스중 하나인데, 디자인이 가독성이 좋고 글의 퀄리티가 전제척으로 좋은 편이라 이런저런 조언을 얻기 좋다.

그 중 [7 Practical Tips for Cheating at Design](https://medium.com/refactoring-ui/7-practical-tips-for-cheating-at-design-40c736799886)라는 글이 있었는데 여기서 <6. Use accent borders to add color to a bland design>에서 hover line effect에 대한 영감을 얻어서 위에 navigator나 카테고리에서 해당 항목에 hover effect로 아래나 왼쪽에 선이 생기도록 했다. 이는 css에서 다음과 같이 구현할 수 있다.

```
.navbar li {
  padding: 12px 8px 12px 8px;
}

.navbar li:hover {
  border-bottom: 4px solid #6666FF;
  padding-bottom: 8px;
}
```

만약 단순히 `border-bottom: 4px solid #6666FF;`만 추가하면 아래에 선이 추가로 생기면서 공간도 추가로 할당되는 효과가 되어 부자연스럽다. 따라서 4px의 `border-bottom`을 추가하면서 동시에 12px였던 `padding-bottom`을 4px 줄인 8px로 바꿔주면 좀 더 자연스러운 효과를 낼 수 있다.

## Subcategory hierarchy

Hugo에서는 기본적으로 category라는 taxonomy를 제공한다. 나는 여기서 더 나아가서 subcategory를 통해 계층적인 분류를 하고싶었다.

우선 카테고리를 추가하는 것은 어렵지 않다. 우선 `\config.toml`에 

```
[taxonomies]
  category = "categories"
  tag = "tags"
  subcategory = "subcategories"
```

를 추가한다. 여기서 주의할 점은 `category`와 `tag`는 기본적으로 제공되는 항목이고 `subcategory`만 추가할 뿐이지만 세개 모두 서줘야 한다는 것이다. 만약에 `category`나 `tag`를 써주지 않으면 taxonomy에서 제거된다. 이렇게 하면 `subcategory`라는 새로운 taxonomy가 생긴다. 이제 글을 쓸 때 front matter에서

```
subcategories = ["web"]
```

처럼 써주면 된다. 이를 쉽게 하려면 `\archetypes\default.md`의 front matter에 `subcategories = []`를 추가하면 앞으로 글을 생성할 때 자동으로 `subcategories` 항목이 생긴다.

이렇게 `subcategory`를 만들기는 했지만 아직 `category`와의 연관성이 없다. 즉, 다음 문제를 해결해야 했다.

 - 각 `category`에 어떤 `subcategory`가 속하는지 코드상으로 어떻게 구현할 것인가

 이 문제를 해결하기 위해 [Data template](https://gohugo.io/templates/data-templates/#readout)를 사용했다.

 우선 subcategory 정보를 `data\subclist.toml`에 저장하기로 했다. 예를 들어 이 문서에

 ```
 code = ["system-architecture", "web"]
 ```

 라고 쓰여있으면 `code`라는 `category` 안에는 `system-architecture`와 `web`이라는 `subcategory`가 있다는 뜻이다.

 이제 이 데이터들은 `.Site.Data.subclist` context를 통해 불러올 수 있다. 예를 들어 위의 `code` 리스트를 가져오고 싶다면 `index .Site.Data.subclist "code"`를 하면 된다. 이를 통해 계층적으로 구현한 category list page는 다음과 같이 구현했다.

 ```
 <div class="categories">
  <ul>
    {{ $baseurl := .Site.BaseURL }}
    {{ $data := .Data }}
	{{ $site := .Site }}
    {{ range $key,$value := .Data.Terms.Alphabetical }}
    <li>
      <a href="{{ $baseurl }}{{ $data.Plural }}/{{ $value.Name | urlize }}">
        <i class="fa fa-angle-right icon" aria-hidden="true"></i>
        {{ humanize $value.Name }}
        <div class="count">{{ $value.Count }} posts</div>
      </a>
    </li>
	<div class="subcategories">
	  {{ $list := ( index $site.Data.subclist $value.Name ) }}
	  <ul>
	  {{ range $key, $value := $site.Taxonomies.subcategories.Alphabetical }}
	  {{ if in $list $value.Name }}
		<li>
		  <a href="{{ $baseurl }}subcategories/{{ $value.Name | urlize }}">
			<i class="fa fa-angle-right icon" aria-hidden="true"></i>
			{{ humanize $value.Name }}
			<div class="count">{{ $value.Count }} posts</div>
		  </a>
		</li>
	  {{ end }}
	  {{ end }}
	  </ul>
	</div>
    {{ end }}
  </ul>  
</div>
 ```

## Mathjax

글을 쓰다보면 중간중간에 수식을 넣어야 할 필요가 생긴다. 웹에서 수식을 표현하기 위해 [mathjax](https://www.mathjax.org/)를 많이 사용한다. 그러나 여기서 문제점은 내가 글을 쓰는 파일은 html이나 js같은 웹용이 아니라 md라는 것이다. 사실 이 문제는 hugo 뿐만 아니라 jekyll을 비롯한 많은 곳에서도 md로 글을 쓰기 때문에 이를 해결하기 위해 이미 많은 사람들이 고민했고, 좋은 솔루션이 나와있다.

결론적인 해결법부터 보자. 우선 헤더에 다음을 추가한다.

```
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
  MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$']],
    processEscapes: true,
    processEnvironments: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
    TeX: { equationNumbers: { autoNumber: "AMS" },
         extensions: ["AMSmath.js", "AMSsymbols.js"] }
  }
  });
  MathJax.Hub.Queue(function() {
    // Fix <code> tags after MathJax finishes running. This is a
    // hack to overcome a shortcoming of Markdown. Discussion at
    // https://github.com/mojombo/jekyll/issues/199
    var all = MathJax.Hub.getAllJax(), i;
    for(i = 0; i < all.length; i += 1) {
        all[i].SourceElement().parentNode.className += ' has-jax';
    }
  });
</script> 
```

나는 이를 `\layouts\partials\mathjax-header.html`에 partial로 따로 만들어서 관리했다.

그리고 css에 다음을 추가한다.

```
code.has-jax {
  font: inherit;
  font-size: 100%;
  background: inherit;
  border: inherit;
  color: #000000;
}
```

이제 md에 글을 쓸 때
```
`$ x = {-b \pm \sqrt{b^2-4ac} \over 2a} $`
```

처럼 코드 안에 `` `\$ \$` ``로 감싸서 쓰면

`$
x = {-b \pm \sqrt{b^2-4ac} \over 2a}
$`

처럼 수식이 랜더링된다.

사실 이 방법은 검색하면 널리고 널릴 정도로 많은 글에서 소개한다. 그럼에도 불구하고 내가 또 이 글에 쓰는 이유는 이것이 작동하는 원리에 대해 쓴 글이 거의 없었기 때문이다. 물론 작동만 하면 되는 사람도 있겠지만 나는 작동하는 원리를 아는 것도 중요하게 생각하기 때문에 열심히 찾아봤고, 이를 공유하려 한다.

md로 작성한 코드를 실제 그러럼 보이기 위해서는 기본적으로 랜더링 과정이 필요하다. 여기서 문제는 우리가 쓴 수식이 이 마크다운의 랜더링에 들어가버리면 더이상 손을 쓸 방법이 없어진다. 여기서 해답은 `` ` ``로 감싸서 표현하는 코드부분이다. 이 부분은 랜더링 되기 전에 `<code>` 태그로 감싸져서 먼저 처리되기 때문에 이를 이용하면 mathjax를 따로 처리할 수 있다.

먼저 위의 코드에서 `tex2jax` 부분이 `<code>`로 감싸진 부분에서 `$` 등으로 감싸진 부분이 있으면 수식으로 랜더링을 하라는 부분이다. 이를 통해 수식을 랜더링할 순 있지만 아직 한가지 문제가 남아있다. 우리가 마크다운 랜더링에서 빠져나오기 위해 `<code>` 태그를 사용했기 때문에 배경이 회색이 되는 등 자연스럽게 스타일도 `<code>`부분을 따라가게 된다. 따라서 자연스럽게 글을 쓰기 위해 이 부분을 처리해줘야 한다.

이를 위해 `has-jax`라는 태그를 사용한다. 스크립트의 아래 부분이 이러한 수식 부분에 `has-jax`를 붙이는 역할을 한다. 그러면 이제 태그가 `<code>`가 아니라 `<code.has-jax>`가 된다. 이제 css에서 `<code.has-jax>` 일 경우의 스타일을 원래 본문의 경우와 동일하게 설정해주면 자연스럽게 본문에 수식이 들어가는 효과를낼 수 있다.

