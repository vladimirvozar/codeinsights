---
title: Deploying Hexo blog to GitHub Pages
tags: [Hexo, CodeInsights, Blog]
categories: [Hexo, CodeInsights, Blog]
---
Step-By-Step Creating a Hexo blog post series:

1. [Installing Hexo blog and adding a new theme]()
2. [Customizing Hexo blog]()
3. [Using Hexo blog - writing posts]()
4. **Deploying Hexo blog to GitHub Pages**
<br/><br/>

### Overview ###
The last part of our Hexo series will be about deploying our blog to GitHub Pages. There are several possible approaches to this subject. Our goal is to have blog files under source control on a GitHub repository and being served on a GitHub Pages server.

### GitHub Pages ###
From the GitHub Pages documentation:

*GitHub Pages is an easy hosting solution for websites with HTML, CSS, and JavaScript files. You can create two types of sites on GitHub Pages:*

*1. User Pages - if you’re hosting a personal website or an index of your projects, or something that represents you as a whole. You can only have one user page per account. The URL for this page will be http://yourusername.github.io/*.

*2. Project Pages - if you’re hosting one project, of which you have many. You can create multiple project pages per account. The URL for this page will be http://yourusername.github.io/projectname/*

If you want to go with *User Pages*, the repository where your generated website content will be would have to be named *yourusername.github.io*. And it will have to be on the *master* branch. 
Alternatively, if you go with *Project Pages*, your website content can reside in any repository (usually named after your blog), but it needs to be on a *gh-pages* branch. 
Beside two approaches just mentioned, there is a third option - GitHub Pages can serve website content from a *docs* subfolder of your repository (on the *master* branch). In my opinion, this is the simplest way to deploy our blog, and we will go with this option.

As you can see, GitHub pages have specific rules. More details about configuring a publishing source for your GitHub Pages site can be found on [official GitHub Pages documentation](https://help.github.com/en/github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site).

### Serving blog from */docs* folder ###

Hexo generates a website content into a *public* folder. We will change our *publishing* folder to be a *docs* folder.
Before we do that, execute the ```hexo clean``` command. This command deletes currently configured *publishing* folder (*public* folder).

GitHub Pages can serve website content from a *docs* subfolder of our repository (on the *master* branch). 

To achieve this scenario, there are a couple of things we have to configure in the blog-level *_config.yml* file:
- set the *public_dir: docs*
- set the *url: http://yourusername.github.io*
- set the *root: /projectname*

In the theme-level *_config.yml* file:
- set the *index_cover: /projectname/img/home-bg.jpg*
- set the *favicon: /projectname/img/favicon.ico*

Above settings, for *index_cover* and *favicon* are needed, because when deploying to GitHub Pages, the base address is *http://yourusername.github.io*, not the *http://yourusername.github.io/projectname/*.

- run ```hexo generate``` to generate our *publishing* folder with our blog content
- push your repository to GitHub
- in your GitHub repository, go to *Settings* tab, and scroll down to *GitHub Pages* section
- as the *Source*, select the option: *master branch /docs folder*

### GitHub repository settings ###

Every GitHub repository under its *Settings* has a GitHub Pages section that contains several configurations. As the source of our blog, we have already chosen the *master branch /docs folder* option.

Selecting a source option will not make any changes to our source code. It just instructs the GitHub Pages server where to look for our blog content.
But, selecting a Jekyll theme, or setting up a custom domain are two actions that actually do make a change to our blog source code. 

When I've tried for the first time to see the deployed blog (served on GitHub Pages), I was getting *404 Error Page*. Apparently, the selection of the Jekyll theme was mandatory. Not sure why this option would be mandatory, but in case you are facing the same situation, here is the thing to keep in mind:

When you select some Jekyll theme, GitHub will automatically create a commit in which a file named *_config.yml* is added to a *docs* folder.
The content of this file would be (e.g. for a Jekyll *Cayman* theme selected):

```
theme: jekyll-theme-cayman
```

Even if we pull this commit to our local repository, this could cause us some troubles in the future. The problem is when we use ```hexo clean``` and ```hexo generate``` commands, our *docs* folder is deleted and recreated, and we lose our *_config.yml* file inside it. The way to go to avoid this situation is to create exactly the same *_config.yml* file in the *source* folder of our blog, which will be automatically copied to *docs* folder when running ```hexo generate```.

After making the above mentioned changes, our blog should be configured. Push the code and we should be able to see it up and running on *http://yourusername.github.io/projectname/*.

This way we have both our blog source code as well as generated blog content (*docs* folder) in one GitHub repository, on one (*master*) branch. 

### A CNAME file ###

GitHub Pages supports custom domains, so the websites hosted by GitHub Pages can be accessed via DNS names. Configuring *A* and *CNAME* records depend on your DNS provider.

***CNAME* - uppercase file name without a file extension. *CNAME* stands for Canonical Name, and a *CNAME* record must always point to another domain name, never directly to an IP address**

If you want a custom domain assigned to your blog, you need to have a *CNAME* file inside your *publishing(docs)* folder. The contents of the *CNAME* file should be the domain of your website, e.g. *www.yourwebsite.com*.

Similar to selecting the Jekyll theme, if you enter your custom domain in repository settings, GitHub commits a *CNAME* file to a *docs* folder.
Again, when you update *docs* folder, *CNAME* file will be deleted. So, create a *CNAME* file in the *source* folder of your blog, and regenerating *docs* folder will pick it up, and place it in the *docs* folder.

When you set a custom domain on a GitHub repository, it may take some time before your domain gets connected with the repository. After that, you probably will need to update some of the blog settings: paths, references to possible background images, headers, favicon ...

### Summary ###

This post covered the following tasks:
- configuring blog deployment from *master branch /docs* folder
- explaining GitHub Pages settings of GitHub repository
- avoiding possible issues with GitHub Pages Theme and/or custom domain

Hexo Framework is simple to use, but if you just take it for granted, you will definitely hit some walls. I hope I have clarified some of the possible Hexo issues, and I hope that you found some valuable information in this series.

