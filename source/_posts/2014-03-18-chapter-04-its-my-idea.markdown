---
layout: post
title: "Chapter 04 - It's my idea!"
date: 2014-03-18 09:19:42 +0100
published: false
comments: false
categories:
---

idea ownership - connecting users to ideas

<!-- more -->

add user connection to ideas

rails generate migration add_user_id_to_ideas
rake db:migrate

add has_many and belongs_to in models

save current_user to idea on create in ideas_controller

show user email in idea index/show

(optionally:
delete all previous ideas in rails console:
Idea.destroy_all)

check it out locally

deploy to heroku and check it out in pairs
git add .
git commit -m 'connect users and ideas'
git push heroku master
heroku run rake db:migrate

(optionally:
heroku run rails c
Idea.destroy_all
exit with ctrl+d)
