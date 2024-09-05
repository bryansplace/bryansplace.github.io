# Supported Front Matter for Jekyll Minima Theme

The Jekyll Minima theme, which is the default theme for Jekyll, supports a variety of front matter options that you can use to customize your pages and posts. Here’s a list of the most commonly used and supported front matter options for the Minima theme:

## 1. Basic Front Matter
These are the essential fields that should be included in every post or page:

```yaml
---
layout: post    # or 'page' for pages
title: "Your Post Title"
date: 2024-09-04 10:00:00 +0000  # Publication date
categories: [category1, category2]  # Categories, typically for posts
tags: [tag1, tag2]  # Tags, useful for posts
---
```

- **`layout`**: Defines the layout to use for the page/post. The Minima theme provides layouts like `post`, `page`, and `home`.
- **`title`**: The title of the page or post.
- **`date`**: The publication date of the post. This is typically only used for posts, not pages.
- **`categories`**: A list of categories that the post belongs to.
- **`tags`**: A list of tags for the post.

## 2. Page-Specific Front Matter
For pages (as opposed to posts), you might include:

```yaml
---
layout: page
title: "About Me"
permalink: /about/  # URL path for the page
---
```

- **`permalink`**: Customizes the URL for the page. This is especially useful for pages where you want a specific path (e.g., `/about/`).

## 3. Optional Front Matter
These fields allow for further customization:

```yaml
---
layout: post
title: "Your Post Title"
date: 2024-09-04 10:00:00 +0000
categories: [category1, category2]
tags: [tag1, tag2]
excerpt: "This is a short summary of the post."
author: "Your Name"
published: true  # Set to false to prevent the post/page from being published
last_modified_at: 2024-09-04 12:00:00 +0000  # Last modified date
toc: true  # Enable table of contents (if supported)
header: true  # Show or hide the header (if customized in the theme)
footer: true  # Show or hide the footer (if customized in the theme)
---
```

- **`excerpt`**: A short summary of the post, often shown in listings or feeds.
- **`author`**: The name of the author. Useful if your site has multiple authors.
- **`published`**: Set to `false` to prevent the post/page from being published.
- **`last_modified_at`**: Timestamp of the last modification, useful for displaying last edited dates.
- **`toc`**: If you have customized the theme to support a table of contents, setting this to `true` enables it on the page/post.
- **`header`** and **`footer`**: Control the visibility of the header and footer, if your theme customization supports these options.

## 4. SEO and Social Media
Minima also integrates with `jekyll-seo-tag`, so you can include SEO-specific front matter:

```yaml
---
layout: post⬤