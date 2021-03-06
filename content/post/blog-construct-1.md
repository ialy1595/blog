---
title: "블로그 구축기 1 (Hugo + github.io)"
date: 2019-02-22T20:07:41+09:00
categories: ["code"]
subcategories: ["web"]
tags: ["blog", "hugo", "github.io", "github page", "static site", "static site generator"]
references: [
    "https://ryan-han.com/post/2018/etc/creating_static_blog/",
    "https://blog.lulab.net/install-hugo-and-configure-for-your-blog/",
    "https://tadakichi.tistory.com/188",
    "https://www.staticgen.com/",
    "https://gohugo.io/documentation/"
]
nextp: "블로그 구축기 2 (Hugo Theme Customizing)"
prevp: ""
---

예전에는 문서화 작업에 그렇게 큰 신경을 두지 않았다. 프로젝트를 진행할 때 이것저것 삽질해보고 구글링하면서 문제를 해결하고선 이를 따로 정리해 두지 않고 완성해서 잘 동작시키는 것에만 의의를 두었다. 그러다보니 예전에 부딪혔던 문제를 다시 만나도 어떻게 해결했는지 기억이 안 나서 결국 같은 작업을 반복하는 경우가 생겼다. 이러한 일이 반복되자 내 작업 과정을 문서화해두는 작업이 필요함을 느꼈다. 

그렇게 생각해보면 내가 그동안 구글링하면서 참고했던 블로그의 글들도 누군가의 작업 기록이었고, 나는 그들의 글을 보면서 내가 좀 덜 삽질할 수 있게 많은 도움을 받았다. 따라서 내가 작업하면서 겪었던 것들을 잘 정리해두면 나중에 누군가에게 도움이 되지 않을까 라는 생각도 블로그 구축을 다짐하는 데에 한 몫을 했다.

그러다보니 작업 경험 외에도 써야할 글들이 몇 개 더 생각났다. 지금까지 수많은 전공을 들어왔지만 1~2년 정도 지나고나니 많은 내용을 까먹게 된다. 큰 그림으로 어떤 개념이 있었는지 정도는 기억이 나지만 결국 정확한 내용은 기억이 안 나서 다시 예전에 배웠던 자료를 들춰보는 경우도 많았다. 그래서 이번 기회에 글을 쓰는 김에 배웠던 전공 내용도 정리해보면 어떨까 하는 생각을 했다.

블로그를 만들려면 크게 두 가지가 필요하다. 하나는 내 블로그 컨텐츠들을 담은 소스이고, 나머지 하나는 이 소스를 배포해줄 서버이다. 우선 블로그 소스를 만드는 것에는 쉽고 빠르게 만들 수 있다는 static site generator를 쓰기로 했고, 그 중에서도 hugo라는 static site generator를 써보기로 했다. 서버로는 github에서 제공하는 github page 기능을 쓰기로 했다. 개발자들에게 github은 많이 친숙한 이름이지만 hugo는 다소 생소할 수도 있다. 따라서 hugo에 대해 먼저 알아보자.

# Hugo?

Hugo는 jekyll, hexo 등과 같이 웹사이트를 쉽게 만들 수 있게 해주는 static site generator중 하나이다. Hugo가 뭔지 알아보기 전에 우선 static site, 그리고 static site generator가 뭔지 먼저 알아보자.

## Static site

웹사이트를 분류하자면 크게 두 종류로 나눌 수 있다. 바로 **dynamic site**와 **static site**이다.

Dynamic site는 그때그때 동적으로 html을 생성해서 보여주는 사이트다. 즉, dynamic site의 화면은 같은 주소라도 지속적으로 변할 수 있다. 이에 비해 static site는 고정된 html을 그냥 뿌려주는 사이트이다. 따라서 static site를 쓴다면 언제 들어간다고 해도 항상 같은 화면만 나온다.

