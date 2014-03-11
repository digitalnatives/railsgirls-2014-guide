---
layout: post
title: "Chapter 02 - It's alive"
date: 2014-03-20 09:17:10 +0100
published: false
comments: false
categories:
---

heroku deploy

<!-- more -->

register a heroku account - should be done prior
gem install heroku

heroku login (generate ssh key if necessary)

config/application.rb : config.assets.initialize_on_precompile = false

Gemfile:
gem 'sqlite3', group: [:development, :test]
group :production do
  gem 'thin'
  gem 'pg'
end

(bundle install --without production)

git config --global user.email "you@example.com"
git config --global user.name "Your Name"

git init
git add .
git commit -m 'init'
git status =>
  On branch master
  nothing to commit, working directory clean

heroku create =>
  Creating stark-everglades-5886... done, stack is cedar
  http://stark-everglades-5886.herokuapp.com/ | git@heroku.com:stark-everglades-5886.git
  Git remote heroku added

heroku keys:add

git push heroku master
heroku run rake db:create

heroku ps:scale web=1

heroku apps =>
  === My Apps
  stark-everglades-5886

=> open stark-everglades-5886.heroku.com
