---
layout: post
title: "Chapter 05 - It looks kinda ugly isn't it?"
date: 2014-03-17 09:20:28 +0100
published: false
comments: false
categories:
---

# Dizájn - Bootstrap

Most, hogy működik az app, már foglalkozhatunk a külsőségekkel is kicsit. Adjunk egy kis dizájnt neki, hogy ránézésre is profi oldalunk legyen!

## 1. lépés: állítsuk be az app kinézetét

Használjuk a bootstrap.min.css fájlt, hogy kicsit könnyedebb stílusú legyen az oldal.
Nyissuk meg az `app/views/layouts/application.html.erb` a szövegszerkesztőnkben, és a 

	<link rel="stylesheet" href="http://railsgirls.com/assets/bootstrap.css">

sort írjuk át a következőre:

	<link rel="stylesheet" href="http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap.min.css">

A `app/assets/stylesheets/application.css` fájlban pedig a

	body { padding-top: 100px; }

sorban írjuk át a 100px méretet 60px-re.
Végül pedig töröljük az –– app/assets/stylesheets/scaffolds.css.scss –– , hogy az alap Rails stílust most ne használjuk.
Frissítsük az oldalunkat a [http://localhost:3000/ideas](http://localhost:3000/ideas) címen. Nem sok minden változott, de azért valami már kezd látszani.

## 2. lépés: Navigáció

Mivel az új ötlet hozzáadása az egyik legfontosabb funkciója az app-nek, csináljunk neki egy gombot a menüsorba, hogy mindig egyből elérjük!
Nyissuk meg a `app/views/layouts/application.html.erb` fájlt a szerkesztőben, és a

	<li class="active"><a href="/ideas">Ideas</a></li>

sor alá tegyük be a következőt:

	<li ><%= link_to 'New Idea', new_idea_path %></li>

## 3. lépés: Csinosítsuk ki az ötletlistát

Tegyük profi kinézetűvé az 0tletlista oldalunkat végre! Ehhez a táblázatok helyett div-eket fogunk használni.

Nyissuk meg a `app/views/ideas/index.html.erb` fájlt a szerkesztőben, és az egészet írjuk át a következőre:

	<h1>Listing ideas</h1>
	<% @ideas.in_groups_of(3) do |group| %>
	  <div class="row">
	    <% group.compact.each do |idea| %>
	      <div class="span4">
	        <%= image_tag idea.picture_url, :width => '100%' if idea.picture.present?%>
	        <h4><%= link_to idea.name, idea %></h4>
	        <%= idea.description %>
	      </div>
	    <% end %>
	  </div>
	<% end %>

Frissítsük az oldalunkat! Most már egész csinosan néz ki. Ha a `New idea` gombra kattintunk, egyből tudunk is hozzáadni pár ötletet, mivel minél tübb tartalmunk van az oldalon, annál jobban néz ki.
Manapság az egyik fő elv a dizájnban úgyis az, hogy a tartalom a legjobb dekoráció :)

## 4. lépés: Formázzuk meg az 0tlet részleteiről szóló oldalt is

Kattintsunk az ötlet címére, és meg tudjuk nézni a hozzá tartozó részleteket. Ez most még csak a Rails által generált scaffold-ot mutatja, úgyhogy szépítsünk rajta.

Nyissuk meg a `app/views/ideas/show.html.erb` fájlt a szeresztőben, és írjuk át az egészet a következőre:

	<p id="notice"><%= notice %></p>
	<div class="row">
	  <div class="span9">
	    <%= image_tag(@idea.picture_url, :width => "100%") if @idea.picture.present? %>
	  </div>
	  <div class="span3">
	    <p><b>Name: </b><%= @idea.name %></p>
	    <p><b>Description: </b><%= @idea.description %></p>
	    <p>
	      <%= link_to 'Edit', edit_idea_path(@idea) %> |
	      <%= link_to 'Destroy', @idea, confirm: 'Are you sure?', method: :delete %> |
	      <%= link_to 'Back', ideas_path %>
	    </p>
	  </div>
	</div>

Frissítsük űjra az oldalunkat a [http://localhost:3000/ideas](http://localhost:3000/ideas) címen. Ugye, hogy jobb lett?