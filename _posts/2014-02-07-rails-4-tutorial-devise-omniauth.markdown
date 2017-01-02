---
layout: post
title: "Rails 4 Tutorial with Devise and Omniauth"
date: 2014-02-07 22:08:50 -0500
comments: true
categories: rails 
---

I'm going to run you through this very basic setup for a start Rails
app.  It features Rails 4.1 and user authentication with Devise and Omniauth. Devise handles many things related to creating user accounts within rails and is modular,  so you only include what you need. There's many plugins to allow Devise to do fancy things like work with invitations.  Omniauth is used to provide oauth with a 3rd party oauth provider, like Facebook or Soundcloud.

First things first: make sure you are using rbenv or rvm. I'm partial to
rvm. 

```rvm list rubies```
will tell you which version of Ruby you are using, and

```rvm gemset show```
will tell you which gemset you are using.  I have a default rails 4
gemset, but want to make a new one for rails 4.1.

```rvm gemset create rails-4-1```
should do the trick. View more at [the rvm gemset page](http://rvm.io/gemsets)

Now that you have rails installed (check which version with ```rails
-v```), go ahead and create a new project. If you want to use Rspec
testing framework as opposed to the default test unit, make sure to add
this gem to your Gemfile:

```'rspec-rails'``` and then run ```bundle install```

I'm going to install the postgresql gem (add to Gemfile: ```'pg'```)and specify that as the database as well:

```rails new myApp -d postgresl```

Oh Lawd, writing an entire tutorial for each command is daunting. Ok fast lane...from there you will install Devise, an Omniauth-provider gem of your choice
(I'm using Soundcloud), and then you will need to the create some sort
of omniauth login-or-create user flow.  I always refer back to the
Railscast episode on Devise and Omniauth if I need a starting point.
And I found I needed a starting piont for just about every app I create.
So, to not keep repeating the same steps over and over, here's my starter app that I can reuse (and so can you):

[rails4-devise-omniauth-soundcloud-example-app](https://github.com/danman01/rails4-devise-omniauth-soundcloud)

The example app contains a complete devise and omniauth-soundcloud
integration, with all the extra needed to implement devise and omniauth.
I needed to handle multiple omniauth
providers, so moved the omniauth information into a separate Identity
class.
