---
layout: post
title: "Chapter 03 - Meet Devise"
date: 2014-03-19 09:17:54 +0100
published: false
comments: false
categories:
---

# Devise integráció

Nyissuk meg a `Gemfile`-t, és adjuk hozzá a következő sort:

	gem devise

Utána pedig futtassuk a terminálban a következőt:

	bundle install --without production

Ezután indítsuk újra a szerverünket.

## 1. lépés: A devise beállítása az app-ben

A terminálba írjuk be, hogy:

	rails g devise:install

## 2. lépés: Konfiguráljuk!

Nyissuk meg a szerkesztőben a `config/environments/development.rb` fájlt, és adjuk hozzá a következő sort az `end` szó előtt:

	config.action_mailer.default_url_options = { :host => 'localhost:3000' }

Most nyissuk meg a `app/views/layouts/application.html.erb` fájlunkat, és a `<%= yield %>` fölé írjuk be ezt:

	<% if notice %>
	  <p class="alert alert-notice"><%= notice %></p>
	<% end %>
	<% if alert %>
	  <p class="alert alert-error"><%= alert %></p>
	<% end %>

## 3. lépés: Állítsuk be a User modellt

A terminálba kell most beírnunk, hogy:

	rails g devise user
	rake db:migrate

## 4. lépés: Az első felhasználó...

Most, hogy már mindent beállítottunk, beregisztrálhatjuk az első felhasználónkat! Ehhez (ha a szerver fut) a [http://localhost:3000/users/sign_up](http://localhost:3000/users/sign_up) címre kell mennünk, és megadni a szükséges felhasználói adatokat.

## 5. lépés: Linkek a regisztrációhoz és bejelentkezéshez

Szeretnénk, ha a látogatók is be tudnának regisztrálni az oldalunkra, így csináljunk a navigációba linket hozzá!
Nyissuk meg a szerkesztőben a 'app/views/layouts/application.html.erb' fájlt, és a 

	<ul class="nav">
  		<li class="active"><a href="/ideas">Ideas</a></li>
	</ul>

rész után adjuk hozzá a következőket:

	<p class="navbar-text pull-right">
	<% if user_signed_in? %>
	  Logged in as <strong><%= current_user.email %></strong>.
	  <%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> |
	  <%= link_to "Logout", destroy_user_session_path, method: :delete, :class => 'navbar-link'  %>
	<% else %>
	  <%= link_to "Sign up", new_user_registration_path, :class => 'navbar-link'  %> |
	  <%= link_to "Login", new_user_session_path, :class => 'navbar-link'  %>
	<% end %>

Adjuk a következő kódot a 'flash messages in 'app/views/layouts/application.html.erb'-hez:
	
	<p class="notice"><%= notice %></p>
	<p class="alert"><%= alert %></p>

Végül még állítsuk be, hogy automatikusan a bejelentkező oldalra legyenek irányítva a látogatók, mielőtt bármilyen ötlethez nyúlhatnának.
Ehhez most a 'app/controllers/application_controller.rb' fájlba kell beleírnunk a 'protect_from_forgery with: :exception' rész után, hogy

	before_action :authenticate_user!

Az 'ideas_controller' fájlhoz pedig azt, hogy:

	before_filter :authenticate_user!

És készen is vagyunk! Jelentkezgessünk ki-be az oldalon, ugye milyen jó?

Beállíthatjuk még a jelszó hosszát a 'config/initializers/devise.rb' részen, mondjuk 6-ra.

# Egy másik gem

## Az elfelejtett jelszó problémája

Az előző részben látotthoz hasonlóan adjuk a 'letter_opener_web'-t is hozzá a 'gemfile'-hoz!
Majd jöhet a szokásos terminál parancs:

	bundle install --without production

Állitsuk be az útvonalakat:

	if Rails.env.development?
		mount LetterOpenerWeb::Engine, at: "/letter_opener"
	end

Majd a 'development.rb' fájlban állítsuk be az e-mail küldés formáját:

	config.action_mailer.delivery_method = :letter_opener_web
	config.action_mailer.default_url_options = { host: 'localhost' }

Nézzük, mi is történt: igényeljünk egy jelszó-emlékeztetőt, majd ellenőrizzük a [http://localhost:3000/letter_opener](http://localhost:3000/letter_opener) oldalt!
