---
layout: post
title: "Chapter 03 - Meet Devise"
date: 2014-03-19 09:17:54 +0100
published: false
comments: false
categories:
---

devise integration

<!-- more -->

add devise to gemfile
bundle install --without production

rails generate devise:install

flash messages in app/views/layouts/application.html.erb
  <p class="notice"><%= notice %></p>
  <p class="alert"><%= alert %></p>

rails generate devise User

rake db:migrate

add before_filter :authenticate_user! to ideas_controller

start server, examine results

set devise password length to 6 in config/initializers/devise.rb

examine rake routes
add current user email to the root page
add sign out and edit profile link to root page

optionally: check forgot password with letter opener
add letter_opener_web to gemfile
bundle install --without production
setup routes:
  if Rails.env.development?
    mount LetterOpenerWeb::Engine, at: "/letter_opener"
  end
set mail delivery method in development.rb
config.action_mailer.delivery_method = :letter_opener_web
config.action_mailer.default_url_options = { host: 'localhost' }

request forgot password mail -> check http://localhost:3000/letter_opener

(should we try to set it up in heroku too? - maybe not)
