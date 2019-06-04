# Compete Summer Training

This is a repository containing source code for a Jekyll blog for Compete McGill's Summer 2019 Training.

This template is modified from: [hcz-jekyll-blog](https://github.com/codeasashu/hcz-jekyll-blog).

We use [Bootstrap-Material-Design](https://fezvrasta.github.io/bootstrap-material-design/), a bootstrap implementation of Google's Material Design, to define our site layout.

You can read more about the Jekyll generator [here](https://jekyllrb.com), to better understand how it uses awesome tools like Liquid Templating to generate static sites out of Markdown files. 

## Dependencies

We use some additional gems/plugins for added functionality:
- [jekyll-paginate-v2](https://github.com/sverrirs/jekyll-paginate-v2) - More complete pagination functionality, i.e. ability to paginate on multiple pages with different categories.
- [jekyll-gist](https://github.com/jekyll/jekyll-gist) - Ability to embed GitHub Gists into site. Jekyll 3's builtin [Rouge](https://jekyllrb.com/docs/liquid/tags/#code-snippet-highlighting) highlighting feature makes for more seamless integration, however.
- [jemoji](https://github.com/jekyll/jemoji) - Ability to embed GitHub-flavored emoji into Jekyll sites. 

## Contributing

Contributions are welcome. To do so simply:
- Fork the Repository from the top of the page.
- Implement your changes on your repository.
- Open a pull request into our development branch.

### Running Locally

To run the site locally, ensure you have Ruby (we deploy with 2.5.5) and bundler installed and execute

```
bundler install
```

To install the required dependencies. You can then simply execute 

```
bundler exec jekyll serve --baseurl=""
```

to run a local version of the site for preview.

> Note, if you are developing multiple Ruby projects with conflicting dependencies, using the Ruby Version Manager (rvm) is highly recommended. [See here](https://www.digitalocean.com/community/tutorials/how-to-use-rvm-to-manage-ruby-installations-and-environments-on-a-vps) for a quick guide to get you up to speed. 

### Peculiarities of Our Code

There are some peculiarities in the way that we have implemented our site functionality. We aim to list them here in hopes that it speeds up your contribution process:

#### Schedule

The Schedule data that is rendered in the Index page lives in [`_data/schedule.json`](_data/schedule.json). The number field tells us which Problem Set and Solution number the generator should search for and link, if they exist. 

#### Problem Sets and Solutions

Problem Set posts live in [`problemsets/_posts`](problemsets/_posts). The title must be specified in the YAML front matter. For example:
```
---
layout: post
title: Problem Set 1
categories: 
  - linear data structures
  - non-linear data structures
image: logo.png
---
```
> The image field tells us which image file in the [`static/thumbnails`](static/thumbnails) folder to use as a thumbnail.

The corresponding Solution Set lives in [`solutions/_posts`](solutions/_posts) and needs to have **the same title as its Problem Set** in the YAML front matter. For example, the solution set corresponding to the above Problem Set would have:
```
---
layout: post
title: Problem Set 1
categories: 
  - linear data structures
  - non-linear data structures
image: logo.png
---
```

#### Adding your Solutions

Open the `solutions/_posts` folder and find the corresponding solution set. Underneath the heading for the problem for which you want to contribute to, insert your code (below any existing solutions) as follows:
```
{% highlight c++ %}
//Your code here
{% endhighlight %}
```

Or equivalentally for Java:
```
{% highlight java %}
//Your code here
{% endhighlight %}
```
