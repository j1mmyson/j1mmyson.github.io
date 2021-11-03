---
title: "[개발자 블로그] Chirpy테마 게시글 작성 가이드"
date: 2020-12-13 +0800
categories: [Blog]
tags: [blogging]
---

첫글로 [Writting New Post](https://chirpy.cotes.info/posts/write-a-new-post/)페이지를 번역해보았다.

## Naming & Path

`YYYY-MM-DD-TITLE.EXTENSION` 의 형식으로 새 파일을 만들어주고 `_post/` 폴더에 넣어준다.

`EXTENSION`은 `md` 혹은 `markdown`으로 해주어야한다.

<br>

## Front Matter

기본적으로, 글을 작성할 때 아래와 같은 양식의 Front Matter를 작성해야 한다.

```yaml
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/- TTTT
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG]		# TAG는 반드시 소문자로 이루어져야함!
---
```

> **Note**: post의 **layout**이 기본적으로 `post`으로 설정되어있어서 Front Matter에 따로 **layout**변수를 만들어 주지 않아도 된다.

<br>


## Timezone of date

더 정확한 시간을 포스트에 제공하기 위해서는 `_config.yml`의 `timezone`만 설정해줄 것이 아니라, Post FrontMatter에서 `date`필드에도 post timezone을 제공해주어야한다.
형식: `+/-TTTT`, e.g. `+0800`.

<br>


## Categories & Tag

각 포스트의 `categories`는 2개의 요소로 구성되어있으며 `tags`는 0~무한 개의 요소를 가질 수 있다. 

```yaml
categories: [Animal, Insect]
tags: [bee]
```
<br>


## Table of Contents

기본적으로, **T**able **o**f **C**ontents(TOC)는 포스트의 오른쪽 패널에 위치한다. 만약 너가 이 기능을 끄고싶다면 `_config.yml`파일로가서 `toc`값을 `false`로 바꾸어주면된다. 하나의 포스트에만 이 기능을 끄고싶다면 **FrontMatter**에서 `toc`값을 `false`로 바꾸면된다:

```yaml
---
toc: false
---
```
<br>


## Comments

TOC와 비슷하게 *Disqus* comments가 각 포스트마다 기본적으로 내장되어있으며, 모든 글에대한 comments유무는 `_config.yml`파일의 `comments`값에 의해 결정된다. 하나의 포스트에만 이 기능을 끄고 싶다면 **FrontMatter**에서 값을 설정해주면된다:

```yaml
---
comments: false
---
```
<br>


## Mathematics

웹페이지의 성능적인 이유에서 mathematical 기능은 기본적으로 꺼져있으나 아래 코드를 추가하여 켤 수 있다:

```yaml
---
math: true
---
```
<br>


## Mermaid

**Mermaid**는 훌륭한 표생성 도구이다. 아래코드를 추가함으로 기능을 켤 수 있다.

```yaml
---
mermaid: true
---
```

그러면 너는  <code class="highlighter-rouge">```mermaid</code>로 그래프 코드를 감싸 다른 마크다운 언어와 같이 사용할 수 있다.

<br>

## Images

### Preview image

포스트의 최상단에 이미지를 넣고싶다면 아래와 같이 url을 추가하여 이미지를 넣을 수 있다:

```yaml
---
image: /path/to/image-file
---
```

### Image caption

이미지를 삽입한 다음 라인에 아래와 같이 *Italics*를 추가해서 이미지 캡션을 달 수 있다. 이미지 캡션은 이미지의 하단에 달린다:

```markdown
![img-description](/path/to/image)
_Image Caption_
```

### Image size

`width`를 아래와같이 추가하여 이미지 사이즈를 설정할 수 있다.

```markdown
![image-description](path){: width="400px"}
```

기본적으로 이미지의 위지는 `centered`이다. 아래 3가지 방법으로 이미지의 위치를 `normal`, `left`, `right`중 하나로 설정할 수 있다:

- Normal

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: width="350" class="normal"}
  ```

- Left

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: width="350" class="left"}
  ```

- Right

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: width="350" class="right"}
  ```

> 너가 이미지의 위치를 정하게되면 `image caption`은 달 수 없게된다.

<br>

## Pinned Posts

너의 홈페이지 메인화면에 특정 게시물을 고정시킬 수 있다. 순서는 포스팅 날짜가 최근일수록 위로 올라오게된다.

```yaml
---
pin: true
---
```
<br>

## Code Block

마크다운 문법 <code class="highlighter-rouge">```</code> 를 통해 쉽게 코드블럭을 생성할 수 있다.

``` This is a common code snippet, without syntax highlight and line number. ```
<br>

## Specific Language

<code class="highlighter-rouge">```language</code>를 통해 line number 와 syntax highlight를 갖는 코드블럭을 생성할 수 있다.
> **Note**: The Jekyll style `{% raw %}{%{% endraw %} highlight LANGUAGE {% raw %}%}{% endraw %}` or `{% raw %}{%{% endraw %} highlight LANGUAGE linenos {% raw %}%}{% endraw %}` are not allowed to be used in this theme !

```yaml
items:
	- part_no:	A4786
	  descrip:	Water Bucket (Filled)
	  price:	1.47
	  quantity:	4
```
<br>

## Liguid Codes

만약 **Liquid** snippet(수도코드?)를 배치하고싶다면 liquid code를 `{% raw %}{%{% endraw %} raw {%raw%}%}{%endraw%}`와 `{% raw %}{%{% endraw %} endraw {%raw%}%}{%endraw%}`로 감싸주면된다.

{% raw %}
```liquid
{% if product.title contains 'Pack' %}
  This product's title contains the word Pack.
{% endif %}
```
{% endraw %}
<br>

## Learn More

더 많은걸 알고싶다면, 여기로[Jekyll Docs:Posts](https://jekyllrb.com/docs/posts/).

