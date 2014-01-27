---
layout: post
title: Adding categories to a Jekyll site
tags: [Jekyll, GitHub]
synopsis: One of my Jekyll-based blogs needed to have categories added and it took several goes to get them working. Now that it is it seems very clear how to do it.
---
Adding categories to a Jekyll blog isn't hard but there are a few things to remember to do. During my first attempts at adding categories I figured that they must work the same as tags and I approached the implementation on that basis. I was wrong though, they don't work the same way.

After adding categories you get some new urls in this form:

    http://yoursite.co.uk/categoryN

Posts which are assigned to this category will then have URLs with that as a prefix.

These could then be added to a header to allow users to view different categories of posts on your site.

Here's my solution.

#### Create the category file structure
For each category you add a subdirectory of your project root directory named after that category.

    /root
      /category1
      ...
      /categoryN

#### Add an index for each category
In each of these category directories you create an `index.html` which contains something like this:

    ---
    layout: base
    title:  Category Title
    ---
    {{ "{%" }} for post in site.categories.categoryN %}
       {{ "{%" }} include summary.html %}
    {{ "{%" }} endfor %}

Critical thing to remember is that the line `{{ "{%" }} for post in site.categories.category1 %}` has to refer to the directory name that the `index.html` is in. So in this case that would be "categoryN".

#### Create a post summary include
Notice in the `index.html` file there is an include of `summary.html`. That file looks like this:

    <article class="excerpt">
      <div class="meta-title">
        <h3 class="clear"> <a href="{{ post.url }}" title="{{ "{{" }} post.title }}">{{ "{{" }} post.title }}</a>
        <br/>
        <small>{{ "{{" }} post.date | date_to_long_string }}</small>
        {{ "{%" }} for tag in post.tags %}
          <a href="/tags/#{{ "{{" }} tag }}"><span class="badge">{{ "{{" }} tag }}</span></a>
        {{ "{%" }} endfor %}
       </h3>
      </div>
      <div class="post-content">
        <p>{{ "{{" }} post.content | strip_html | truncatewords: 100 }}</p>
      </div>
    </article>

So now what you have is an `index.html` which iterates over all the posts in the category. For each post it outputs the title, data and first 100 words of the content. 

#### Add "categories" to your posts
Now you can assign categories to each of your blog posts by adding this to the header:

    ---
    categories: [category1, ..., categoryN]
    ---

On the site I used this one I chose to only assign on category per post but there is nothing stopping you from assigning multiple categories per post.

#### Add some flair
Finally you could add some flair so that people can see what category a post is assigned to. Wherever you output a post title you add something like this in before it:

    {{ "{%" }} for category in post.categories %}
      {{ "{%" }} if category == 'pc' %}
         <span class="label label-primary"><i class="fa fa-wrench"></i></span>
      {{ "{%" }} elsif category == 'gaming' %}
         <span class="label label-warning"><i class="fa fa-gamepad"></i></span>
      {{ "{%" }} elsif category == 'running' %}
         <span class="label label-success"><i class="fa fa-trophy"></i></span>
      {{ "{%" }} endif %}
    {{ "{%" }} endfor %}

You can see the result of this on [ultraflynn.com](http://ultraflynn.com/) 