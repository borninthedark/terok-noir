+++
title = "How to Weave Hugo Like a Boss, Ep. 1"
date = 2024-11-27
description = "A step-by-step guide to setting up the Hugo static site generator, including theme configuration, front matter setup, and creating a custom 'About Me' page."
tags = ["Hugo", "Static Site Generator", "Web"]
categories = ["Web", "Tech"]
slug = 'weave-hugo-1'
+++


# How to Set Up the Hugo Static Site Generator

## Table of Contents

1. [Introduction](#introduction)
2. [Installing Hugo](#installing-hugo)
3. [Creating a New Hugo Site](#creating-a-new-hugo-site)
4. [Configuring a Theme](#configuring-a-theme)
   - [Downloading a Theme](#downloading-a-theme)
   - [Applying the Theme](#applying-the-theme)
5. [Configuring Front Matter](#configuring-front-matter)
   - [What is Front Matter?](#what-is-front-matter)
   - [Customizing Front Matter](#customizing-front-matter)
6. [Creating a Custom 'About Me' Page](#creating-a-custom-about-me-page)
   - [Structuring the Markdown File](#structuring-the-markdown-file)
   - [Adding Custom Styles](#adding-custom-styles)
7. [Testing and Deploying Your Site](#testing-and-deploying-your-site)
8. [Conclusion](#conclusion)

---

## Introduction

Hugo is a fast and flexible static site generator that empowers developers to build high-performance websites with ease...after you get acquainted with some of its nuances. This guide is more of a living docu series through setting up a Hugo site, configuring a theme, managing front matter, and creating a personalized "About Me" page, allowing for more confident and complex setups in the future.

---

## Installing Hugo

Before diving in, ensure Hugo is installed on your system.

### Prerequisites

1. Install [Go](https://golang.org/doc/install) (if needed).
2. Ensure you have Git installed: `git --version`.

### Steps to Install Hugo

1. **Install via Package Manager**:
   - **macOS**: `brew install hugo`
   - **Linux**: `sudo apt install hugo`
   - **Windows**: Use [Chocolatey](https://chocolatey.org/): `choco install hugo`

2. **Verify Installation**:
   ```bash
   hugo version
   ```
   This command should return the version of Hugo installed.

---

## Creating a New Hugo Site

1. Open your terminal and create a new site:
   ```bash
   hugo new site my-hugo-site
   cd my-hugo-site
   ```

2. Initialize a Git repository:
   ```bash
   git init
   ```

---

## Configuring a Theme

Themes define your site's layout and design. Hugo offers a range of free and premium themes.

### Downloading a Theme

1. Browse themes at [themes.gohugo.io](https://themes.gohugo.io/).
2. Add your chosen theme as a Git submodule:
   ```bash
   git submodule add https://github.com/theme-repo/theme-name.git themes/theme-name
   ```

### Applying the Theme

1. Open the `config.toml` file.
2. Add the theme name:
   ```toml
   theme = "theme-name"
   ```

3. Test the site locally:
   ```bash
   hugo server
   ```

---

## Configuring Front Matter

### What is Front Matter?

Front matter is metadata included at the top of content files. It controls how Hugo processes and displays the content.

### Example Front Matter

Below is an example of a blog post's front matter in `content/posts/my-first-post.md`:
```toml
+++
title = "My First Post"
date = 2024-11-27
draft = false
tags = ["introduction", "hugo"]
categories = ["blog"]
description = "An introduction to my Hugo blog."
+++
```

### Customizing Front Matter

You can extend front matter to include custom fields:
```toml
+++
title = "Custom Post"
author = "Your Name"
readingTime = 5
summary = "A brief overview of this post."
+++
```

Use these fields in templates by referencing them in your theme:
```html
<p>Author: {{ .Params.author }}</p>
<p>Reading Time: {{ .Params.readingTime }} minutes</p>
```

---

## Creating a Custom 'About Me' Page

### Structuring the Markdown File

1. Create a new file:
   ```bash
   hugo new about.md
   ```

2. Open the file and edit its content:
   ```toml
   +++
   title = "About Me"
   date = 2024-11-27
   draft = false
   +++
   ```

3. Add your content below the front matter:
   ```markdown
   # About Me

   Hello! I'm [Your Name], a web developer passionate about building fast and reliable websites.
   ```

### Adding Custom Styles

To style the "About Me" page:
1. Open or create a custom CSS file in `assets/css/custom.css`.
2. Add your styles:
   ```css
   .about-title {
       font-size: 2.5rem;
       color: #4a90e2;
   }
   ```

3. Reference the CSS file in your layout. In `layouts/_default/baseof.html`:
   ```html
   <link rel="stylesheet" href="{{ "css/custom.css" | relURL }}">
   ```

4. Update the Markdown file to use a custom class:
   ```markdown
   # About Me {.about-title}

   Welcome to my page!
   ```

---

## Testing and Deploying Your Site

1. Test your site locally:
   ```bash
   hugo server
   ```
   Visit [localhost:1313](http://localhost:1313) to preview changes.

2. Build the site for deployment:
   ```bash
   hugo
   ```

3. Deploy your site to platforms like Netlify, Vercel, or GitHub Pages. For example:
   ```bash
   git add .
   git commit -m "Initial site setup"
   git push origin main
   ```

---

## Conclusion

Setting up Hugo allows you to build a fast, customizable website with ease. By configuring a theme, optimizing front matter, and creating a personalized "About Me" page, you can craft a professional site tailored to your needs (hint hint). In future installments, I'll be exploring and implementing additional Hugo features like shortcodes & custom layouts to enhance this site further.
