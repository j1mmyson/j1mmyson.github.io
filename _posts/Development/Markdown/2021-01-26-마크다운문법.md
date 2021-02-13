---
title: "[Markdown] 마크다운 문법 5분컷"
date: 2021-01-26 +0800
categories: [Development, MarkDown]
tags: [markdown, md]
---

# 빠르게 간단한 마크다운 문법을 익혀보자
> 내가 주로 쓰는것들 위주로

## 헤더(Header)

----

```markdown
# H1
## H2
### H3
#### H4
##### H5
###### H6
```

<p style="font-size:40px"> H1</p>
<p style="font-size:30px"> H2</p>
### H3
#### H4
##### H5
###### H6

## 목록(List)

----

```markdown
1. 순서가
2. 있는
3. 목록

- 순서가
- 없는
- 목록
	- 탭으로
		- 들여쓰기
```

1. 순서가
2. 있는
3. 목록

- 순서가
- 없는
- 목록
  - 탭으로
    - 들여쓰기

## 인용구(Quote)

---

```markdown
> 인용구는
>> `>` 를 늘려서
>>> 들여쓰기(?) 가능
```

> 인용구는
> > `>` 를 늘려서
> > > 들여쓰기(?) 가능

## 구분선(Hr)

---

```markdown
---
```

----

## 폰트(Font)

---

```markdown
__bold1__
**bold2**
*italic1*
_italic2_
~~cancel~~
```

__bold1__   
**bold2**   
*italic1*   
_italic2_   
~~cancel~~   

## 코드블럭(Code Block)

---

```markdown
​```python
msg = "여러줄 코드블럭"
print(msg)
​```

` 한줄짜리 코드블럭 `
```

```python
msg = "여러줄 코드블럭"
print(msg)
```

` 한줄짜리 코드블럭`

## 링크(Link)

---

```markdown
[GitHub](https://github.com/)
GitHub: <https://github.com/>
```

[GitHub](https://github.com/)   
GitHub: <https://github.com/>

## 이미지(Image)

---

```markdown
![Alt](image/path/img.PNG)

크기조정을 하고싶다면
<img src="img/path/img.PNG" width = "400px" height="300px"></img>
```

![Image](https://github.com/j1mmyson/GoStudy/blob/master/image/banner.jpg?raw=true)

<img src="https://github.com/j1mmyson/GoStudy/blob/master/image/banner.jpg?raw=true" width="400px" height="300px"></img>

## 체크박스(Check box)

---

```markdown
- [x] 게임
- [ ] 공부
```

- [x] 게임
- [ ] 공부