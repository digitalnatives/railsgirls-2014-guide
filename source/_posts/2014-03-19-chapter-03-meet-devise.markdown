---
layout: post
title: "Harmadik fejezet - Ismerkedjük meg a deviseszal"
date: 2014-03-19 09:17:54 +0100
published: true
comments: false
categories:
---

Ebben a fejezetben megtapasztaljuk, hogy milyen kellemes, mikor nem kell újra feltalálnuk a spanyolviaszt. Megismerkedünk a devise nevű gemmel, és fel is használjuk arra, hogy az oldalunk ezentúl csak regisztrált felhasználók számára legyen elérhető. Majd mire már annyira megszerettük a gem-eket, kipróbálunk még egyet, a letter_opener-t.

<!-- more -->

# Devise integráció

Emlékezzünk vissza picit, mit csináltunk az első fejezetben! Amit ott hallottunk 'gem'-ekről meg a 'bundle' nevű parancsról, most azt fogjuk alkalmazni.
Nyissuk meg a `Gemfile`-t (ami emlékezzünk csak, hogy igazából egy lista arról, milyen gem-eket használ az app), és adjuk hozzá a következő sort:

	$ gem 'devise'

Ezzel most megmondtuk, hogy a 'devise'-ra is szükségünk van a jövőben.
Utána pedig futtassuk a terminálban a következőt:

	$ bundle install --without production

Ezzel tudjuk feltelepíteni az új csomagfüggőséget, mint az app felépítésének legelején is csináltuk.
Ezután indítsuk újra a szerverünket (ez már csak ilyen rutin dolog itt, fejlesztés közben, időnként újraindítjuk a szervert, hogy biztos minden újítás frissüljön az oldalon).

## 1. lépés: A devise beállítása az app-ben

A terminálba írjuk be, hogy:

	$ rails g devise:install

Ezzel hozzáadjuk a deviset

## 2. lépés: Konfiguráljuk!

Nyissuk meg a szerkesztőben a `config/environments/development.rb` fájlt, és adjuk hozzá a következő sort az `end` szó előtt:

``` ruby config/environments/development.rb
config.action_mailer.default_url_options = { host: 'localhost:3000' }
```

Most nyissuk meg a `app/views/layouts/application.html.erb` fájlunkat, és a `<%= yield %>` fölé írjuk be ezt:

``` erb app/views/layouts/application.html.erb
<% if notice %>
	<p class="alert alert-notice"><%= notice %></p>
<% end %>
<% if alert %>
	<p class="alert alert-error"><%= alert %></p>
<% end %>
```

## 3. lépés: Állítsuk be a User modellt

Generáljuk le a felhasználói modellt:

	$ rails generate devise user

A konzolban futtasuk le a következő parancsot, ahhoz, hogy az adatbázis-szintű változások megtörténjenek:

	$ rake db:migrate

## 4. lépés: Az első felhasználó...

