+++
title = "Weaving a Hugo Application in a Docker Container"
date =  2024-11-28
tags = ["Hugo", "Docker", "Web Development"]
categories =  ["Development"]
slug = 'weave-hugo-2'
draft = false
summary = 'Docker + Hugo: Absolutely Stylish Web Apps'
+++

Greetings! Today, I shall guide you through the delicate art of creating a Hugo application and running it within a Docker container. While some might find such endeavors purely technical, I prefer to think of it as using Hugo to weave a digital tapestry. 

Shall we begin?

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Setting Up the Hugo Application](#setting-up-the-hugo-application)
   - [Installing Hugo](#installing-hugo)
   - [Creating Your Hugo Site](#creating-your-hugo-site)
   - [Adding Themes and Content](#adding-themes-and-content)
4. [Creating a Docker Environment](#creating-a-docker-environment)
   - [Dockerfile for Hugo](#dockerfile-for-hugo)
   - [Building the Docker Image](#building-the-docker-image)
5. [Running Hugo in a Docker Container](#running-hugo-in-a-docker-container)
   - [Local Development with Docker](#local-development-with-docker)
   - [Serving Your Hugo Site](#serving-your-hugo-site)
6. [Conclusion](#conclusion)

---

## Introduction

Ah, the charm of Hugo—a lightning-fast static site generator paired with the robust isolation of Docker. Together, they provide the perfect blend of simplicity and elegance, much like a fine Cardassian kanar served at the perfect temperature. I'll guide you through the meticulous process of creating your Hugo site and packaging it within a Docker container for maximum portability and ease of deployment.

---

## Prerequisites

Before we embark on this journey, you’ll need the following tools:

- **Docker**: Installed and running. Download it [here](https://www.docker.com/).
- **Hugo**: The static site generator. Details are available on the [official Hugo website](https://gohugo.io/).
- **A terminal**: Command line skills will be your tools of choice.

If these are ready, you’re prepared for what lies ahead.

---

## Setting Up the Hugo Application

### Installing Hugo

First, let us ensure Hugo is installed on your system:

1. **MacOS/Linux**: Use Homebrew or your distro's package manager:
   ```bash
   brew install hugo
   ```
   I use Arch Linux, so I'd use:
   ```bash
   sudo pacman -S hugo
   ```

   Alternatively, download the appropriate binary from the [Hugo releases page](https://github.com/gohugoio/hugo/releases).

2. **Windows**: Download the installer or use `choco`:
   ```bash
   choco install hugo -confirm
   ```

Verify the installation:
```bash
hugo version
```
A confirmation message should display your Hugo version. Splendid!

---

### Creating Your Hugo Site

Now, create the foundation of your site. In the terminal, run:
```bash
hugo new site my-hugo-site
cd my-hugo-site
```

This scaffolds a new Hugo site. Like a new suit awaiting tailoring, it’s clean and functional, but we can embellish it further.

---

### Adding Themes and Content

Select a theme from the [Hugo Themes website](https://themes.gohugo.io/) and add it to your site:
```bash
git init
git submodule add https://github.com/the_theme_repository themes/mytheme
echo 'theme = "mytheme"' >> config.toml
```

Add some content:
```bash
hugo new posts/my-first-post.md
```

Edit the generated file in `content/posts/` and add some text. Once satisfied, preview your work:
```bash
hugo server
```

Open `http://localhost:1313` in your browser to see your masterpiece.

---

## Creating a Docker Environment

### Dockerfile for Hugo

To run Hugo within a Docker container, we need a `Dockerfile`. Create one in your Hugo site’s root directory:

```dockerfile
# Use the official Hugo image
FROM klakegg/hugo:ext

# Set the working directory
WORKDIR /usr/share/blog

# Copy the site into the container
COPY . .

# Expose the port for the development server
EXPOSE 1313

# Default command to serve the Hugo site
CMD ["hugo", "server", "--bind=0.0.0.0", "--baseURL=http://localhost"]
```

---

### Building the Docker Image

Build your Docker image. In the terminal, run:
```bash
docker build -t my-hugo-site .
```

Docker will package your Hugo site into an image, much like folding a tailored suit into a portable garment bag.

---

## Running Hugo in a Docker Container

### Local Development with Docker

Start a container for local development:
```bash
docker run --rm -p 1313:1313 -v $(pwd):/usr/share/blog my-hugo-site
```

Visit `http://localhost:1313` to see your site live. Make changes locally, and they will reflect immediately in the container.

---

### Serving Your Hugo Site

To generate the final static site and serve it, modify the `Dockerfile` to include:
```dockerfile
CMD ["hugo", "--bind=0.0.0.0", "--baseURL=http://localhost"]
```

Then, rebuild your image:
```bash
docker build -t my-hugo-site:static .
```

Run the static site:
```bash
docker run --rm -p 8080:80 my-hugo-site:static
```

Navigate to `http://localhost:8080` to admire your fully realized site, served flawlessly from a container.

---

## Conclusion

...And there you have it—your Hugo site, dressed to perfection and contained within Docker’s ever-reliable compartments! Whether you’re sharing stories, crafting portfolios, or orchestrating the most intricate of conspiracies, this workflow ensures efficiency, portability, and elegance.
