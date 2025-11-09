---
title: How I Built My Hugo Blog with Obsidian and GitHub Pages
date: 2025-11-09
tags:
  - hugo
  - obsidian
  - github
  - github-pages
  - blog
categories:
  - tutorial
  - dev
featuredImage: featured.png
summary: A step-by-step guide on how I built my Hugo blog, synced it with Obsidian, and deployed it to GitHub Pages using the Blowfish theme.
draft: false
---

## Overview

- This is my first blog post explaining how I created my personal blog using **Hugo**, **Obsidian**, and **GitHub Pages**.
- The goal: write notes in Obsidian, mirror them into Hugo, and host everything automatically on GitHub Pages.

## What I Installed (Windows)

- Installed Hugo prebuilt binaries by following this guide:  
  [Hugo Installation on Windows](https://gohugo.io/installation/windows/#prebuilt-binaries)
- Installed Hugo in `C:\Program Files\Hugo`
- Added the Hugo path to **Environment Variables** → **User Variables → Path**

## Creating a New Hugo Site

- Created a new Hugo site in the folder:  
  `C:\Own_Space\blog`
- Run this command:
```bash
hugo new site blog
```

Then inside the site folder (`blog`):
```bash
git init
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
```

## Configure the Theme

- Deleted the `hugo.toml` file created by Hugo.
    
- Copied the `config` files from the theme into the site folder.
    
- Edited the following files:
    
    - `hugo.toml`
        
    - `languages.en.toml`
        
    - `params.toml`
        
- Customized the menus and other options in `menus.en.toml`.
    
## Run Hugo Locally

- Used this command to start the server:
```bash
hugo server --disableFastRender --noHTTPCache
  ```
- This runs the site locally so I can preview all changes.
    

## Mirror Notes from Obsidian to Hugo

- To sync my notes from Obsidian into Hugo’s `content/Blog` folder, I used this command:
```bash
robocopy "C:\Own_Space\Second_Brain\Makenthor Lab\Blog" "C:\Own_Space\blog\content\Blog" /mir
```
- `/mir` keeps both folders mirrored, deleting and adding files as needed.
    

## Deploying to GitHub Pages

- Followed this guide: [Blowfish Hosting & Deployment](https://blowfish.page/docs/hosting-deployment/)
    
- Created a new file: `.github/workflows/gh-pages.yml`
    
- Added the following code:

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["master"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION:  0.152.2
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Useful Links

[Build your homepage using Blowfish and Hugo](https://n9o.xyz/posts/202310-blowfish-tutorial/)

[Getting Started With Hugo | FREE COURSE](https://youtu.be/hjD9jTi_DQ4?si=ut5KE0FRgz-WtvGk)

[Building a static website in Markdown with Hugo](https://youtu.be/MX4yy1dTVYg?si=czU_B_ogrZj1uo7l)

[I started a blog.....in 2024 (why you should too)](https://youtu.be/dnE7c0ELEH8?si=D6RoVY-buEPJPZeg)

[How To Make A Hugo Blowfish Website](https://youtu.be/-05mOdHmQVc?si=48edKZ6KvaaTqXVp)

[Publish a Static Hugo Website the EASY way // Cloudflare Workers](https://youtu.be/FZMgUSlNp-0?si=UZ8T4Xw_W2NJ6BSn)

```cardlink
url: https://blog.networkchuck.com/posts/my-insane-blog-pipeline/
title: "My Insane Blog Pipeline"
description: "Obsidian - Why I love it Obsidian is the BEST notes application in the world. Go download it: https://obsidian.md/ The Setup Create a new folder labeled posts. This is where you will add your blog posts ….that’s all you have to do Actually…wait….find out where your Obsidian directories are. Right click your posts folder and choose show in system explorer You’ll need this directory in upcoming steps. !Setting up Hugo Install Hugo Prerequisites Install Git: https://github.com/git-guides/install-git Install Go: https://go.dev/dl/ Install Hugo Link: https://gohugo.io/installation/"
host: blog.networkchuck.com
image: https://blog.networkchuck.com/og-image.png
```
