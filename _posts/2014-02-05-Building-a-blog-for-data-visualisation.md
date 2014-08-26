---
layout: postng
title: Building a blog for data visualisation - Part 1 of 2
date: 2014-02-07 00:00:00
author: MichaelGuimet
category: meta
tags: [meta-blog,meta-visualisation]
description: "How I built a blog for data visualisation using GitHub Pages, Jekyll and Bootstrap. Resources used included." 
giants: [{name: G. K. Chesterton, url: "http://en.wikiquote.org/wiki/G._K._Chesterton#All_Things_Considered_.281908.29"},
	{name: Phil Haack, url: "http://haacked.com/"},
	{name: Blind Gaenger, url: "http://blog.blindgaenger.net"},
	{name: Eric Jones, url: "http://erjjones.github.io"},
	{name: Anna Debenham, url: "http://24ways.org/authors/annadebenham/"},
	{name: Everyone ar GitHub, url: "https://github.com"},
	{name: Everyone at Jekyll, url: "http://jekyllrb.com"},
	{name: Everyone at Bootstrap, url: "http://getbootstrap.com/" },
	{name: John Gruber, url: "https://daringfireball.net/projects/markdown/"},
	{name: Everyone at Shopify's liquid, url: "https://github.com/Shopify/liquid"},
	{name: Mike Bostock , url: "http://bost.ocks.org/mike/"}]
sources: [{}]
---

Welcome to datAdventures! Join our expedition in search of fun and knowledge through the art of data visualisation!

Now, every expedition requires preparation. There are many tasks to do before setting sail: plan your route, test your equipment, pack your luggage, buy provisions... You can consider these an inconvenience or, paraphrasing G.K. Chesterton, an adventure! This document explores how the datAdventures expedition was prepared.

*Disclaimer:*

   - *All code in this post is by its respective authors as detailed below. Practically none is original.*
   - *We will be using GitHub Pages "for users" as opposed to "for projects"; GitHub Pages for projects follow a different methodology.*
   - *We will assume a general familiarity with, but not coding knowledge of, web languages ([HTML][1], [CSS][2], [JS][3], [Markdown][4] and [Liquid][5]), [git][6] and [GitHub][7]. Take into account we are web development beginners and we managed just fine.*

###1. Setting up GitHub Pages (on a subdomain)
The [official guide][8] can help us set up a page quickly and easily. However, we want to setup datAdventures on a subdomain and need specific instructions. Luckily, *[Blind Gaenger][9]* has an excellent post with the solution. We will include the basic instructions and all the code but make sure to check out *Blind Gaenfer*'s post for the full story! *(Note "subdomain" stands for your choice of name for your subdomain and "username" refers to your GitHub username)*

First of all, we create a [new repository][10] on [GitHub][8] called "subdomain". Then on our local terminal we issue the following commands.

    mkdir subdomain
    cd subdomain
    git init
    touch README.md
    git add README.md
    git commit -m "Initial commit: README"
    git remote add origin git@github.com:username/subdomain.git
    git push origin master
    git symbolic-ref HEAD refs/heads/gh-pages
    rm .git/index
    git clean -fdx
    echo "Hello, World!" > index.html
    git add .
    git commit -a -m "Initial gh-page"
    git push origin gh-pages

We should now be able to check our page on http://username.github.io/subdomain (may take up to 10 min). 
Then, *Blind Gaenger* suggests to reference the gh-pages banch to a subdir of the master branch.

    git checkout master
    git submodule add -b gh-pages git@github.com:username/subdomain.git
    git commit -m "Added gh-pages as submodule of master"
    git push
    git submodule init

We now have our page up on GitHub.

From here we can move on to installing [Jekyll][11]. However, before we move on, let us look into how to "publish" (push to GitHub) the blog we will create in the following steps. To test this, and actually see the result, we have changes the content of the index.html file before issuing the following commands.

    cd subdomain
    jekyll build
    git add .
    git commit -m "Message to gh-pages"
    git push origin gh-pages
    git commit -a -m "Message to master"
    git push origin master

Now this may seem a lot of commands just to publish the site so, for convenience, we went along and created a bash script. We called it [pcommit][12].

    #!/bin/bash
    if [ -z "$1" ]; then
       echo "Please pass a comment for the commit! Use: ./pcommit \"comment\""
    else   
      cd ~/datadventures
      echo ""
      echo "1. Proceeding to build the site..."
      jekyll build
      echo ""
      echo "2. Committing gh-pages"
      cd _site
      git add .
      git commit -m "$1"
      git push origin gh-pages
      echo ""
      echo "3. Committing site master"
      cd ..
      git commit -a -m "$1"
      git push origin master
    fi

We now have a working page on GitHub and the workflow to publish our blog on GitHub Pages.

###2.Installing Jekyll
The official docs for installing [Jekyll on GitHub Pages][13] are excellent. The `github-pages` gem sets the GitHub servers environment on your local machine. You need to have Ruby 1.9.3 or 2.0.0 installed before you issue the following commands *(on Ubuntu there is a confusing version system where the 1.9.1 Ruby package actually links to Ruby 1.9.3, you have been warned)*.

	cd subdomain
	gem install bundler
	echo "gem 'github-pages'" >> Gemfile
	bundle install

That is all we need for a non-configured local Jekyll installation. We can check out how it looks locally with the command `bundle exec jekyll serve --watch` or publish it with the pcommit script. At this stage we will stay local.

###3. Configuring Jekyll 
For the next steps, we incorporated instructions from *[Anna Debenham][14]* and *[Eric Jones][15]*. 
Before we dive into the configuration, it is convenient to understand the [directory structure for a Jekyll blog][16]. Very bluntly put it sums up to the following.

  - `_includes`: This folder keeps bits of html and liquid code that can be imported into other documents.
  - `_layouts`: This folder keeps your web page layouts in html that will be mostly composed of the files in _includes.
  - `_posts`: This folder keeps your posts in markdown.
  - `index.html`: This is your home page.
  - `_site`: This folder keeps the site Jekyll generates with all the information above. You shouldn't edit any of the files here. 

Good new, there is only one file to configure Jekyll! This is the [_config.yml][17] we are using. But you will probably just need the following at this stage.

    name: title
    subtitle: subtitle
    markdown: redcarpet
    pygments: true
    baseurl: /subdomain

With this our Jekyll is configured and we are ready to design the page.

###4. Designing with Bootstrap
Using [Bootstrap][18], these days, seems to be frowned on by not only web designers but also web developers. However, it is a simple yet flexible tool that is ideal for our blog. We will not go into detail on how to use bootstrap but they have a good [getting started guide and fantastic examples][19]. After setting the overall design, make sure to edit your css to fully personalise your page.

For datAdventures we decided for a sidebar and a simplistic design and raided [Phil Haack's][20] and *Eric Jones'* GitHubs for liquid code. You are free to visit the [datadventures GitHub repository][21] and see how our blog is designed.

###5. Creating content
If the blog is going to be text only then we are set. We can create our posts in markdown in the `_posts` directory and run the `pcommit` script to build and publish your new blog.

However, datAdventures is a blog about data visualisation. More specifically about visualisation using [d3.js][22]. D3 is an excciting network and If you have never heard of it then we invite you to discover [Mike Bostock][23]'s creation. It's fantastic. 

The most simple way to include d3.js graphs on your blog is to include the following code in the layout for the pages that will contain graphs.

    <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

We are now ready to use the d3 framework.

This brings us to the end of part 1 of the series. While you wait for part 2 you can check this blog's source files at the [datadventures repository on GitHub][21]. Feel free to take what you need and use it.

In part 2 we will look into adding social links, share buttons (with AddThis), comments (with intenseDebate or Disqus, still deciding), analytics (with Google Analytics), category and tag pages, an RSS feed (with feedburner) and how to host js locally.

#####Happy adventuring, fellow explorers!
 
[1]: https://developer.mozilla.org/en-US/docs/Web/HTML
[2]: https://developer.mozilla.org/en-US/docs/Web/CSS
[3]: https://developer.mozilla.org/en-US/docs/Web/JavaScript
[4]: http://daringfireball.net/projects/markdown/
[5]: https://github.com/Shopify/liquid/wiki
[6]: http://git-scm.com/
[7]: https://github.com/
[8]: http://pages.github.com/
[9]: http://blog.blindgaenger.net/generate_github_pages_in_a_submodule.html 
[10]: https://github.com/repositories/new
[11]: http://jekyllrb.com
[12]: https://github.com/magp/datadventures/blob/master/pcommit
[13]: https://help.github.com/articles/using-jekyll-with-pages
[14]: http://24ways.org/2013/get-started-with-github-pages/
[15]: http://erjjones.github.io/blog/How-I-built-my-blog-in-one-day/
[16]: http://jekyllrb.com/docs/structure/
[17]: https://github.com/magp/datadventures/blob/master/_config.yml
[18]: http://getbootstrap.com/
[19]: http://getbootstrap.com/getting-started/
[20]: http://haacked.com/
[21]: https://github.com/magp/datadventures
[22]: http://d3js.org/
[23]: http://bost.ocks.org/mike/
