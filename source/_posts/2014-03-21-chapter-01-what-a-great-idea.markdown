---
layout: post
title: "Chapter 01 - What a great idea!"
date: 2014-03-21 09:15:28 +0100
published: false
comments: false
categories:
---

project init + idea scaffold

<!-- more -->

rails new ideapad -T

cd ideapad
edit gemfile

add:
  gem 'execjs'
  gem 'therubyracer', platforms: :ruby
run bundle install

rails generate scaffold Idea title:string description:text

rake db:migrate

root to: "ideas#index"
and delete public/index.html

run rails s and play around a bit
