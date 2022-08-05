
---
# 저장소 클론하기
## 깃 클론시 주의점
저장소 클론 시 반드시 해당형식으로 clone 명령어를 사용해주세요!!!
```bash
git clone --recurse-submodules https://github.com/Network-Leader/blog.git
```

# How to Post
## 직접 포스팅하는 경우

git clone 이후 해당 명령어를 사용하면 정해진 서식으로부터 새로운 .md 파일을 생성할 수 있습니다.  
추가로 첨부사진의 경우 아래 경로의 images 폴더에 넣고, md 파일에서 ![이미지파일제목](images/이미지파일제목.png)의 형식으로 불러올 수 있습니다.
```bash
# hugo new --kind post posts/<날짜-포스트명>/index.md
$ hugo new --kind post posts/2022-08-03-테스트제목/index.md

# hugo new --kind post posts/<날짜-포스트명>/index.md
$ mkdir -p content/posts/2022-08-03-테스트제목/images
```
#
md파일은 다음과 같은 구조를 가지고 있습니다.  
제목과 설명, 저자와 태그 등을 수정하시고 마지막에 있는 "---" 다음 줄부터 마크다운 문법으로 게시물을 작성해 주세요.
```yaml
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
tags: ["first","newdeal"] #태그 달기
author: "You" #본인 이름 넣기  
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: true
description: "설명이 들어갈 칸입니다." #설명 넣기
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true 
disableShare: false
disableHLJS: false  
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
---
```
#
마크다운으로 어떻게 글들을 쓰냐구요? 놀랍게도 notion 페이지에는 내보내기>Markdown 기능이 있답니다.  
그 외에도 많은 마크다운 에디터들이 있으니 편리하게 사용하면 좋겠죠?  
만약 노션으로 작성한다면 더욱 편리하게 업로드할 수 있는 방법을 준비했습니다.  

## Notion을 통해 포스팅하는 경우

우선 노션에서 업로드하고자 하는 노트를 Markdown & CSV 형식으로 내보냅니다.
#
![image](https://user-images.githubusercontent.com/77713508/182411434-ac06769c-75a0-4e85-a0ff-4b386108d0f3.png)

내보낸 Export-@@@@.zip 파일을 clone 한 blog 프로젝트 루트 디렉토리에 넣고 아래 명령어를 사용하면,  
준비된 쉘스크립트가 정해진 포스팅 형식으로 추출해줄거에요.
```bash
bash notion-to-gh-pages.sh
```
#
추출과정이 끝나면 프로젝트 루트 기준 content/posts 폴더에서 본인의 포스팅 폴더이름(스크립트-실행시간-포스팅제목)를 찾은 뒤,  
그 안의 index.md 파일을 편집기를 통해 열어줍니다.  
아래 5번째 줄부터 9번째 줄까지, 포스트의 카테고리/태그/저자/부제목을 설정해줍니다.
```bash
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}

categories: [""] #카테고리 설정
tags: [""] #태그 달기
author: "Me" #본인 이름 넣기  
# author: ["Me", "You"] # 공동저자 일 경우
description: "" # 부제목 넣기

showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableHLJS: true 
disableShare: false
disableHLJS: false  
hideSummary: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowCodeCopyButtons : true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
ShareButtons: []
---
```
#
로컬에서 포스팅 결과를 미리 살펴보려면 다음 명령어를 사용해보면 된답니다.
```bash
hugo server
```
#
이후 브라우저에서 http://localhost:1313/ 로 접속하면 적용된 포스트를 미리 확인해볼 수 있어요.  
확인 후 프로젝트 main 브랜치에 push 하면, 몇 분내로 블로그 페이지에 적용되는 것을 보실 수 있을거에요!
#
---

## Install/Update 📥

Read Wiki For More Details => **[PaperMod - Installation](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)**

---

## Social-Icons/Share-Icons 🖼️

Read Wiki For More Details => **[PaperMod-Icons](https://github.com/adityatelange/hugo-PaperMod/wiki/Icons)**

---

## FAQs / How To's Guide 🙋

Read Wiki For More Details => **[PaperMod-FAQs](https://github.com/adityatelange/hugo-PaperMod/wiki/FAQs)**

---

## Release Changelog 📃

Release ChangeLog has info about stuff added: **[Releases](https://github.com/adityatelange/hugo-PaperMod/releases)**

---

## [Pagespeed Insights (100% ?)](https://pagespeed.web.dev/report?url=https://adityatelange.github.io/hugo-PaperMod/) 👀

---

## Special Thanks 🌟

-   [**Highlight.js**](https://github.com/highlightjs/highlight.js)
-   [**Fuse.js**](https://github.com/krisk/fuse)
-   [**Feather Icons**](https://github.com/feathericons/feather)
-   [**Simple Icons**](https://github.com/simple-icons/simple-icons)
-   **All Contributors and Supporters**

## Stargazers over time 📈

<kbd>[![Stargazers over time](https://starchart.cc/adityatelange/hugo-PaperMod.svg)](https://starchart.cc/adityatelange/hugo-PaperMod)</kbd>
