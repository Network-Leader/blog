<h1 align=center>Hugo PaperMod | <a href="https://adityatelange.github.io/hugo-PaperMod/" rel="nofollow">Demo</a></h1>

<h4 align=center>☄️ Fast | ☁️ Fluent | 🌙 Smooth | 📱 Responsive</h4>
<br>

> Hugo PaperMod is a theme based on [hugo-paper](https://github.com/nanxiaobei/hugo-paper).
> The goal of this project is to add more features and customization to the og theme.

The [demo](https://adityatelange.github.io/hugo-PaperMod/) includes a lot of documentation about Installation, Features with a few more stuff. Make sure you visit it, to get an awesome hands-on experience and get to know about the features ...

**ExampleSite** can be found here: [exampleSite](https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite). Demo is built up with [exampleSite](https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite) as source.

[![Minimum Hugo Version](https://img.shields.io/static/v1?label=min-HUGO-version&message=0.83.0&color=blue&logo=hugo)](https://github.com/gohugoio/hugo/releases/tag/v0.83.0)
[![Build GH-Pages](https://github.com/adityatelange/hugo-PaperMod/workflows/Build%20GH-Pages/badge.svg)](https://github.com/adityatelange/hugo-PaperMod/deployments/activity_log?environment=github-pages)
[![GitHub](https://img.shields.io/github/license/adityatelange/hugo-PaperMod)](https://github.com/adityatelange/hugo-PaperMod/blob/master/LICENSE)
[![hugo-papermod](https://img.shields.io/badge/Hugo--Themes-@PaperMod-blue)](https://themes.gohugo.io/themes/hugo-papermod/)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=adityatelange_hugo-PaperMod&metric=alert_status)](https://sonarcloud.io/dashboard?id=adityatelange_hugo-PaperMod)
![code-size](https://img.shields.io/github/languages/code-size/adityatelange/hugo-PaperMod)
[![Discord](https://img.shields.io/discord/971046860317921340?label=Discord)](https://discord.gg/ahpmTvhVmp)

---

<p align="center">
  <kbd><img src="https://user-images.githubusercontent.com/21258296/114303440-bfc0ae80-9aeb-11eb-8cfa-48a4bb385a6d.png" alt="Mockup image" title="Mockup"/></kbd>
</p>

---

## How to Post
git clone 이후 해당 명령어를 사용하면 정해진 서식으로부터 새로운 .md 파일을 생성할 수 있습니다.
```bash
$ hugo new --kind post archetypes/<포스트 명>.md
```
md파일은 다음과 같은 구조를 가지고 있습니다. 제목과 설명, 저자와 태그 등을 수정하시고 마지막에 있는 "---" 다음 줄부터 마크다운 문법으로 게시물을 작성해 주세요.
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
마크다운으로 어떻게 글들을 쓰냐구요? 놀랍게도 notion 페이지에는 내보내기>Markdown 기능이 있답니다.
그 외에도 많은 마크다운 에디터들이 있으니 편리하게 사용하면 좋겠죠?

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
