# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Jekyll-based blog using the Minimal Mistakes theme, hosted on GitHub Pages. The blog focuses on engineering topics, AI development, and ministry content.

## Architecture

- **Static Site Generator**: Jekyll with Minimal Mistakes remote theme
- **Content Structure**: 
  - `_posts/engineering/` - Technical blog posts about AI, Ruby, development
  - `_posts/ministry_online/` - Ministry and online outreach content
  - `_pages/` - Static pages (about, archives, 404)
  - `_data/navigation.yml` - Site navigation configuration
- **Assets**: Images stored in `assets/images/`, with source diagrams in `_source/assets/images/`
- **Generated Site**: Built to `_site/` directory

## Common Development Commands

### Local Development
```bash
# Install dependencies
bundle install

# Serve site locally (with auto-reload)
bundle exec jekyll serve

# Build site for production
bundle exec jekyll build

# Build with specific base URL (used in GitHub Actions)
bundle exec jekyll build --baseurl "/path"
```

### Content Management
- Blog posts use Jekyll's `_posts/` directory with date-filename format
- Posts are organized in subdirectories by category (`engineering/`, `ministry_online/`)
- Front matter includes layout, title, categories, tags, and other metadata
- Images should be placed in `assets/images/` and referenced with `/assets/images/filename`

## Configuration Files

- `_config.yml` - Main Jekyll configuration, theme settings, author info
- `Gemfile` - Ruby gem dependencies including Jekyll plugins
- `.github/workflows/jekyll.yml` - GitHub Actions workflow for automated deployment

## Deployment

The site automatically deploys to GitHub Pages via GitHub Actions when pushing to the `master` branch. The workflow:
1. Sets up Ruby 3.2.2 environment
2. Installs dependencies with bundle caching
3. Builds site with Jekyll
4. Deploys to GitHub Pages

## Content Guidelines

- Engineering posts focus on Ruby, AI development, prompt management, and technical insights
- Ministry posts cover online outreach, social media, podcasting, and digital ministry tools
- Use appropriate categories and tags for discoverability
- Include author profile and enable comments/sharing for posts