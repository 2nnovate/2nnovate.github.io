---
title: "jekyll blog 에 카테고리 추가"
layout: post
categories: jekyll
---

## summary

> _config.yml 에서 path variable 을 설정하고, 네비게이션을 추가한다.
>
> liquid 문법을 이해하고 category 별 post 를 보여주는 html 페이지를 작성한다.
>
> 각 post 의 yml 에 categories 를 설정한다.

## 1. 전역 환경설정

> _config.yml 파일은 페이지 전역에 적용되는 환경설정을 하는 파일이다.

_config.yml 에 설정한 옵션은 html 파일에서 site 전역변수로 사용할 수 있다.
_config.yml 에 lang 을 한국어로 설정하고 html 파일에서 사용하는 예는 아래와 같다.

```yaml
lang: "ko"
```

```html
<html lang="{{ page.lang | default: site.lang | default: "en" }}">
```

### 1-1) path variable 설정

> 카테고리 별로 정리된 포스팅을 위해 semantic url 을 적용한다.

_config.yml 에 permalink 를 설정하자.
permalink 는 페이지의 url 을 설정하는 option 으로, `:` 를 사용하여 변수를 설정할 수 있다.

카테고리/글제목으로 permalink 를 설정하여 url 에 의미를 부여할 것이다.

```yaml
permalink: /:categories/:title/
```

위와 같이 permalink 를 설정하면 각 포스트의 yml 에 설정한 categories 와 page 파일의 제목으로 url 이 생성된다.

### 1-2) navigation 설정

> 상위 메뉴로 사용할 navigation 을 설정한다.

페이지에서 사용할 네비게이션을 설정한다. navigation option 을 사용하고 사용법은 아래와 같다.

```yaml
navigation:
  - {file: "index.html", title: "Home"}
  - {file: "categories.html", title: "Category"}
  - {file: "README.md", title: "About"}
```

**permalink 와 navigation option 을 통해 전역 환경설정을 완료했다. 이제 실제 html 페이지에서 이를 사용해보자.**

## 2. liquid

> liquid 는 템플릿 언어로, 중괄호를 사용하여 미리 정의된 변수를 출력하거나 논리연산을 할 수 있다.

`{{ "{{ variable " }}}}`

중괄호 두 개를 사용하면 변수를 출력할 수 있다.

`{{ "{% assign my_variable = false " }}%}`

중괄호와 퍼센를 사용하면 논리 연산이 가능하다.

자세한 내용은 [liquid 공식 문서](https://shopify.github.io/liquid/)를 참고하자.

### 2-1) navigation 환경설정을 통한 상단 menu 구현

> 조각파일과 liquid 문법을 사용해 상단 메뉴를 구현한다.

#### layout

jekyll 의 디렉토리 구조 중 _layouts 하위에 default.html 을 작성(존재한다면 수정)해보자.
_layout 에 있는 파일은 post 등 각 파일 상단의 yml 설정에서 layout 옵션을 통해 사용할 수 있다.
쉽게 이야기 하면, _layouts 에 설정한 html 파일을 껍데기로 사용하고 블로그 포스트 등의 페이지의 내용을 layout 의 content 로 끼워 넣는 것이라 생각하면 된다.

```html
<!DOCTYPE html>
<html>
    <head>...</head>
    <body>
        {{ "{{ content " }}}}
    </body>
</html>
```
*_layouts/default.html*


```markdown
{% raw  %}
---
layout: default
---
{% endraw %}
안녕하세요.
```
*index.html*

위와 같이 default layout 을 설정하고 페이지의 yml 설정에서 layout 을 default 로 설정하면,
default.html 의 content 내용 안에 '안녕하세요'가 표시된다.

#### 조각파일 (includes) 및 변수 (variables)

조각파일은 _includes 폴더에 위치한 파일을 liquid 의 변수로 불러와 사용하는 것을 의미한다.
_includes 디렉토리 하위에 menu.html 파일을 생성(혹은 수정)하자.

```html
{% raw  %}
{% if include.menu %}
  <nav>
  {% for item in include.menu %}
    {%- assign node = site.pages | where: "name", item.file | first -%}
    {%- assign url = item.url | default: node.url -%}
    {%- assign title = item.title | default: node.title -%}
    <a aria-label="{{ title }}" href="{{ url | relative_url }}" {% if url == page.url %}class="selected"{% endif %}>
      {%- if title -%}
        <span aria-hidden="true" {% if include.icons %}class="hidden"{% endif %}>{{ title }}</span>
      {%- endif -%}
    </a>
  {% endfor %}
  </nav>
{% endif %}
{% endraw %}
```
*_includes/menu.html*

설정한 menu 조각파일을 default 레이아웃에서 사용하도록 해보자.

```html
{% raw  %}
...
<header class="icons">
  {% unless site.show_title != true and site.navigation and site.external %}
    <a href="{{ "/" | relative_url }}" class="title">{{ site.title | escape }}</a>
  {% endunless %}
  {% if site.navigation %}
    {% include menu.html menu=site.navigation %}
  {% endif %}
</header>
...
{% endraw %}
```
*_layouts/default.html*

default.html 에서 if 부분을 보자.
site 는 전역환경에 접근할 수 있는 변수로, site.category 는 _config.yml 에 설정한 navigation 옵션을 의미한다.
우리는 전역환경에 navigation 옵션을 설정하였으므로 if 절 안으로 들어간다.

include 는 _includes 파일에 접근하는 것으로, menu.html 파일을 사용한다는 의미이다.
menu.html 뒤는 파라메터를 설정하는 것으로, menu 를 site.navigation 으로 설정한다는 의미이다.
이렇게 하면 _includes/menu.html 안에서 include.menu 로 site.navigation 을 사용할 수 있다.

자세한 내용은 아래 공식 문서를 참조하자.
*[변수 관련 문서](https://jekyllrb-ko.github.io/docs/variables/)*
*[조각파일 관련 문서](https://jekyllrb-ko.github.io/docs/includes/)*

### 2-2) category 상세 페이지 설정

> 카테고리별 포스트 내용을 보여주는 페이지를 작성한다.

루트 디렉토리에 categories.html 파일을 생성한다.

```html
{% raw  %}
---
title: "Categories"
permalink: "/categories/"
layout: page
---

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name }}"></a>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
{% endraw %}
```

요약하자면, categories 를 돌면서 해당 카테고리에 포함된 포스트를 나열하는 페이지이다.
[liquid 문서](https://shopify.github.io/liquid/)를 참조하며 이해해 보도록하자.

## 3. 각 포스트 페이지에 categories 를 설정한다.

> 포스팅을 작성할 때 yml 설정에 categories 를 설정한다.

_posts 에 글을 작성할 때 yml 설정에 categories 를 설정한다.
이를 설정하면 자동으로 site.category 에 해당 카테고리가 저장된다.
categories 설정은 아래와 같이 한다.

```markdown
{% raw  %}
---
title: "jekyll blog 에 카테고리 추가"
layout: post
categories: jekyll
---

...
{% endraw %}
```

한 포스팅에 여러개의 카테고리를 추가하려면 아래와 같이 array 로 설정하면 된다.
```yaml
categories: [jekyll, etc]
```

## end

jekyll 에 포스트를 카테고라이징 하는 방법은 필자가 쓴 방법 이외에도 많이 존재한다.
필자는 이 방법이 설정 및 사용이 분리되어 있다는 점에서 편하고 효율적이라 판단하여 이 방법을 택하였다.
category 를 통해 블로그 포스팅을 체계적으로 관리해보자.