사실 우리가 사용하는 네이버, 유튜브, 페이스북 등의 대부분의 사이트는 모두 dynamic site이다. 그렇다면 왜 많은 사이트들이 static site가 아닌 dynamic site를 사용할까?

 - **Data-base UX를 설계할 수 있다.** 네이버 메인 화면의 경우 인기 검색어나 최신 뉴스 등 실시간으로 계속 업데이트되는 데이터들로 화면을 구성한다. 혹은 유튜브의 경우 유저가 주로 관심을 가지는 카테고리나 구독한 BJ 등 유저에게 종속된 데이터들로 메인 화면을 구성한다. 이처럼 시간과 상황에 따라 변화하는 화면은 static site라면 구현할 수 없다.

 - **유저, 시스템과의 상호작용을 할 수 있다.** 페이스북이나 인스타그램같은 sns 서비스를 보자. 내 친구가 글을 썼을 때 그 글이 나에게 보이게 하려면 친구의 글이 등록된 순간 내가 보고있는 화면의 html에도 그 정보가 들어와야 한다. 이러한 input에 의해 내 화면의 html이 계속 변화해야 하므로 dynamic site로써만 구현할 수 있다.

 - **클라이이언트 부분에서 처리할 수 있다.** 내가 페이스북에 글을 쓴다고 가정해보자. Dynamic site를 사용한다면 글을 쓰는 칸에 쓰고 싶은 글을 쓴 다음 확인 버튼만 누르면 된다. 그러면 자동으로 내가 쓴 글이 DB에 등록된 다음 내 글이 나오는 html에도 추가된다. 그러나 페이스북이 static site였다고 가정해보자. 그러면 내가 글을 등록하기 위해 페이스북 서버에 접속해서 내 글이 나오는 html을 수정해서 직접 글을 추가해야 한다. 이 작업은 번거로울 뿐 아니라 보안상으로도 문제가 생길 소지가 있다.

그 외에도 dynamic site를 사용하면 static site에서는 할 수 없는 무궁무진한 많은 것들을 할 수 있다. 그러나 static site generator라는 말에서 볼 수 있듯이 내가 사용하려는 Hugo는 static site를 만들어주는 것이다. 이렇게 좋은 dynamic site를 두고 왜 블로그를 만들 때 많은 사람들이 static site를 사용할까?

 - **속도가 훨씬 빠르다.** 비록 dynamic site에서도 virtual DOM이나 cache 등을 사용해서 속도를 빠르게 하려는 노력을 하지만, 동적으로 그때그때 계산을 해서 html을 만들거나 수정해야 하기 때문에 이미 정해져 있는 것을 뿌리기만 하면 되는 static site의 속도를 절대로 따라올 수 없다. 이는 마치 완제품을 집어서 사는 것과 주문제작물품을 구입하는 정도의 차이라고 생각하면 된다.

 - Dynamic site에 비해 **만들기 쉽다.** Dynamic site를 구현하려면 유저, 상호작용 시나리오, 데이터베이스, 백엔드 데이터 처리 등 신경 써야 할 것이 많고, 그만큼 구현해야 하는 부분도 많다. 그러나 static site는 내가 어떤 데이터를 어떻게 보여줄지 만 신경 쓰면 된다. 그만큼 블로그를 구축하는데 필요한 지식이나 시간 등의 비용이 적게 든다. 

 - 내가 만들고자 하는 블로그는 **방문객 입장에서 보면 READ ONLY이다.** 내 블로그에 사람들이 와서 하는 활동은 글을 읽는 것이 전부이다. 따라서 그들에 의해서 내가 보여주려는 화면이 바뀔 일이 없으니 굳이 힘들게 dynamic site로 만들 필요가 없다. 물론 내가 글을 등록하기 위해서는 서버 쪽에서 작업을 해야 하긴 하지만 이 또한 static site generator를 사용하면 쉽게 할 수 있다. 

## Static site generator

자 이제 static site로 블로그를 만들기로 다짐을 했다. static site가 dynamic site보다는 만들기 쉽다고 하지만 그래도 처음부터 만들기 시작하려 하면 막막하다. 나의 미적 감각으로 과연 블로그를 보기 좋게 꾸밀 수 있을지 걱정이 들기도 한다. 이러한 사람들을 위해 나온 것이 static site generator이다.

