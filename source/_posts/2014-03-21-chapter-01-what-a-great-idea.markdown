---
layout: post
title: "Chapter 01 - What a great idea!"
date: 2014-03-21 09:15:28 +0100
published: false
comments: false
categories:
---

Projekt inicializálás, scaffold generálás.

<!-- more -->


Futtassuk le a következő parancsokat:
    $ rails new ideapad -T
    $ cd ideapad
    $ bundle install
    $ rails generate scaffold Idea title:string description:text
    $ rake db:migrate


Adjuk hozzá az alábbi sort a `config/routes.rb` fájlhoz:

``` ruby config/routes.rb
  root to: "ideas#index"

```

Töröljük ki a `public/index.html` fájt, majd indítsuk el a szervert az alábbi paranccsal:

    $ rails server
