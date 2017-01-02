---
layout: post
title:  "Blogging with Jekyll"
date:   2017-01-02 10:41:13 -0500
categories: jekyll update
---
Jekyll is a blogging platform for developers. What that means is, there's more that you have to do to create a post than simply clicking 'Post'. This is a good thing, because developers want more control, and want to feel like they're writing software while writing prose. It's a bad thing, because since the GUI isn't maintained for us, we have to remember how to even use the system, and since we're not professional writers (usually), we take long breaks between posts, thus forgetting how to even write a post.

This is my documentation on how to even use my jekyll set up. It's like turning the ignition to a car, but first you have to connect a few wires so the ignition is getting the write signals from the rest of the car.

How I compose and publish blog posts using Jekyll:

I've created a Rakefile to hold some basic tasks so I don't have to generate a timestamp and frontmatter for each post. That Rakefile lives in the root of my [github pages repo](https://github.com/danman01/danman01.github.io).

`rake new_draft['title of draft']`

then when it's ready...

`rake post['title of draft']` 

moves it into the posts directory

`jekyll build`

builds all the files for the server

`jekyll serve`

serves up the site at :4000 to preview it

use git and `git push ghpages master` when you're ready to publish.

`rake unpost['title of post']` to move from posts to draft

edit the meta data date to influence the url and date of a piece.

Taken from the latest jeykll welcome blog post: 

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