Most, hogy már mindent beállítottunk, beregisztrálhatjuk az első felhasználónkat! Ehhez biztos-ami-biztos, indítsunk most megint újra a szervert, és menjünk a [http://localhost:3000/users/sign_up](http://localhost:3000/users/sign_up) címre, majd töltsük ki a szükséges felhasználói adatokat, és kész is az első felhasználónk!

## 5. lépés: Linkek a regisztrációhoz és bejelentkezéshez

Szeretnénk, ha anélkül is be lehetne regisztrálni az oldalunkra, hogy mindig be kellene gépelni a [http://localhost:3000/users/sign_up](http://localhost:3000/users/sign_up) címet, így csináljunk a navigációba linket hozzá!
Nyissuk meg a szerkesztőben a `app/views/layouts/application.html.erb` fájlt, és a <body> után adjuk hozzá a következőket:

``` erb app/views/layouts/application.html.erb
<div class="navbar">
  <div class="navbar-inner">
		<p class="navbar-text pull-right">
			<% if user_signed_in? %>
			  Logged in as <strong><%= current_user.email %></strong>.
			  <%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> |
			  <%= link_to "Logout", destroy_user_session_path, method: :delete, :class => 'navbar-link'  %>
			<% else %>
			  <%= link_to "Sign up", new_user_registration_path, :class => 'navbar-link'  %> |
			  <%= link_to "Login", new_user_session_path, :class => 'navbar-link'  %>
			<% end %>
		</p>

  	<%= link_to "Ideas", ideas_path, class: "brand" %>
  </div>
</div>
```

Ez azt csináltatja a programmal, amit angolul jelent a kód, de tényleg: 'link to', azaz csinálj linket, majd megmondja, hogy milyen néven, és hova mutasson a link.
Végül még állítsuk be, hogy automatikusan a bejelentkező oldalra legyenek irányítva a látogatók, mielőtt bármilyen ötlethez nyúlhatnának, hogy ne tudjon csak úgy akárki randalírozni az oldalon.
Ehhez most az `app/controllers/ideas_controller` fájlban az első sor után hozzá kell adnunk a következő sort:

``` ruby app/controllers/ideas_controller.rb
	before_filter :authenticate_user!
```

Itt a `before_filter` függvény ellenőrzi a felhasználót, hogy be van-e jelentkezve. Ezzel a függvénnyel ma még később is találkozunk majd.
Beállíthatjuk még a jelszó hosszát a `config/initializers/devise.rb` részen, mondjuk 6-ra, csak a biztonság kedvéért.
És készen is vagyunk! Jelentkezgessünk ki-be az oldalon, ugye milyen jó?

# Egy másik gem

## Az elfelejtett jelszó problémája

Biztos előfordult már mindenkivel, hogy a sok regisztráció után elfelejtettük egy adott oldalra a jelszavunkat (hiába van meg a kedvenc kis jelszavunk, amit az összes oldalon használunk, itt mondjuk valamiért mást kellett megadni, amit persze el is felejtettünk azóta). Ennek a problémának a megoldására van a jelszó-emlékeztető e-mail kitalálva, ahogy biztos már sok oldalon láttuk (és használtuk) is. Amíg azonban még nem múködik tökéletesen a jelszó-emlékeztetőnk, igen idegesítő lehet állandóan e-maileket küldözgetni magunknak, amihez mindig ki-be kell jelentkezni a levelezésünkbe, ráadásul tele lesz szeméttel, stb.
Mivel azonban a Ruby on Rails annyira jó, természetesen erre is van egy jó kis gem, a 'letter_opener_web'. Ez azt tudja, hogy egy honlapon legenerálja nekünk a jelszó-emlékeztető levelet, így egyből láthatjuk, hogyan is nézne ki e-mailben.
Tehát akkor az előző részben látotthoz hasonlóan adjuk a `letter_opener_web` nevű gemet is hozzá a `Gemfile`-hoz!
Majd jöhet a szokásos terminál parancs a függőségek beállítására:

	$ bundle install --without production

Állitsuk be az útvonalakat:

``` ruby config/routes.rb
	if Rails.env.development?
		mount LetterOpenerWeb::Engine, at: "/letter_opener"
	end
```

Majd a `config/environments/development.rb` fájlban állítsuk be az e-mail küldés formáját (magyarul: itt mondjuk meg a gem-nek, hogy ne e-mailt írjon, hanem egy honlapon jelenítse meg a levelet):

``` ruby config/environments/development.rb
	config.action_mailer.default_url_options = { host: 'localhost:3000' }
```
elé szúrjuk be az alábbi sort

``` ruby config/environments/development.rb
	config.action_mailer.delivery_method = :letter_opener_web
```

Nézzük, mi is történt: igényeljünk egy jelszó-emlékeztetőt, majd ellenőrizzük a [http://localhost:3000/letter_opener](http://localhost:3000/letter_opener) oldalt!
