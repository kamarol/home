---
author: "Hugo Authors"
title: "Adventures in Creating a Budget Dev Blog in 2023"
date: 2023-04-27
description: "Like Pulling Teeth, but With More Fun!"
tags: [""]
thumbnail: /dentist-pulling-teeth.png
---

After years of procrastination, I've decided to create a blog.

## Why Create a Blog?

There will come a time in a developer's career where they decide to have a personal blog, to showcase and indoctrinate their ideas to the world.

Besides, blogging and coding are practically the same thing:

You write a bunch of text on a computer and hope it makes sense. The only difference is that with coding, you get to add in some fancy brackets and semicolons to really make things interesting!

## Why Github Pages?

- Cost-effective. It's free
- Eternal (until Microsoft goes bankrupt)
- Developer brain be like: Git = Good. I can save drafts in a different branch. Git blame myself on my typo mistakes, etc.

Git based also means text based, and without using an RDBMS like MySQL in Wordpress. Whatever hot new technology or framework comes out, text never goes out of style 😎


## Failed attempt at running Jekyll on M1 Mac

My first stumbling block was trying to get Jekyll to work on my M1 Mac. Some Jekyll themes require native ruby dependencies that only compiles on X86 machines. Now I have a few obvious choices moving forward:

1. Reinstall Ruby using Rosetta 2
1. Run Jekyll in an X86 emulated docker container
1. Use a different static site generator


At first glance: 

Option #1 seems like the easiest option. However, this might require reinstalling a lot of the existing tools on my M1 Mac, such as Homebrew, chruby, terminal to run in Rosetta mode, which might break other things, as well as affect the battery life since native ARM versions are always more efficient than emulated X86 versions.


So, I decided to go with option #2... If that doesn't work, I'll go with option #3.

### Running Jekyll X86 Docker Emulation with live reload

Writing and development aren't so different - both benefit from fast feedback and Live reload makes that possible -- I've never done a live reload project using Docker before, so off to experimenting we go!

I'm quite familiar with Node, I try to create a test project that would contain a live reloadable dev environment inside Docker.

Dockerfile
```
FROM node:14

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD [ "npm", "run", "dev" ]
```

package.json
```
{
  "name": "my-node-app",
  "version": "1.0.0",
  "description": "A Node.js application",
  "main": "index.js",
  "scripts": {
    "start": "node lib/ndex.js",
    "dev": "nodemon lib/index.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.7"
  }
}
```

index.js
```
console.log("Hello :0")
```

Updating the console.log to see if it triggers the live reload:
![Placeholder](/live-reload-node.png)


After wasting half a day on creating it, only to discover that the Jekyll's live reload requires Inode support, which is not available in an X86 emulated docker container running on an M1 Mac.


Sure, I could opt for nodemon instead of Jekyll's live reload, but where's the fun in that? I'm already 1/3 of the way into this blogpost, and I need to introduce some drama to keep the readers engaged 🤫 So I'll continue with choice #3 by choosing another static site generator!

## Choosing an alternative Static Site Generator

The first thing I did when researching is to always google "reddit + X", so I did: `reddit static site generator`, `reddit jekyll vs`. Because of SEO gaming and everything else. Reddit is a safe haven for honest opinions.

{{< youtube 48AOOynnmqU >}}


Looking at reddit's suggestions: 11ty, Hugo, Gatsby, etc.

1. Gatsby is a React framework, however, I think it's too heavy for my blog (-1 points). I use React daily for my work, and I don't want to make my blog also the same as "work" 😂
2. 11ty written in Javascript (+1 points)
3. Hugo runs on Go, but it seems like there is a lot of templates (1+ points), and doesn't take that much to install on M1 Macs. And since it is a single binary, there's less chance a dependency will break.

So now 11ty and Hugo is tied with the points. But since the points are made up anyways, I've decided to go with Hugo because of the huge amount of themes available from https://themes.gohugo.io/


![Placeholder](/agile-meme.jpg)



## Installing Hugo and applying themes

- Installing Hugo is as easy as downloading the native ARM binary from https://gohugo.io/installation/macos/
- Applying a theme (https://github.com/apvarun/blist-hugo-theme), which is just adding a git submodule inside an empty git project, then copying the exampleSite folder into the root directory. 

Why git submodule? Seems like that's what Hugo recommends https://gohugo.io/getting-started/quick-start/ 

```
hugo new site quickstart
cd quickstart
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke themes/ananke
echo "theme = 'ananke'" >> config.toml
hugo server
```

which makes sense, since I can update the theme by just doing a git pull on the submodule.

## Setting up Github Actions for deployment

Just copy pasting the github actions yml from here: https://gohugo.io/hosting-and-deployment/hosting-on-github/

I've skipped some parts such as registering a domain name (10USD per year), setting up the https using cloudflare (free), hosting images (putting it on github, but there is a limit, etc) but that's pretty much it!