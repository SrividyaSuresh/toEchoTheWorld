---
title: "Creating this GitHub Pages Blog"
date: 2025-07-31
draft: false
---

# Journey of creating this Blog (with AI)

I’ve been wanting to start a blog for a while now. Somthing that integrates my love for tech with everything else in my life. I wanted to build it from scratch, make it my own. Cos UI isn't my strongest skill, I kept putting it off for another day, a day when I'll be awesome at HTML, CSS, JS and everything else in between. 

It's been a long time, guys. Last week I just wanted to get something up and running, quick and dirty as they say. So I did what anyone with a little bit of skill could do, I used ChatGPT to help me code. And here we are. Let's start the walkthrough. 

&nbsp;

## Why GitHub Pages?

GitHub Pages lets you host a static page directly from its repo. It is free, has version control, ease of deployment and whats a better place to showcase tech projects. It also supports static site generators like Jekyll and Hugo, so you can choose from a wide array of pre-built themes. 
For me, the main pull was to get a better understanding of GitHub, its Actions, CI/CD workflows and hosting. 

### Choosing the right Static Site Generator

I decided to give in and use a static site generator.[^1] Here it means that I'd choose a theme, a design, drawn up by other smarter people. I would get a certain amount of freedom to customize, but at my skill level, I knew this would be no Ship of Theseus. 

My expectation of the theme was minimal, modern, and delicate. I tried [Jekyll](https://jekyllrb.com/docs/themes/) first, but just couldn't pick a style. I was constricted to the few they had, and I knew I'd have to make massive changes to the themes to be satisfied with it. I went as far as deploying a draft website, and that convinced me to move on. 
I tried [Hugo]((https://themes.gohugo.io)) the next day, and I was delighted. It has a lot more themes, and is varied in taste. I picked the [BearBlog](https://themes.gohugo.io/themes/hugo-bearblog/) theme by Jan Raasch.

At this step, you can choose to use Hugo with a third-party hosting service. I wished for everything under a single stack, so was happy with GitHub pages[^2].

&nbsp;

## The codes

1. Pick a nice theme from [Hugo](https://themes.gohugo.io)

2. On Mac, from your terminal, install Homebrew and then Hugo 
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install hugo
```
On Windows, from your Command Prompt, install Hugo
```
winget install Hugo.Hugo.Extended
```

3. Decide what you want your blog to be named and create a basic outline. Let's consider YellowPages as the name. 
```
hugo new site yellowPages
cd yellowPages
```

4. Now is the time to add in your theme. I'll use the Bear theme as the example here. 
```
git init
git submodule add https://github.com/janraasch/hugo-bearblog themes/bear
```

5. Open this folder on VSCode or some such code editor. Edit the config.toml file to include your github username, repo/page name, and the main menu tabs. If you already have a design in mind, create accordingly, else let's first get the page deployed, you can customize it to your liking later.
```
baseURL = "https://<your-github-username>.github.io/yellowPages/"
languageCode = "en-us"
title = "Yellow Pages"
theme = "bear"
publishDir = "public"

[params]
  author = "Your Name"
  description = "Blogs and such"

[menu]

  [[menu.main]]
  identifier = "home"
  name = "HOME"
  url = "/"
  weight = 1

  [[menu.main]]
  identifier = "blog"
  name = "BLOG"
  url = "/blog/"
  weight = 2

  [[menu.main]]
  identifier = "random"
  name = "RANDOM"
  url = "/random/"
  weight = 3
```

6. The Home page should be added as `layouts/index.html` file with the following format
```
{{ define "main" }}
  <main class="home">
    Welcome to my blog!
  </main>
{{ end }}
```

The About page can be created directly as `content/About.md` file with the following format
```
+++
title = "About Me"
date = "2025-07-31"
menu = "main"
+++
```
You can add your content under this. 

7. Adding posts can be done in a few ways.

i. Via Hugo inbuilt commands. 
```
hugo new blog/firstpost.md
```
You can edit the above created file on vscode
```
---
title: "First Post"
date: 2025-07-31
draft: false
---

This is the content of the **first blog post**. 
```

ii. Manually 
Inside the folder called `content`, create subfolders with the identifier names in `config.toml`. Add Markdown (.md) files inside those folders to add new posts. 
You can also just use the default posts option. If you do, then update posts as the identifier under your menu options in config.toml. Following commands given for clarity.
```
cd content
mkdir blog
mkdir random
cd blog
vi firstpost.mdru/v
```
Add content in the following format
```
---
title: "First Post"
date: 2025-07-31
draft: false
---

This is the content of the **first blog post**. 
```

8. Let us add the commands for using Github Actions Workflow. 
Create .github/workflows/hugo.yml
```
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches:
      - main  # or 'master' if you're using that

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v3
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.125.0'  # Change to your version if needed

      - name: Build site
        run: hugo --minify

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: gh-pages
```

9. Commit and Push into GitHub
```
git add .
git commit -m "Initial commit for GitHub Pages setup"
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
git branch -M main   # or master if you prefer
git push -u origin main
```

Right after this step, enable write access to allow GitHub Actions to push build site into gh-pages branch.

Repo → Settings → Actions → General → Workflow permissions → Read and write permissions select

10. Enable GitHub Pages

Go to your repo → Settings → Pages
Set:
- Branch: gh-pages
- Folder: / (root)
Your site will go live on https://<your-username>.github.io/<your-repo-name>/

11. You can check the workflow on Actions maintab. 

12. After any edits, run `hugo server` on local machine to see the changes you've made. This opens a localhost setup that updates the blog view as changes are made to your content. 

&nbsp;

## Understanding Hugo flow

1. Content

The markdown files for posts and pages are to be edited inside /content. The structure would look something like this.
```
content/
  blog/
    first-post.md
  about.md
```

To edit the CSS, create a copy of `themes/your-theme/layouts/partials/style.html` page into `layouts/partials/style.html`. Edit into this new folder, not the original theme folder (which would be a submodule, so your commits would move into the original author's repo). Hugo will now use your version and ignore the one inside the theme. Commit only your local layouts/... files. 

2. Build 

When Hugo build is run either locally via `hugo server` or via GitHub Actions,

i. Hugo reads 
- `content` folder
- theme’s layouts (`themes/your-theme/layouts/`) 
- configurations in `config.toml` or `config.yaml`.

ii. Converts Markdown into HTML and wraps with the theme's HTML and CSS. 

iii. Updates these ready-to-serve files in `public` folder. 

3. Final site

The final site is present under public folder. So this contains HTML, CSS, JS and images. 
Never edit the public folder[^3], and make sure its added into your `.gitignore` file. This is overwritten everytime the site is rebuilt, locally or via GitHub Actions.

4. Deployment

The final site present in public folder is pushed by GitHub into gh-pages branch. This is the branch name GitHub looks for, so do not change this or many any edits to this branch directly. All edits are pushed to (or merged into) the main branch. 

## And here we are
So, my first post here is about the journey of making this blog.
There was a lot of thinking, and a lot more debugging. But I come away better skilled at UI. And the writing bit? I'll get there. Stick around!

&nbsp;
&nbsp;

[^1]: I know high-minded folks would not approve, customizing everything gives you freedom like no other. But you do you, right. 
[^2]: The greens this would add to my profile wouldn't hurt either. 
[^3]: Learnt this the hard way.
