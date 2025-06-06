---
published: true
layout: post
title: "Minima Theme Front Matter Fields for GitHub Pages"
date: 2025-05-27 08:30:00 +0200
categories: [Jekyll, GitHub Pages, Themes]
tags: [Minima, Front Matter, YAML, Web Development]
permalink: /jekyll/minima-front-matter-guide/
excerpt: "A comprehensive guide to the YAML front matter fields processed by the Minima theme for Jekyll and GitHub Pages, detailing their purpose and usage."
comments: true
---

# Introduction

The **Minima** theme, Jekyll's default, relies  on YAML front matter to define and structure content. Understanding these fields is important to effectively managing the site's pages and blog posts.
Note that different themes use front matter in different ways

## Conclusion

Using the minima theme, categories and tags need additional configuration to be useful.

More advanced would be to use remote themes, but this would involve setting up my owen Jekyll.

## Frontmatter

Below is a breakdown of the common YAML front matter fields used by the Minima theme, explaining their purpose and typical usage.


### Common Front Matter Fields for Pages and Posts

These fields are placed at the very top of the Markdown files (e.g., `_posts/YYYY-MM-DD-title.md` or `about.md`), enclosed by triple-dashed lines (`---`).

```yaml
---
layout: post
title: "My Awesome Blog Post"
date: 2023-10-26 14:30:00 +0100
categories: [technology, web-development]
tags: [jekyll, minima, blog]
permalink: /my-awesome-post/
excerpt: "A brief summary of my awesome blog post."
comments: true
---
```
 * layout (Required)
   * Purpose: Specifies which HTML layout file (from the _layouts directory within the Minima theme or your own custom layouts) Jekyll should use to render the content.
   * Common values for Minima:
     * post: Used for blog posts (files in _posts/). This layout typically includes features like the post date and title.
     * page: Used for static pages (e.g., about.md, contact.md). This layout is simpler, often just displaying the title and content.
     * home: Specifically designed for your index.md or index.html file, which serves as your site's homepage. It typically displays a list of recent blog posts.
     * default: The foundational layout that other layouts might inherit from. You generally won't set this directly on content files unless you want a very barebones page.
 * title
   * Purpose: The main heading or title for your page or blog post. This is displayed prominently on the page itself, in the browser tab, and is also used for generating navigation links if configured in your _config.yml.
 * date (Primarily for Posts)
   * Purpose: Specifies the publication date and time of a blog post. While Jekyll can infer the date from the filename (YYYY-MM-DD-title.md), explicitly setting it here allows for more precise control (e.g., setting an exact time, or overriding the filename date). It's used for sorting posts and displaying the date on the blog.
   * Format: YYYY-MM-DD HH:MM:SS +/-TTTT (hours, minutes, seconds, and timezone offset are optional but recommended for consistency).
 * categories (Primarily for Posts)
   * Purpose: Assigns the post to one or more categories. This is typically an array (YAML list) of strings. While Minima doesn't automatically generate category archive pages by default, this field is useful for organization and can be leveraged by plugins or custom layouts.
   * Example: categories: [technology, tutorials]
 * tags (Primarily for Posts)
   * Purpose: Similar to categories, but often used for more granular topics or keywords. Also typically an array of strings. Like categories, Minima doesn't create tag pages by default, but it's a common field used by Jekyll plugins for generating tag archives.
   * Example: tags: [markdown, static-site, github-pages]
 * permalink
   * Purpose: Defines the custom URL path for the generated page or post. This overrides Jekyll's default permalink structure.
   * Examples: permalink: /about/ or permalink: /my-custom-post-url/
 * excerpt (Primarily for Posts)
   * Purpose: Provides a short summary of the post that can be displayed on the homepage or in post listings. If not provided, Jekyll will often automatically generate an excerpt from the beginning of your post's content.
   * Note: Minima's home.html layout can display excerpts if show_excerpts: true is set in your _config.yml.
 * comments (Primarily for Posts)
   * Purpose: Controls the visibility of comments for a specific post.
   * Value: Set to false to disable comments for that particular post. (Minima integrates with Disqus, and this field usually signals to the Disqus integration to hide the comment section for that specific post). By default, if not specified or set to true, comments are usually enabled if configured in your _config.yml.
Minima-Specific Front Matter Fields (Less Common in Content Files)
While less frequently used directly in content files, these fields are relevant for fine-tuning specific Minima features:
 * list_title (for index.md / home layout)
   * Purpose: Overrides the default "Posts" heading on your homepage to something custom, like "Latest Articles" or "My Blog Entries."
   * Example in index.md:
     ---
layout: home
title: "Welcome to My Blog!"
list_title: "Recent Musings"
---

Important Note on _config.yml and Front Matter:
Many global settings for the Minima theme are controlled in your site's _config.yml file, not directly in the front matter of individual pages/posts. These _config.yml settings influence how the front matter fields are interpreted or displayed across your entire site.
Examples of Minima-related settings in _config.yml:
 * title: Global site title.
 * email: Your email address.
 * description: Site description for SEO and RSS feed.
 * author: Your name (often referenced by posts).
 * minima: (a hash for Minima-specific settings):
   * date_format: Controls how dates are displayed.
   * skin: Changes the theme's color scheme (e.g., dark, classic, solarized-light).
   * social_links: To display social media icons in the footer (requires title, icon, url).
 * show_excerpts: true or false to show post excerpts on the homepage.
 * header_pages: A list of Markdown files to include in the main navigation (Minima uses their title front matter for the link text).
By understanding these front matter fields and how they interact with Minima's layouts and your _config.yml, you gain robust control over the content and presentation of your GitHub Pages site.