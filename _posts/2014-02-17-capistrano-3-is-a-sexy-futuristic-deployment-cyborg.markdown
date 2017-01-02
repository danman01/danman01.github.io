---
layout: post
title: "Capistrano 3 is a sexy futuristic deployment cyborg"
date: 2014-02-17 11:47:39 -0500
comments: true
categories: rails
tags: server, rails, capistrano
---

I recently got a server up and running in just a few hours time. Yea it's
probably time to go for something like Chef or Vagrant, but redoing the setup each time is a
nice way to force-learn new tools. And practicing Unix command line skills is a major turn on for the ladies, or so they tell me.

(Notes - here's some posts that got me through the server setup, as for
some reason I still don't have it committed to memory. This one is great for [Capistrano 3:](https://medium.com/p/ba896a142ac), and this server setup post is almost spot on (but don't muck with the capistrano part at the end...): [rails, postgresql, nginx, capistrano server setup](https://coderwall.com/p/yz8cha)


One major hurdle was realizing I was no longer in the old world of
Capistrano 2, but instead in the far off future with Capistrano 3, which
is a complete rewrite.  When it's all said and done, Capistrano 3 is a
sexy deployment cyborg that you will want to take home to meet the
parents.

So down to the nuts and bolts:

Capistrano 3 has extracted many things out into separate gems, so you
only include what you need.  This makes it apparent that this is not
just a tool for rails deployment, but useful for many server deploy
scenarios.  You will want to require everything you need in the Capfile.
I am doing a vanilla rails 4 app, so my requires reflect using Rails,
bundler, setup, deploy and RVM.

My Capfile

```
# Load DSL and Setup Up Stages
require 'capistrano/setup'

# Includes default deployment tasks
require 'capistrano/deploy'

# require everything for rails
require 'capistrano/rails'



# Includes tasks from other gems included in your Gemfile
#
# For documentation on these, see for example:
#
#   https://github.com/capistrano/rvm
#   https://github.com/capistrano/rbenv
#   https://github.com/capistrano/chruby
#   https://github.com/capistrano/bundler
#   https://github.com/capistrano/rails
#
require 'capistrano/rvm'
# require 'capistrano/rbenv'
# require 'capistrano/chruby'
require 'capistrano/bundler'
# require 'capistrano/rails/assets'
# require 'capistrano/rails/migrations'

# Loads custom tasks from `lib/capistrano/tasks' if you have any defined.
Dir.glob('lib/capistrano/tasks/*.cap').each { |r| import r }

#load 'deploy/assets'
#load 'config/deploy'
```

Moving to config/deploy.rb:

This is where you set variables and write tasks if you do not want to
place them in the newly minted lib/capistrano/tasks/ directory.  Note that I added in a line to set linked_files so that my app_name/shared folder holds things that persist across code releases.  Capistrano is a cyborg with strong emotions and will get angry if those files are not present when deploying. I had to scp my files from my local machine to the shared/config directory
on the server after the deploy halted the first time.  This made
Capistrano calm and pleasant to be around.

Also note the new syntax to read in a previously `set` value:
`fetch(:var_name)`

```
# config valid only for Capistrano 3.1
lock '3.1.0'

set :application, 'matchgogo'
set :user, "deploy"
set :port, 22

set :repo_url, "git@bitbucket.com:danman01/#{fetch(:application)}.git"

# Default branch is :master
# ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }

# Default deploy_to directory is /var/www/my_app
set :deploy_to, "/home/#{fetch(:user)}/apps/#{fetch(:application)}"
set :deploy_via, :remote_cache
set :use_sudo, false

# Default value for :scm is :git
# set :scm, :git

# Default value for :format is :pretty
# set :format, :pretty

# Default value for :log_level is :debug
# set :log_level, :debug

# Default value for :pty is false
# set :pty, true

# Default value for :linked_files is []
set :linked_files, %w{config/database.yml config/application.yml}
set :linked_dirs, %w{bin log tmp}

# Default value for linked_dirs is []
# set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

# Default value for default_env is {}
# set :default_env, { path: "/opt/ruby/bin:$PATH" }

# Default value for keep_releases is 5
# set :keep_releases, 5

set :keep_releases, 3

namespace :deploy do

  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      # Your restart mechanism here, for example:
      # execute :touch, release_path.join('tmp/restart.txt')
    end
  end

  after :publishing, :restart

  after :restart, :clear_cache do
    on roles(:web), in: :groups, limit: 3, wait: 10 do
      # Here we can do anything such as:
      # within release_path do
      #   execute :rake, 'cache:clear'
      # end
    end
  end

end
```

One more thing, my Unicorn hooks are not in there, but will likely end
up to be some sort of restart unicorn call to the init.d script. I tried
the capistrano-unicorn gem in the past, and it conflicts a little with
an init.d script plus I doubt it's ready for capistrano 3. I'll look
into it and report back.


Next, Capistrano 3 automatically includes the stages add-on.  You will
have to specify `cap production deploy` or `cap staging deploy` now.
Get used to it.

My deploy/production.rb

```
# Simple Role Syntax
# ==================
# Supports bulk-adding hosts to roles, the primary
# server in each group is considered to be the first
# unless any hosts have the primary property set.
# Don't declare `role :all`, it's a meta role
role :app, %w{deploy@192.241.242.75}
role :web, %w{deploy@192.241.242.75}
role :db,  %w{deploy@192.241.242.75}, primary: true 
# not sure if primary: true is needed / correct in capistrano 3, but doesn't seem to harm anything.

# Extended Server Syntax
# ======================
# This can be used to drop a more detailed server
# definition into the server list. The second argument
# something that quacks like a hash can be used to set
# extended properties on the server.
# server 'example.com', user: 'deploy', roles: %w{web app}, my_property: :my_value

# you can set custom ssh options
# it's possible to pass any option but you need to keep in mind that net/ssh understand limited list of options
# you can see them in [net/ssh documentation](http://net-ssh.github.io/net-ssh/classes/Net/SSH.html#method-c-start)
# set it globally
#  set :ssh_options, {
#    keys: %w(/home/rlisowski/.ssh/id_rsa),
#    forward_agent: false,
#    auth_methods: %w(password)
#  }
# and/or per server
# server 'example.com',
#   user: 'user_name',
#   roles: %w{web app},
#   ssh_options: {
#     user: 'user_name', # overrides user setting above
#     keys: %w(/home/user_name/.ssh/id_rsa),
#     forward_agent: false,
#     auth_methods: %w(publickey password)
#     # password: 'please use keys'
#   }
# setting per server overrides global ssh_options

set :rails_env, "production"

```

And last, take a peek at my Gemfile. It's something that I'm proud of.

```
# Use unicorn as the app server
gem 'unicorn'

# Use Capistrano for deployment

gem 'capistrano-rails', '~> 1.1'
gem 'capistrano', '~>3.1'
gem 'capistrano-bundler', '~> 1.1.2'
gem 'capistrano-rvm'
```

What, you want to see something else? Alright shows over, go home!