Static site generator는 말 그대로 static site를 만들어주는 프로그램으로, 정해진 포멧대로만 파일을 만든 뒤 빌드를 하면 **알아서 html을 생성해준다.** 예를 들어 `\content\` 폴더 안에 마크다운 형식으로 글을 쓰면 그 글을 내용으로 하는 블로그 포스트도 생성되고, 글 목록을 보여주는 페이지에도 자동으로 이 글이 추가된 html이 생성된다. dynamic site는 달리 보안 등의 문제를 고려할 필요도 없어서 이렇게 생성된 html을 그냥 서버에서 띄우기만 하면 된다. 서버에 띄우는 방법은 이 글에서 사용할 방법인 github page를 이용할 수도 있고, 간단한 개인 서버나 aws의 작은 서버를 활용해도 된다.

Static site generator의 또 다른 장점은 **다른 사람이 만들어둔 테마를 쉽게 가져와서 사용할 수 있다는 것**이다. 물론 컨텐츠와 내용도 중요하지만, 블로그는 결국 보여주는 서비스이기 때문에 디자인적인 측면을 절대로 버릴 수 없다. 그러한 면에서 다른 디자인 감각이 좋은 사람들이 해둔 작품들을 가져다 쓸 수 있다는 것은 매우 큰 메리트이고, 내가 좋아하는 테마가 있는지 여부에 따라 어떤 static site generator를 쓸지 정하는 사람도 있을 정도이다.

## 왜 Hugo인가?

이처럼 static site generator를 쓰면 쉽고 빠르게 블로그를 만들 수 있다. 이제 어떤 static site generator를 사용할지 선택해야 한다. 이 글에서 소개할 hugo 말고도 jekyll, hexo 등 수많은 static site generator가 존재한다. [staticgen](https://www.staticgen.com/)에 들어가면 다양한 static site generator를 볼 수 있다. 이렇게 많은 static site generator가 있긴 하지만 그래도 그중에 어느 정도 유명한 것을 사용하기를 권장한다. 왜냐하면 사람들이 많이 쓰는 것일수록 테마도 다양하고, 유지보수도 지속적으로 될 가능성이 크기 때문이다. 이러한 관점에서 나는 두 후보를 골랐다. 바로 **Hugo**와 **Jekyll**이다.

![](/images/blog_construct/jekyllhugo0.png#center50)

Jekyll은 Ruby로 만들어진 static site generator로, 무려 github CEO가 만들었다. 그래서 그런지는 모르겠지만 다른 static site generator는 내가 빌드를 한 다음 빌드해서 나온 결과를 올려야 하는 반면, jekyll은 프로젝트 폴더만 올려도 github에서 자동으로 빌드해서 페이지를 띄워준다. 이렇게 github에서 공식적으로 후원하는 만큼 가장 많은 사람들이 사용하고, 가장 많은 테마를 보유하고 있다. 또한 한글화된 문서도 가장 많이 가지고 있다. 그럼에도 불구하고 나는 왜 Hugo를 선택했을까?

 - **빠르다.** 반응속도가 다른 static site generator에 비해 굉장히 빠르다. 또한 jekyll은 github에서 자동으로 빌드를 해주는 대신 글이 많아질수록 빌드 속도가 굉장히 느려진다. 그러나 Hugo는 비록 별도의 빌드 과정이 있긴 하지만 빠르게 블로그를 배포할 수 있다.
 - **점점 성장하는 추세이다.** Github의 별 개수는 개발자들 사이에서 인기의 지표중 하나라고 볼 수 있다. [staticgen](https://www.staticgen.com/)에서 jekyll과 hugo의 별 갯수를 보면, 2년전만 해도 hugo가 만개 근처, jekyll이 2만대 중후반으로 2~3배정도 차이가 났었는데 지금은 거의 대등한 수준까지 올라왔다. 게다가 아래의 초록색+로 된 증가 추세를 보면 확실히 hugo가 jekyll보다 점점 인기도가 올라감을 알 수 있다. 입시 때도 점점 성적이 오르는 성장형 인재를 좋아했듯이, 이렇게 성장형 추세를 보이는 것은 그만큼 잠재력을 가지고 있는 대기만성형 인재라고 생각한다. 실제로 구글에 "hugo jekyll" 두 단어를 동시에 검색하면 jekyll에서 hugo로 이전한다는 글이 많이 보인다. 사람들이 바꾸는 데에는 다 이유가 있는 것이다.
 - **Go라는 최신 언어로 쓰여 있다.** 물론 최신 언어라고 무조건 좋은 것은 아니다. 그러나 일단 최신 언어라면 호환성 같은 문제도 적을 것이고, 최신 트렌드 기술도 반영하기 쉬울 것이다.
 - **한글 문서가 적다.** 엥? 이건 단점이 아닌가? 라고 생각할 수도 있다. 물론 대부분의 사람들에게는 이 점이 단점으로 작용할 것이다. 그러나 나에게는 이 점이 장점으로 작용했다. 비록 시간과 비용을 절약하기 위해 static site generator를 사용하긴 하지만, 블로그를 만들면서 어느 정도 공부는 해보고 싶었다. 그러한 면에서 외국문서와 Hugo 공식문서를 뒤지면서 삽질을 하다보면 배워가는 것이 어느 정도는 있을 것이라는 생각을 했다. 또한 내가 삽질한 과정을 잘 정리해두면 나중에 내 글이 hugo를 사용하려는 사람들에게 도움을 줘서 더 많은 사람들이 hugo를 쓰게 되지 않을까 하는 생각도 해봤다.
 - **내 마음에 드는 테마가 존재한다.** 테마는 블로그에서 굉장히 중요한 요소 중 하나이다. 만약에 Hugo에 마음에 드는 테마가 하나도 없었다면 위와 같은 이유가 있었음에도 불구하고 다른 static site generator를 선택했을지도 모른다. 다행히 hugo에도 마음에 드는 테마가 있었다.
 - **윈도우를 공식적으로 지원한다.** Jekyll은 안타깝게도 아직 윈도우를 공식적으로 지원하지 않는다. 그러나 hugo는 윈도우를 공식적으로 지원한다. 보통 개발자들은 리눅스나 맥OS를 많이 쓰는 편이라 이런 선택을 한 것 같지만, 아직 한국사회에서는 윈도우 없이는 불편한 점이 많기 때문에 윈도우를 공식 지원한다는 점은 충분히 하나의 장점이 될 수 있다. 

이러한 점들을 이유로 Hugo를 선택했다. 물론 그렇다고 Hugo가 무조건 최고다! 라는 것은 아니다. 각자의 장단점을 잘 따져보고, 테마들도 둘러보면서 자신에게 맞는 static site generator를 찾아보는 것을 추천한다.

# Hugo로 github.io 블로그 만들기

## 1. Git, Hugo 설치

Hugo로 작업하기 위해선 당연히 hugo도 필요하고, github page 기능을 이용해서 배포를 할 것이기 때문에 git도 필요하다. 우선 git을 먼저 설치해보자.

git은 [git-scm](https://git-scm.com/)에서 오른쪽 아래의 초록색 모니터의 다운로드 버튼을 클릭해서 다운받아 설치하면 된다. 중간에 PATH 환경변수 설정을 하면서 command line을 설정하는 부분이 있는데, 어차피 hugo도 cmd에서 작업해야 하기 때문에 git도 같이 cmd를 쓰는걸 추천한다.

![](/images/blog_construct/git0.png#center100)

설치를 완료했으면 cmd에서 `$ git --version`으로 제대로 설치가 되었는지 확인해보면 된다. 

이제 hugo를 설치해보자.

 1. [hugo release](https://github.com/gohugoio/hugo/releases)에서 최신버전을 다운받는다.
 2. `C:\Hugo\bin\`에 압축을 해제한다. 압축을 해제해서 보면 설치파일이 아니라 그냥 portable형 exe 파일이 나온다. 따라서 cmd에서 언제든지 사용하기 위해서는 환경변수에 추가해 줘야한다.
 3. `window + Q`로 검색창을 연 뒤 `환경 변수`를 검색해서 `시스템 환경 변수 편집`에 들어간다.
 4. 아래의 `환경 변수`를 클릭한다.
 5. 맨 위의 박스에서 `Path`를 더블클릭한다.
 6. `새로 만들기`를 클릭한 다음 아까 압축을 풀었던 곳인 `C:\Hugo\bin`를 등록해준다.
 7. 이제 아무데서나 `$ hugo version`을 쳐봐서 잘 등록 되었나 확인해본다.

만약 헷갈리는 부분이 있으면 [이 영상](https://gohugo.io/getting-started/installing#windows)을 보면 비록 영어이긴 하지만 친절하게 설명해준다.

## 2. Hugo 프로젝트 만들기

설치가 다 완료 되었으면 본격적으로 hugo 프로젝트를 만들어보자. 우선 프로젝트 폴더를 만들 곳을 고르자. 아무 곳이나 해도 큰 문제는 없지만 혹시 모르니 경로명에 한글이 없는 곳을 추천한다. cmd에서 그 경로로 접속한 후 

```
$ hugo new site <프로젝트 이름>
```

로 프로젝트 폴더를 만들면 된다. 그러면 프로젝트 이름으로 폴더가 하나 생기고, 그 안에 들어가 보면 기본적인 구조가 만들어져 있을 것이다. 이제부터 아래에서 설명할 때 기본경로는 이 프로젝트 폴더가 될 것이다.

## 3. 테마 다운받기

그 다음으로는 테마를 다운받아서 적용해보자. Hugo의 테마들은 [이곳](http://themes.gohugo.io)에서 둘러볼 수 있다.

![](/images/blog_construct/theme0.png#center75)

테마를 선택하면 나오는 창에서 demo 버튼을 누르면 실제로 테마가 적용됐을 때 어떻게 보이는지도 체험해 볼 수 있다. 어떤 테마를 사용할지 골랐으면 download 버튼을 눌러보자. 그러면 그 테마의 github 페이지로 연결된다. 그 github 페이지의 주소를 복사해두자. 다시 프로젝트 폴더로 돌아와서 `\themes\` 폴더에 들어간 후 

```
$ git clone <테마 github 페이지 주소>
```

를 하자. 그러면 자동으로 테마가 다운받아진다.

이렇게 다운 받은 테마를 적용하려면 `\config.toml`파일을 수정해야 한다. 보통 그 테마의 github 페이지의 README에 `config.toml`을 어떻게 수정해야 하는지 적혀있을 것이다. 만약에 아무것도 적혀있지 않다면 `\config.toml`에 

```
theme = "<테마 이름>"
```

을 추가하면 된다. 여기서 테마 이름은 `\themes\`폴더에 있는 테마 폴더의 이름이다. 예를 들어 내 테마가 `\themes\hello-programmer\`에 들어있다면 `\config.toml`에 

```
theme = "hello-programmer"
```

를 추가하면 된다.

## 4. 글 생성하기

글을 생성하기 위해 다시 프로젝트 폴더로 돌아와보자. 

```
$ hugo new <파일 이름>
```

을 치면 `\contents\<파일 이름>`이 생성된다. 예를 들어 `$ hugo new post/test.md`를 치면 `\contents\post\test.md`가 생긴다. 이제 이 파일을 열어서 마크다운 형식으로 글을 쓰면 빌드할 때 그 내용을 가지는 html이 자동으로 생성된다. 이 때 `\contents` 아래의 경로가 페이지의 경로가 된다. 예를 들어 `\contents\post\test.md`라는 문서를 만들었으면 이 글에 대한 주소는 `<baseURL>/post/test`이다. 파일을 열어보면 맨 위에 `---`로 감싸진 부분이 있는데, 이 부분을 [front matter](https://gohugo.io/content-management/front-matter/#readout)라고 하고 그 글에 대한 메타데이터를 나타낸다.

## 5. 미리보기

테마도 적용 했고 글도 썼으면 이제 내가 만든 것이 어떻게 보이는 지 확인할 차례이다. 휴고는 실제로 배포하기 전에 localhost로 미리 확인해보는 것을 지원한다. 프로젝트 폴더로 가서 

```
$ hugo server -D
```

를 하면 표 같은 게 뜨면서 서버를 실행한다. 이제 브라우저에서 [http://localhost:1313](http://localhost:1313)로 접속하면 내가 만든 블로그가 실제로 어떻게 보여지는지 확인할 수 있다. 

이 명령어에서 뒤에 붙는 `-D` 옵션은 draft 문서도 보여준다는 표시인데, draft란 현재 이 문서가 작업 중인지 아닌지 나타내는 특성으로 front matter에 포함되어있다. 문서의 front matter부분에 `draft = true`로 되어있으면, `hugo server -D`로 했을 때에는 이 문서도 포함되어 보여주지만 나중에 빌드할 때 생성되는 html에는 포함되지 않는다. 따라서 문서를 작업할 때 `draft = true`로 해놓고 localhost로 확인하면서 작업한 다음 나중에 작업을 완료하면 저 부분을 지우면 된다.

여기서 또 하나의 hugo의 장점이 드러나는데, hugo에서 이렇게 localhost로 서버를 실행한 후에 변경사항이 있으면 다시 끄고 실행하지 않아도 자동으로 변경사항이 적용된다. 참고로 브라우저 개발자 도구에서 캐시를 비활성화 해야 더 바로바로 적용된다.

## 6. 빌드 및 git push

미리보기로도 확인해서 완벽하게 작업을 했다고 생각하면 이제 빌드를 할 차례이다. 그 전에 git remote 설정을 먼저 해서 git에 저장을 해두자.

위에서도 말했듯이 hugo는 jekyll과 달리 github에서 자동으로 빌드를 해주는 것이 아니라 내가 빌드해서 만든 파일을 github page 기능으로 띄우는 것이다. 따라서 전체 컨텐츠를 저장할 곳과 빌드 결과로 github page를 띄울 곳, 이렇게 두개의 git repository가 필요하다. 첫번째 repository는 `<프로젝트 이름>`, 두번째 repository는 `<user-id>.github.io`로 만들면 된다.

우선 첫 번째 repository의 경우 그냥 프로젝트 폴더를 remote로 하면 된다. 여기서 주의할 점은 이 단계에서 아직 `\public\`폴더가 존재하면 안 된다. 혹시 미리 빌드를 해서 `\public\`폴더가 있다면 지워주도록 하자. 이제 프로젝트 폴더에서

```
$ git init
$ git remote add origin <첫 번째 repository url>
```

를 해주면 된다. 참고로 `repository url`는 github 페이지에서 오른쪽의 `Clone or download`라는 초록색 버튼을 눌렀을 때 나오는 주소이다.

이제 빌드의 결과로 실제 github page로 띄워질 두 번째 repository를 설정해보자. 빌드를 하면 그 결과가 `\public\`에 생기는데, 이 경로는 첫 번째 repository 안에 포함되는 경로이다. 이를 따로 repository로 가지기 위해서는 첫 번째 repository의 [submodule](https://git-scm.com/book/ko/v1/Git-%EB%8F%84%EA%B5%AC-%EC%84%9C%EB%B8%8C%EB%AA%A8%EB%93%88)로 지정해야 한다. 따라서 프로젝트 폴더에서

```
$ git submodule add -b master <두번째 repository url> public
```

을 하면 된다.

이제 빌드를 해보자. 참고로 빌드하기 전에 작업이 끝나서 빌드를 해줄 문서는 front matter에서 `draft = true` 부분을 지워줘야 빌드 항목에 포함된다. 이제 프로젝트 폴더에서 

```
$ hugo -t <테마 이름>
```

을 하면 빌드가 되면서 `\public\` 폴더에 빌드 결과가 나온다. 이제 이 결과를 github에 올리기만 하면 `<user-id>.github.io`로 블로그가 가 생성된다.

github에 push하려면 위에서 만들었던 두개의 repository에 모두 push를 해야 한다. 우선 `\public\` 폴더를 먼저 push한 다음, 프로젝트를 push하면 된다. 따라서

```
$ cd public
$ git add .
$ git commit -m "<커밋 메세지>"
$ git push origin master
$ cd ..
$ git add .
$ git commit -m "<커밋 메세지>"
$ git push origin master
```

를 하면 된다. 이제 `https://\<user-id\>.github.io`에 접속하면 내가 만든 블로그가 보일 것이다. 요즘 워낙 잘 만든 테마가 많이 나오다 보니 충분히 만족스러울 수도 있다. 하지만 무언가 맘에 들지 않는다면 다음 글에 설명할 테마 customizing을 해보자.