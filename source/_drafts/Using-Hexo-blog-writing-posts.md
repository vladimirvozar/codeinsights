---
title: Using Hexo blog - writing posts
tags: [Hexo, CodeInsights, Blog]
categories: [Hexo, CodeInsights, Blog]
---
Step-By-Step Creating a Hexo blog post series:

1. [Installing Hexo blog and adding a new theme]()
2. [Customizing Hexo blog]()
3. **Using Hexo blog - writing posts**
4. [Deploying Hexo blog to GitHub Pages]()
<br/><br/>

### Overview ###
In the previous post, we have configured our blog, but we still need to add pages for *tags* and *categories*. After that, the next step is to start using our blog. We will go through the process of creating a new *post* and *draft* pages, and explain the workflow for using these two types of page layouts together. We will see where these pages are being placed when created, and what happens in our blog file structure when we generate and deploy our blog.


### Generating *tags* and *categories* pages ###
Adding *tags* and *categories* pages is documented in the [README file](https://github.com/klugjo/hexo-theme-clean-blog#tags-page) of our theme.
All we need to do is to run ```hexo new page "tags"``` and ```hexo new page "categories"``` commands.

These commands will create two files:
- *\source\categories\index.md*
- *\source\tags\index.md*

Edit the front matter of newly created pages and set appropriate page type.

For *Tags*:
```
title: All tags
type: "tags"
```

For *Categories*:
```
title: All categories
type: "categories"
```

*NOTE: [Front-matter](https://hexo.io/docs/front-matter.html) is a block of YAML or JSON at the beginning of the file that is used to configure settings for your writings. Front-matter is terminated by three dashes when written in YAML or three semicolons when written in JSON.*

Run ```hexo server``` again, and you can check our new pages by going to the *Tags* and *Categories* links in the menu.

### Generating *public* folder ###
If you open file explorer of our blog, there should be a files and folders structure like on the following screenshot:

{% asset_img "blog_folder_without_public.png" "Hexo folder structure" %}

Up until now, we haven't created any new posts (or drafts) yet. But we do already have one post - a *Hello world* post that initially came with Hexo blog creation. This post, as well as any new one that we are going to create, is placed under *\source\_posts* folder. But, as you can see, this is only the *.md* (Markdown) file of the post article. No styles, no images or any other HTML files here (not even an *index.html* file).

So, the next thing we need to do is to *generate* the content that will be our actual site.
To generate our blog site, Hexo has a command:

```
hexo generate
```

Run this command from your bash terminal, and you will see log informations about which files were generated with this command.
Now, open your file explorer again, and notice the new folder, called *public* inside the blog root folder. This is the folder that holds the whole content of our blog. 

By default, Hexo generates a website content into a *public* folder. There is a *public_dir* setting in *_config.yml* file where this configuration is set. It can be set to some other folder, and we will explain a scenario (in the next post) why would we want to publish to some other folder.

### Writing - Posts vs Drafts ###
There are three default page layouts in Hexo: post, page, and draft. Files created by each of them are saved to a different path (*source/_posts*, *source/_drafts* and *source/_pages*). We have already used *page* layout for creating our *tags* and *categories* pages. *Post* and *draft* layouts are two layouts we are going to use for post creation.

Drafts are the same as posts. Actually, drafts are just unpublished posts that we are still *working on*. Those are just files that contain the article of our future post in the Markdown format.

*NOTE: [Markdown](https://guides.github.com/features/mastering-markdown/) is a way to style text on the web.*

Common workflow to follow when writing posts would be:
- create a new *draft* page
- write your article content (Markdown content)
- when done with writing, publish your *draft* (make it a *post*)
- generate website/blog content

If you remember, in the previous article, when we were configuring blog-level settings, we have set *default_layout* to have a value of *draft*.
This means, that by executing ```hexo new name-of-the-post``` command, we would get a new draft page generated.

Detailed instructions about writing new posts can be found in [official Hexo documentation](https://hexo.io/docs/writing.html).

So, let's see the above steps in action. 

Create a new *draft* page by running:
```
hexo new "First Hexo post"
```

This command creates a new draft page, a markdown file, inside the *source/_drafts* folder. Also, a new asset folder with the same name is created.
This folder is meant to hold assets (images, additional files ...) referenced from our post/draft article.

Now, place following markdown content inside the *First-Hexo-post.md* file:

```
Hi there.
This is our first **Hexo** generated post.

Example of an image inside the post content:
{% asset_img "example.png" "This is an example image" %}
```

I have placed one image in the *source/_drafts/Hello* folder and referenced it from the draft file like:

```
{% asset_img "example.png" "This is an example image" %}
```

To see the changes that we have made in our draft post, we need to start Hexo server with ```hexo server --draft``` command.
Without the *--draft* part, we would not be able to see the drafts on our blog (only posts are visible).

Let's say we have our post article ready. The next step is to move our draft content from *source/_drafts* to *source/_posts* folder.
This is automatically achieved by running:
```
hexo publish "First Hexo post"
```

In short, that would be some common scenario for creating new posts.

### Summary ###

In this part we have:
- created *tags* and *categories* scaffold pages
- generated *public* folder
- explained post creating workflow by first using drafts


The last part of this series will explain how to organize and configure our blog for easy deploying, and we will deploy our blog to GitHub pages.
