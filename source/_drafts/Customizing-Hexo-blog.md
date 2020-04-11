---
title: Customizing a Hexo blog
subtitle: Customizing a Hexo blog by applying blog and theme-level settings, and installing some useful Hexo plugins.
tags: [Hexo, Blog, ENF]
categories: [Hexo, Blog]
---
Step-By-Step Creating a Hexo blog post series:

1. [Installing Hexo blog and adding a new theme]()
2. **Customizing Hexo blog**
3. [Using Hexo blog - writing posts]()
4. [Deploying Hexo blog to GitHub Pages]()

<br/>

> This article is also available on [Exception Not Found](https://exceptionnotfound.net/customizing-hexo-blog/) blog

In the [first part]() of this series, we have generated an empty blog, we have installed the *clean-blog* theme, and applied it instead of the default one that came with an initially generated blog. At this moment, our blog source code is available in a [GitHub repository](https://github.com/vladimirvozar/hexo-blog-create) under commit with *commit_id = 778d366*.
(*git checkout 778d366*)

> A finished blog that we are building in this series is available [online](https://vladimirvozar.github.io/hexo-blog-create/), served on a GitHub Pages server. The source code can be seen on [this GitHub repository](https://github.com/vladimirvozar/hexo-blog-create).

### Overview ###
In this part, we will be modifying our Hexo blog by customizing some of the blog-level and theme-level settings. We will also install one useful Hexo plugin, and according to these settings, you should be able to continue with customizations to suit our needs.

Many of these customizations can be found on the *clean-blog* theme's [README file](https://github.com/klugjo/hexo-theme-clean-blog). Every well defined Hexo theme should have a README file with instructions about its settings and which plugins it supports.

### Deleting unneeded files ###
First, let's delete some files that we do not need. The *clean-blog* theme comes with support for several languages. And for each language, there is a separate *'.yml'* file that contains translations for that language. We can see these files if we open *'themes\clean-blog\languages'* folder. Assuming our blog is going to be in one language, delete all the files, except the one we need for our blog (choose some language). I have left *'en.yml'* and *'defaul.yml'* files.


### The look and feel of our blog ###
Run ```hexo server``` command again, and go to [localhost:4000](http://localhost:4000) address in the browser.

Notice the page elements and page content. We can see there are some links and pieces of information that we should change, or completely remove from our blog.

{% asset_img "initial_look.png" "Hexo initial page look" %}

*Clean-blog* theme has menu section that has links to the *Home*, *Archives*, *Tags* and *Categories* pages (other themes can initially come with different pages). Clicking on the *Tags* or *Categories* links will take us to an empty page. According to the theme documentation, *Tags* and *Categories* pages do not come automatically with the theme installation, and it is our job to create these pages. 

In the footer, there are several links related to *[clean-blog](https://github.com/klugjo/hexo-theme-clean-blog)* theme repository. We are going to remove these later. If you prefer to have these on your page, just leave it as is.

We only have one post for now - a *Hello World* post, without any tag or category assigned. By going to the [Hello World post link](https://vladimirvozar.github.io/hexo-blog-create/2020/03/hello-world/), we can see how our post pages are going to look. Also, notice the format of the URL address of the Hello World post.

### Customizing our blog ###
As stated earlier in the post, the best place to see how to customize any theme is to check the instructions in the [README file](https://github.com/klugjo/hexo-theme-clean-blog) of the theme.

I will list the changes that I have made to our newly created blog. These are some simple configurations of blog and theme-level settings.
After every change, check the effect of that change on the blog by restarting the Hexo server and refreshing browser page.

Changes in the blog-level *_config.yml* file:
- set the *title* and *subtitle* settings

- *'post_asset_folder'* is a setting that if set to *true*, each of our blog posts will have its dedicated asset folder. Assets are non-post files, such as images, CSS or JavaScript files that will be included in our blog post article. Set it to *true*. With asset folder management enabled, Hexo will create a folder every time we make a new post.

- change *'permalink'* setting to *'permalink: :year/:month/:title/'* (remove the *'/:day'* part). With this modification our post URLs will be slightly shorter.

- set the *'default_layout'* to be *'default_layout: draft'* (We will explain what drafts are in the next part of the series)

Changes in the theme-level *_config.yml* file:
- delete value from *'menu-title'* setting

- find or create some *'favicon.ico'* file that will be our blog favicon icon, and put it in the *\themes\clean-blog\source\img* folder

- set *'favicon: /hexo-blog-create/img/favicon.ico'*

- set *'index_cover: /hexo-blog-create/img/home-bg.jpg'*

Of course, if we want to make some bigger changes to our theme, we should investigate the *javascript and css* files of the theme, and make our changes there.

For example, some changes that I have made are:
- remove the link in the upper left corner of the blog home page by commenting out one line in the *'\themes\clean-blog\layout\_partial\menu.ejs'* file:
```
	<!-- <a class="navbar-brand" href="<%- config.root %>"><%- theme.menu_title || config.title || "" %></a> -->
```

- remove unnecessary informations from blog footer page (changes made in *'\themes\clean-blog\layout\_partial\footer.ejs'* file)

- increase/set the font-size of code blocks by modifying *'\themes\clean-blog\source\css\article.styl'* file
```
    .line
      height 20px
      font-size 15px
```

### Installing additional Hexo plugins ###
When generating the default blog, we already have installed some plugins. We can see the list of these plugins if we open *package.json* file inside our root(blog) folder:

```
  "dependencies": {
    "hexo": "^4.0.0",
    "hexo-generator-archive": "^1.0.0",
    "hexo-generator-category": "^1.0.0",
    "hexo-generator-index": "^1.0.0",
    "hexo-generator-tag": "^1.0.0",
    "hexo-renderer-ejs": "^1.0.0",
    "hexo-renderer-stylus": "^1.1.0",
    "hexo-renderer-marked": "^2.0.0",
    "hexo-server": "^1.0.0"
  }
```

Beside initially installed plugins, there are a bunch of other useful plugins that we can add to our blog. [Hexo Plugins](https://hexo.io/plugins/) is a place where we can find and explore Hexo plugins. We should choose our plugins according to which plugins are supported by our theme (*search* option, *sitemap*, *comments* support ...), and of course, we can add some of the useful plugins that we can use when writing posts.

One plugin that I find useful is a *hexo-browsersync* plugin. With this plugin, the changes we make (while writing posts) will be automatically synced, meaning we will not have to manually restart Hexo server and refresh the page in a browser
 
To install *hexo-browsersync* plugin, execute following command: 

``` bash
npm i -S hexo-browsersync
```

### Summary ###

At this moment, we have a nicely configured blog that is ready for use. The following is the list of tasks that we have accomplished in this article:
- removed some unneeded files from our theme
- customized blog-level settings
- customized theme-level settings
- installed additional plugins


In the next post, we will start writing posts, we will add *tags* and *categories* pages, and we will describe how to generate the content that will be deployed.
