---
layout: post
title: "Building My First Jekyll Website: Lessons Learned"
description: >
  My journey of creating this website using Jekyll and GitHub Pages, including the challenges I faced and solutions I discovered.
categories: [tech, web-development]
tags: [jekyll, github-pages, web-development, learning]
---

When I decided to create my personal website, I wanted something that was both professional and easy to maintain. After researching various options, I settled on Jekyll with the Hydejack theme, hosted on GitHub Pages.

## The Challenge

As someone relatively new to static site generators, I faced several hurdles:
- Understanding Jekyll's file structure
- Configuring the theme properly
- Managing assets and permalinks
- Deploying to GitHub Pages

## The Learning Process

### 1. File Structure Matters
One of my biggest initial mistakes was not maintaining the proper folder structure when copying theme files. Jekyll relies heavily on convention, and files need to be in specific directories:

### 2. Configuration is Key
The `_config.yml` file is the heart of any Jekyll site. Key lessons:
- Use `remote_theme` for GitHub Pages deployment
- Set proper `baseurl` for subdirectory hosting
- Configure permalinks thoughtfully for SEO

### 3. GitHub Pages Deployment
Understanding the difference between local development and GitHub Pages deployment was crucial. Some gems work locally but not on GitHub Pages due to security restrictions.

## What I Built

This website now features:
- Responsive design with Hydejack theme
- Blog functionality for sharing thoughts
- Project showcase section
- About page with my background

## Next Steps

Moving forward, I plan to:
- Add more interactive elements
- Integrate analytics to understand visitor behavior
- Expand the project portfolio
- Write more technical tutorials

## Resources That Helped

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Hydejack Theme Guide](https://hydejack.com/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)

Building this website taught me that the best way to learn is by doing. Every error message and debugging session was a step forward in understanding how modern web development works.

*What's your experience with Jekyll or static site generators? I'd love to hear your thoughts!*
