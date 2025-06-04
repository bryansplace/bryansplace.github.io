---
layout: page
title: Setup a github blog
date: 2024-09-02
---
Updated: ${mdate}$

## Initial setup

This site is, obviously, hosted on github. 

I wanted a simple static site and looked at all sorts of alternatives. My main concern was to find something simple, easy and without annoying adverts.

Self hosting is an obvious way to go for 'No adverts' and I looked at  several alternatives but finally decided I didn't like the security risk of letting  everyone onto my home lab.

Github can be used to host a web site, but it is a bit daunting for a beginner. 

As Github uses the Jekyll system for it's site, I at first thought it necessary to install and  understand Jekyll. But this is not so. Jekyll is I assume undoutably very flexible but seemed  very complex..too complex with all sorts of customisations and alternatives.

Github uses Jekyll in the background. If you use a supported theme and have a few files initially setup, new posts can just be added in. 

I would advise using your web browser to do the initial set up, afterwards, posts can be added using a browser or the android app.

## Step 0 Create a Git account

Your blogsite will be available at <your-user-name>.github.io. So, if you dont already have one, you might want to create your account with a friendly name; I chose BryansPlace.

## Step 1 Create a repository for your site

The clearest guide I have found to set up the repository for the site is at the official github site  [quickstart](https://docs.github.com/en/pages/quickstart).

However, that sets up a page based site, not a blog, so we need to change the theme and set up the initial file structure
   bryansplace.github.io
    |-  _config.yml
    |-   index.md
    |-   README.md
    |-   about.md
     _   _posts
             |-  2026-06-01-first-post.md


Blog posts will go in a sub-folder called _posts.
The index.md contains the 'home' page.
The about.md is the typical info about you.
The README.md doesn't go into the blog site but is visible to anyone visiting the github source repository.
 

## Step 2 File _config.yml

The configuration in the _config.yml files needs to be changed; instead of using minimal theme which sets up a pages based site, use the minima theme which is for a blog site with posts.

There are also two changes I make to the minima theme. 
1) Hide the default  rss site feed link which I don't want, and 
2) Add the possibility to incude a table of contents to long posts.

Here is a modified _config.yml file

```
# _config.yml

theme: minima
title: Bryan's Place

description: > 
  My blog. This description will go into the footer 
  or each page.

minima: 
  hide_site_feed_link: true

header_pages:
    - about.md

plugins:
  - jekyll-toc
```

Step 3 index.md

The index.md file is the home page and can be something like
```
---
layout: home 
title: Welcome to Bryan's Place!
permalink: / # This ensures it's the root URL
---

Hello!

This is in the index.md file

```

The --- marks and what is in-between is called frontmatter; I'll come to that below. 

Different Jekyll themes can do different things and might require different frontmatter.

## Step 4 about.md

I'm not a great fan of aboutme information, but I've left it in for completeness
```
---
layout: page 
title: About me....
permalink: /about/ 
---

Whatever you want blah blah blah..... 

```
## Step 5 First post


Post file names must start with a date in the format YYYY-MM-DD followed by a name with no spaces.
The date can be overidden in the front matter

In the _posts directory, the  2025-06-01-first-post.md file contains something like
```
---
layout: post
title: "First Post"
date: 2025-06-01
---
Welcome to my first post 

```

Note: The required filename format for posts is
YEAR-MONTH-DAY-title.md
where YEAR_MONTH_DAY is in YYYY-MM-DD format and title has no spaces.

## View site

Once the above files are setup, you can use your browser to go to yourusername.github.io and see the initial setup.



## Tidy up

The initial set up is....well...very initial. A few changes are necessary to make the blog presentable.

Jekyll is not required. Jekyll would be nice as changes can be previewed before 'pushing' changes to your public site.

Changes can be made either by logging into github or by using the github android app on a tablet or phone.

To improve the look a little, we can add a title and description by updating the _config.yml file. We'll also add an add-in to improve search engine scanning, and a feature to enable a table of contents generation for long posts. 
```
theme: minima
plugins:
  - jekyll-seo-tag

title: My Minima Blog
author: Your Name
description: A blog based on Minima, with ToC and no RSS

markdown: kramdown
kramdown:
  input: GFM
  toc_levels: 1..3

`


## Zettel notes

I looked at various applications to create markdown notes and eventually settled on [Zettel notes](https://www.zettelnotes.com).

I set up a Github repository that is synced with [Bryansplace.github.io]( https://github.com/bryansplace/bryansplace.github.io.git). This repository has to be in the apps internal storage. 
I have a Notes repository for misc and draft notes.



## Front Matter

This is the metadata stuff that goes at the top of the page. I think it varies depending on which system (Jekyll) and which Theme.

For the Jekyll Minima theme, the essential front matter fields are layout, title, date (for posts), and categories or tags. Optional fields like excerpt, author, published, and permalink offer additional control and customization. The theme also supports SEO-related fields such as description and image for better social media integration.
For a blog post, I think the following should be defined in a template
```
---
published: true or false
layout: post or page
title: whatever
date: overrides date from the name
categories:
tags:
---
```


