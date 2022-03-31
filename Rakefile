require "rubygems"
require "bundler/setup"
require "uri"

## -- Misc Configs -- ##
posts_dir       = "_posts"
drafts_dir       = "_drafts"


def _build_post(dir, t, args)
  if args.title
    title = args.title
  else
    title = gets("Enter a title for your post: ")
  end
  escaped_title = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  raise "### You haven't set anything up yet. First create a #{dir} directory." unless File.directory?(dir)
  # creates directory, and all parent directories
  #mkdir_p "#{source_dir}/#{posts_dir}"
  post_ext    = "markdown"
  filename = "#{dir}/#{Time.now.strftime('%Y-%m-%d')}-#{escaped_title}.#{post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "tags: "
    post.puts "---"
  end
end

# usage rake new_post[my-new-post] or rake new_post['my new post'] or rake new_post (defaults to "new-post")
desc "Begin a new post in #{posts_dir}"
task :new_post, :title do |t, args|
  _build_post(posts_dir, t, args)
end

desc "Begin a new draft in #{drafts_dir}"
task :new_draft, :title do |t, args|
  if args.title
    title = args.title
  else
    title = gets("Enter a title for your post: ")
  end
  escaped_title = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  raise "### You haven't set anything up yet. First create a _drafts directory." unless File.directory?(drafts_dir)
  filename = "#{drafts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{escaped_title}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new draft: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "tags: "
    post.puts "---"
  end
end

desc "Prepare draft for post"
task :post, :title do |t, args|
  if args.title
    title = args.title
  else
    title = gets("Enter the title of your draft: ")
  end
  escaped_title = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  regex = Regexp.new(/(\d{4}-\d{2}-\d{2}-#{escaped_title}.#{new_post_ext})/)
  filedir = "#{drafts_dir}/*"
  thefile = Dir[filedir].select {|x| x=~ regex}
  if !thefile.empty? 
    puts "moved draft #{thefile} to posts"
    draft=thefile.first
    `mv #{draft} "_posts/#{$1}"`
    # TODO update date
  else
    puts "cannot find file named #{title}!"
  end
end

desc "Move post to draft"
task :unpost, :title do |t, args|
  if args.title
    title = args.title
  else
    title = gets("Enter a title for your post: ")
  end
  escaped_title = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  regex = Regexp.new(/(\d{4}-\d{2}-\d{2}-#{escaped_title}.#{new_post_ext})/)
  postsdir = "#{posts_dir}/*"
  thefile = Dir[postsdir].select {|x| x=~ regex}
  if !thefile.empty? 
    puts "moved post #{thefile} to drafts"
    post=thefile.first
    `mv #{post} "_drafts/#{$1}"`
  else
    puts "cannot find file named #{title}!"
  end
end
