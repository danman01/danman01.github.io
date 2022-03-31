## how to install:
ensure dependencies installed on system [official docs](https://jekyllrb.com/docs/):
bundle


## how to write:

rake new_draft['title of draft']

then when it's ready...

rake post['title of draft'] 

moves it into the posts directory

jekyll build

builds all the files for the server

jekyll serve

serves up the site at :4000 to preview it

use git and git push ghpages master when you're ready to publish.

rake unpost['title of post'] to move from posts to draft

edit the meta data date to influence the url and date of a piece.
