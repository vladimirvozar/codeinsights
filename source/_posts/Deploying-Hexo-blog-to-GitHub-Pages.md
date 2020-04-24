---
title: Deploying Hexo blog to GitHub Pages
tags:
  - Hexo
  - Blog
  - ENF
categories:
  - Hexo
  - Blog
subtitle: >-
  Deploying Hexo blog by simply pushing code to the remote repository. Using the
  GitHub Pages option to serve a website from the "docs" folder.
date: 2020-04-24 17:25:06
---

Step-By-Step Creating a Hexo blog post series:

1. [Installing Hexo blog and adding a new theme](https://www.codeinsights.net/2020/04/Installing-Hexo-blog-and-adding-a-new-theme/)
2. [Customizing a Hexo blog](https://www.codeinsights.net/2020/04/Customizing-Hexo-blog/)
3. [Using Hexo blog - writing posts](https://www.codeinsights.net/2020/04/Using-Hexo-blog-writing-posts/)
4. **Deploying Hexo blog to GitHub Pages**

<br/>

> This article is also available on [Exception Not Found](https://exceptionnotfound.net/deploying-hexo-blog-to-github-pages/) blog.

By following instructions in the previous three articles in our mini-series, we should have a working blog that is ready for use. And we have also created our first post with Hexo. Our blog is now configured, but only for local usage. In this part, we will add some more configurations that will allow easy deployment of our blog.

> A finished blog that we are building in this series is available [online](https://vladimirvozar.github.io/hexo-blog-create/), served on a GitHub Pages server. The source code can be seen on [this GitHub repository](https://github.com/vladimirvozar/hexo-blog-create).

### Overview ###
The last part of our Hexo series will be about deploying our blog to GitHub Pages. There are several possible approaches to this subject. Our goal is to have blog files under source control on a GitHub repository and being served on a GitHub Pages server.

### GitHub Pages ###
From the GitHub Pages documentation:

>GitHub Pages is an easy hosting solution for websites with HTML, CSS, and JavaScript files. You can create two types of sites on GitHub Pages:

>1. User Pages - if you’re hosting a personal website or an index of your projects, or something that represents you as a whole. You can only have one user page per account. The URL for this page will be http://yourusername.github.io/.

>2. Project Pages - if you’re hosting one project, of which you have many. You can create multiple project pages per account. The URL for this page will be http://yourusername.github.io/projectname/.

- If we want to go with *User Pages*, the repository where our generated website content will be would have to be named *yourusername.github.io*. And it will have to be on the *master* branch. 
- Alternatively, if we go with *Project Pages*, our website content can reside in any repository (usually named after our blog), but it needs to be on a *gh-pages* branch. 
- Beside two approaches just mentioned, there is a third option - GitHub Pages can serve website content from a *docs* subfolder of our repository (on the *master* branch). In my opinion, this is the simplest way to deploy our blog, and we will go with this option.

As we can see, GitHub pages have specific rules. More details about configuring a publishing source for our GitHub Pages site can be found on [official GitHub Pages documentation](https://help.github.com/en/github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site).

### Serving blog from */docs* folder ###

Hexo generates a website content into a *public* folder. And we want our generated content to be in the *docs* folder.
Before we change our blog publishing folder, execute the ```hexo clean command```. This command deletes the publishing folder. To create or update it again, run ```hexo generate```.

As we said earlier, we want our website content to be served from *docs* subfolder of our repository (on the master branch). To achieve this scenario, there are a couple of things we have to configure in the blog-level _config.yml file:
- set the *public_dir: docs*
- set the *url: http://yourusername.github.io*
- set the *root: /projectname*

In the theme-level *_config.yml* file:
- set the *index_cover: /projectname/img/home-bg.jpg*
- set the *favicon: /projectname/img/favicon.ico*

In the above settings, *index_cover* and *favicon* are needed, because when deploying to GitHub Pages, the base address is *http://yourusername.github.io*, not the *http://yourusername.github.io/projectname/*.

- run ```hexo generate``` to generate our *publishing* folder with our blog content
- push the repository to GitHub
- in the GitHub repository, go to *Settings* tab, and scroll down to *GitHub Pages* section
- as the *Source*, select the option: *master branch /docs folder*

### GitHub repository settings ###

Every GitHub repository under its *Settings* has a GitHub Pages section that contains several configurations. As the source of our blog, we have already chosen the *master branch /docs folder* option.

Selecting a source option will not make any changes to our source code. It just instructs the GitHub Pages server where to look for our blog content.
But, selecting a Jekyll theme, or setting up a custom domain are two actions that do make a change to our blog source code. 

When I've tried for the first time to see the deployed blog (served on GitHub Pages), I was getting *404 Error Page*. Apparently, the selection of the Jekyll theme was mandatory. Not sure why this option would be mandatory, but in case you are facing the same situation, here is the thing to keep in mind:

When you select some Jekyll theme, GitHub will automatically create a commit in which a file named *_config.yml* is added to a *docs* folder.
The content of this file would be (e.g. for a Jekyll *Cayman* theme selected):

```
theme: jekyll-theme-cayman
```

After making the above-mentioned changes, our blog should be configured, and we should be able to see it up and running on *http://yourusername.github.io/projectname/*.

This way we have both our blog source code as well as generated blog content (*docs* folder) in one GitHub repository, on one (*master*) branch. 

### A CNAME file ###

GitHub Pages supports custom domains, so the websites hosted by GitHub Pages can be accessed via DNS names. Configuring *A* and *CNAME* records depend on your DNS provider.

>*CNAME* - uppercase file name without a file extension. *CNAME* stands for Canonical Name, and a *CNAME* record must always point to another domain name, never directly to an IP address

If we want a custom domain assigned to our blog, we need to have a *CNAME* file inside our *publishing(docs)* folder. The contents of the *CNAME* file should be the domain of our website, e.g. *www.yourwebsite.com*.

If we enter our custom domain in repository settings, GitHub commits a *CNAME* file to a docs folder.

When we update a *docs* folder, the *CNAME* file will be deleted. So, create a *CNAME* file in the source folder of our blog, and regenerating *docs* folder will pick it up, and place it in the *docs* folder.

When we set a custom domain on a GitHub repository, it may take some time before our domain gets connected with the repository. After that, we probably will need to update some of the blog settings: paths, references to possible background images, headers, favicon ...

### Summary ###

This post covered the following tasks:
- configuring blog deployment from *master branch /docs* folder
- explaining GitHub Pages settings of GitHub repository
- avoiding possible issues with GitHub Pages Theme and/or custom domain

Hexo Framework is simple to use, but if we just take it for granted, we will hit some walls. I hope we have clarified some of the possible Hexo issues, and I hope that you found some valuable information in this series.
