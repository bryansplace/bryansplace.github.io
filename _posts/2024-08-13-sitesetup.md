---
layout: page
title: Site setup
date: 2024-09-02
categories:
tags:

published: false
excerpt:
description:

---
${mdate}$

## Initial setup

This site is, obviously, hosted on github. 

I wanted a simple static site and looked at all sorts of alternatives. My main concern was to find something simple and easy. This summarizes where I'm at.

Setting up the site can be done by following the [Github Skills course](https://github.com/skills/github-pages).

As Github uses the Jekyll system for it's site, I thought it necessary to understand Jekyll.

Error; Jekyll is undoutably very flexible but accordingly very complex..too complex. Once set up, new pages can just be added in. 

## Zettel notes

I looked at various applications to create markdown notes and eventually settled on [Zettel notes](https://www.zettelnotes.com).

I set up a Github repository that is synced with [Bryansplace.github.io]( https://github.com/bryansplace/bryansplace.github.io.git). This repository has to be in the apps internal storage. 
I have a Notes repository for misc and draft notes.



## Config

There is a [config.yml file](https://github.com/bryansplace/bryansplace.github.io/blob/main/_config.yml) that controls Jekyll. Mine is more or less  one line at present.

## Themes

Github supports different themes which can change the way the site looks. An initial look indicated that I'll stick with the default 'minima' and maybe change later.





## Front Matter

This is the metadata stuff that goes at the top of the page. I think it varies depending on which system (Jekyll) and which Theme.

For the Jekyll Minima theme, the essential front matter fields are layout, title, date (for posts), and categories or tags. Optional fields like excerpt, author, published, and permalink offer additional control and customization. The theme also supports SEO-related fields such as description and image for better social media integration.






