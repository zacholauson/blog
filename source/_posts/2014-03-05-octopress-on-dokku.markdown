---
layout: post
title: "Octopress on Dokku"
date: 2014-03-05 14:40

---
Today I was playing around with Dokku, which is a 'Docker powered mini Heroku'. I initially wanted to use it to host my Joodo app because I was having issues deploying it on Heroku, but I ended up having issues on Dokku as well.

Even though I didnt get my Joodo app up on Dokku, I still wanted to deploy something on it, so I decided I would put my blog up. I figured it would be as easy as adding the git remote just like Heroku or Github pages, but when I deployed it just gave me a blank page. I found a git repo with someone who forked Octopress to work with Dokku, but since I already had a Octopress setup I didnt want to have to migrate just to test it out. I took a look at what he had done and played around with it and got it working.

First you need to move most of your gems out of development in your `Gemfile`.

Before:

{% codeblock lang:ruby %}
source "https://rubygems.org"

group :development do
  gem 'rake', '~> 0.9'
  gem 'jekyll', '~> 0.12'
  gem 'rdiscount', '~> 2.0.7'
  gem 'pygments.rb', '~> 0.3.4'
  gem 'RedCloth', '~> 4.2.9'
  gem 'haml', '~> 3.1.7'
  gem 'compass', '~> 0.12.2'
  gem 'sass', '~> 3.2'
  gem 'sass-globbing', '~> 1.0.0'
  gem 'rubypants', '~> 0.2.0'
  gem 'rb-fsevent', '~> 0.9'
  gem 'stringex', '~> 1.4.0'
  gem 'liquid', '~> 2.3.0'
  gem 'directory_watcher', '1.4.1'
end

gem 'sinatra', '~> 1.4.2'

{% endcodeblock %}

After:

{% codeblock lang:ruby %}

source "https://rubygems.org"

gem 'rake', '~> 0.9'
gem 'jekyll', '~> 0.12'
gem 'rdiscount', '~> 2.0.7'
gem 'pygments.rb', '~> 0.3.4'
gem 'RedCloth', '~> 4.2.9'
gem 'haml', '~> 3.1.7'
gem 'compass', '~> 0.12.2'
gem 'sass', '~> 3.2'
gem 'sass-globbing', '~> 1.0.0'
gem 'rubypants', '~> 0.2.0'
gem 'stringex', '~> 1.4.0'
gem 'liquid', '~> 2.3.0'
gem 'directory_watcher', '1.4.1'
gem 'thin'
gem 'sinatra', '~> 1.4.2'

group :development do
  gem 'rb-fsevent', '~> 0.9'
end

{% endcodeblock %}

Lastly, you need to include Public into git, so just remove it from your `.gitignore`.

{% codeblock %}

.bundle
.DS_Store
.sass-cache
.gist-cache
.pygments-cache
_deploy
sass.old
source.old
source/_stash
source/stylesheets/screen.css
vendor
node_modules

{% endcodeblock %}

Now your Octopress setup should work on Dokku.

I didnt end up switching to Dokku for hosting my Blog.
I dont really like how little control over my apps I have on it, but I didn't play with it too much so I may give it another shot later on.
