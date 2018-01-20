require "rubygems"
require "bundler/setup"
require "stringex"

## -- Config -- ##

posts_dir       = "_posts"    # directory for blog files
drafts_dir       = "_drafts"  # directory for blog files
new_post_ext    = "markdown"  # default new post file extension when using the new_post task
new_page_ext    = "md"

#############################
# Create a new Post #
#############################

# usage rake new_post
desc "Create a new post in #{posts_dir}"
task :new_post, :title do |t, args|
  title = get_stdin("Enter a title for your post: ")
  filename = "#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  author = get_stdin("Enter author name: ")
  tags = get_stdin("Enter tags to classify your page (space separated): ")
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: #{title.gsub(/&/,'&amp;')}"
    post.puts "author: #{author}"
    post.puts "published: false"
    post.puts "tags: #{tags}"
    post.puts "---"
  end
end

#############################
# Create a new Draft #
#############################

# usage rake new_draft
desc "Create a new draft in #{drafts_dir}"
task :new_draft, :title do |t, args|
  title = get_stdin("Enter a title for your post: ")
  filename = "#{drafts_dir}/#{title.to_url}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  author = get_stdin("Enter author name: ")
  tags = get_stdin("Enter tags to classify your page (space separated): ")
  puts "Creating new draft: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: #{title.gsub(/&/,'&amp;')}"
    post.puts "author: #{author}"
    post.puts "published: false"
    post.puts "tags: #{tags}"
    post.puts "---"
  end
end

#############################
# Publish Draft #
#############################

# usage rake new_draft
desc "Publish draft in #{posts_dir}"
task :publish_draft, :filename do |t, args|
  next if ARGV.size < 2
  file = ARGV.last
  filename = File.basename file
  post_filename = "#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{filename}"
  if File.exist?(post_filename)
    abort("rake aborted!") if ask("#{post_filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{post_filename}"
  open(post_filename, 'w') do |post|
    post.puts File.read(file)
  end
  puts "Deleting draft #{filename}"
  File.delete(file)
end

#############################
# Create a new Page         #
#############################

# usage rake new_page
desc "Create a new page"
task :new_page, :title do |t, args|
  title = get_stdin("Enter a title for your page: ")
  filename = "#{title.to_url}.#{new_page_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  tags = get_stdin("Enter tags to classify your page (space separated): ")
  puts "Creating new page: #{filename}"
  open(filename, 'w') do |page|
    page.puts "---"
    page.puts "layout: page"
    page.puts "permalink: /#{title.to_url}"
    page.puts "title: \"#{title}\""
    page.puts "tags: #{tags}"
    page.puts "---"
  end
end

#############################
# Start server              #
#############################
desc "Serve blog in local machine"
task :serve do
  system "bundle exec jekyll serve --drafts"
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end

def ask(message, valid_options)
  if valid_options
    answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
  else
    answer = get_stdin(message)
  end
  answer
end
