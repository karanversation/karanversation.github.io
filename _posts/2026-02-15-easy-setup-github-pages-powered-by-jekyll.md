---
layout: post
title: "Easy setup Github Pages powered by Jekyll"
categories: [programming]
date: 2026-02-15
---

A guide to setting up a Jekyll-based blog on GitHub Pages and testing it locally.

## Setting up GitHub Pages with Jekyll

### Step 1: Create a GitHub Repository

1. Go to GitHub and create a new repository
2. Name it `yourusername.github.io` (replace `yourusername` with your GitHub username)
3. Make it public (required for free GitHub Pages)
4. Initialize with a README (optional)

### Step 2: Choose a Jekyll Theme

GitHub Pages supports several Jekyll themes. You can:

**Option A: Use a built-in theme (easiest)**

Create a `_config.yml` file in your repository root:

```yaml
title: Your Blog Title
description: Your blog description
theme: minima
```

Then create an `index.md` or `index.html` file as your homepage.

**Option B: Use a custom theme**

1. Fork or clone a Jekyll theme repository
2. Customize the `_config.yml` file
3. Push to your `username.github.io` repository

### Step 3: Basic Jekyll Structure

Create these essential files and directories:

```
your-repo/
├── _config.yml          # Site configuration
├── _posts/              # Blog posts (YYYY-MM-DD-title.md)
├── index.html           # Homepage
├── _layouts/            # HTML layouts (optional)
└── assets/              # CSS, images, etc. (optional)
```

### Step 4: Create Your First Post

Create a `_posts` directory and add a file named `YYYY-MM-DD-your-post-title.md`:

```markdown
---
layout: post
title: "Your First Post"
date: 2026-02-15
---

Your content here...
```

### Step 5: Enable GitHub Pages

1. Go to your repository **Settings**
2. Navigate to the **Pages** section
3. Under "Source", select the branch (usually `main` or `master`)
4. Click **Save**
5. Your site will be available at `https://yourusername.github.io` in a few minutes

---

## Setting up Local Environment to Test Pages

### Prerequisites

- Ruby (version 2.6 or higher)
- RubyGems
- Bundler

### Step 1: Install Ruby

**On macOS:**

```bash
# Using Homebrew
brew install ruby

# Or using rbenv (recommended)
brew install rbenv
rbenv init
rbenv install 3.2.3
rbenv local 3.2.3
```

**On Linux:**

```bash
sudo apt-get install ruby-full
```

**On Windows:**

Download and install Ruby from [rubyinstaller.org](https://rubyinstaller.org/).

### Step 2: Install Bundler

```bash
gem install bundler
```

### Step 3: Create Gemfile

In your repository root, create a `Gemfile`:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3"
gem "github-pages", group: :jekyll_plugins
```

### Step 4: Install Dependencies

```bash
bundle install
```

### Step 5: Run Jekyll Locally

```bash
bundle exec jekyll serve
```

Or with auto-reload:

```bash
bundle exec jekyll serve --livereload
```

Your site will be available at `http://127.0.0.1:4000`.

### Step 6: Build for Production

```bash
bundle exec jekyll build
```

This creates a `_site` directory with the generated static site.

---

## Troubleshooting

**Ruby version mismatch**
- Use `rbenv` or `rvm` to manage Ruby versions
- Ensure your local Ruby version matches GitHub Pages requirements

**Bundle install fails**
- Run `bundle update`
- Check Ruby and Bundler versions

**Jekyll serve errors**
- Clear Jekyll cache: `rm -rf .jekyll-cache _site`
- Restart the Jekyll server

For more information:
- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Jekyll Themes](https://jekyllthemes.io/)
