---
layout: post
title: "Ötödik fejezet - De ez így picit csúnyácska még, nem?"
date: 2014-03-17 09:20:28 +0100
published: false
comments: false
categories:
---

# Dizájn - Bootstrap

Most, hogy működik az app, már foglalkozhatunk a külsőségekkel is kicsit. Adjunk egy kis dizájnt neki, hogy ránézésre is profi oldalunk legyen!

<!-- more -->

## 1. lépés: állítsuk be az app kinézetét

Használjuk a bootstrap.min.css fájlt, hogy kicsit könnyedebb stílusú legyen az oldal. A CSS fájlok segítségével tudjuk egyszerűen és gyorsan felöltöztetni az oldalunkat akármilyen stílusúvá. Nem kell mást tennünk, mint egy fájlt (amiben van a css kódunk) belinkelni az oldalunkra, így az automatikusan onnan veszi majd a dizájnra való beállításokat.
Nyissuk meg az `app/views/layouts/application.html.erb` fájlt a szövegszerkesztőnkben, és a

``` html app/views/layouts/application.html.erb
	<%= stylesheet_link_tag    "application", :media => "all" %>
```

sort után szúrjuk be az alábbit:

``` html app/views/layouts/application.html.erb
  <%= stylesheet_link_tag "http://netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap.min.css", 
													:media => "all" %>

```

Ezeket a fájlokat saját számítógépünkön is tárolhatjuk, sőt, sajátot is írhatunk, de most a gyorsabb haladás érdekében nem megyünk nagyon bele a részletekbe, hanem előre megírt stílusfájlokkal dolgozunk.
Következő lépésként a `app/assets/stylesheets/application.css` fájl végére írjuk be az alábbi sort

``` css app/assets/stylesheets/application.css
	body { padding: 30px 20px 0 20px; }
```

Ez már kőkemény programozás a stílusfájlban! Annyi történt, hogy beállítottuk a lap tetején levő tér méretét 30px-re, a jobb és bal oldalit pedig 20-20 pixelre.
Végül pedig töröljük az `app/assets/stylesheets/scaffolds.css.scss` fájlt, hogy az alapból beállított Rails stílust most ne használja az oldalunk.
Frissítsük az oldalt a [http://localhost:3000/ideas](http://localhost:3000/ideas) címen. Nem sok minden változott, de azért valami már kezd látszani (például a tetején látszik a méretváltozás).

## 2. lépés: Navigáció

Mivel az új ötlet hozzáadása az egyik legfontosabb funkciója az app-nek, csináljunk neki egy gombot a menüsorba, hogy mindig egyből elérjük!
Nyissuk meg a `app/views/layouts/application.html.erb` fájlt a szerkesztőben, és a

``` html app/views/layouts/application.html.erb
	<%= link_to "Ideas", ideas_path, class: "brand" %>
```

sor alá tegyük be a következőt:

``` erb app/views/layouts/application.html.erb
  <ul class="nav">
		<li ><%= link_to 'New Idea', new_idea_path %></li>
	</ul>
```

Ezzel gyakorlatilag annyit tettünk, hogy hozzáadtunk egy új linket az oldalunkhoz. Szinte szó szerint ezt is írtuk le a programban, csak angolul: a fájl neve, majd 'link to new idea'.

## 3. lépés: Csinosítsuk ki az ötletlistát

Tegyük most már profi kinézetűvé az ötletlista oldalunkat végre! Ehhez a táblázatok helyett div-eket fogunk használni. Röviden ez azért jó nekünk, mert akkor nem egy fix, nagy táblázat van a honlapon, amit nem tudunk mozgatni és kedvünkre átszabdalni (mintha egy kész házba beköltöznénk), hanem mi építjük fel és rakjuk a helyére az összes elemet (ezek a 'div'-ek) az oldalunkon, a nekünk megfelelő helyre.

Nyissuk meg a `app/views/ideas/index.html.erb` fájlt a szerkesztőben, és az egészet írjuk át a következőre:

``` erb app/views/ideas/index.html.erb
<h1>Listing ideas</h1>
<% @ideas.in_groups_of(3) do |group| %>
  <div class="row">
    <% group.compact.each do |idea| %>
      <div class="span4">
        <h4><%= link_to idea.title, idea %></h4>
        <%= idea.description %><br/>
        <em><%= idea.user.try(:email) %></em>
        <p>
          <%= link_to 'Edit', edit_idea_path(idea) %> |
          <%= link_to 'Destroy', idea, confirm: 'Are you sure?', method: :delete %>
        </p>
      </div>
    <% end %>
  </div>
<% end %>
```

Itt látható is, hogy megjelentek kis 'div' szövegek itt-ott a programban, és pontosan ez kellett nekünk. 

Frissítsük az oldalunkat! Most már egész csinosan néz ki, hiszen az új css fájl (amit ennek a fejezetnek a legelején belinkeltünk) pont úgy volt megírva, hogy a div-eket szépen a helyére rakja.
Most, ha a `New idea` gombra kattintunk, egyből tudunk is hozzáadni pár ötletet a listánkhoz, ami ráadásul minél tübb tartalmunk van az oldalon, annál jobban néz ki.
Manapság az egyik fő elv a dizájnban úgyis az, hogy a tartalom a legjobb dekoráció :)

## 4. lépés: Formázzuk meg az ötlet részleteiről szóló oldalt is

Kattintsunk az ötlet címére, és meg tudjuk nézni a hozzá tartozó részleteket. Ez most még csak a Rails által generált scaffold-ot mutatja, úgyhogy szépítsünk rajta.

Nyissuk meg a `app/views/ideas/show.html.erb` fájlt a szeresztőben, és írjuk át az egészet a következőre:

``` erb app/views/ideas/show.html.erb
<div class="row">
  <div class="span3">
    <p><b>Name: </b><%= @idea.title %></p>
    <p><b>Description: </b><%= @idea.description %></p>
    <p><b>By: </b><%= @idea.user.try(:email) %></p>
    <p>
      <%= link_to 'Edit', edit_idea_path(@idea) %> |
      <%= link_to 'Destroy', @idea, confirm: 'Are you sure?', method: :delete %> |
      <%= link_to 'Back', ideas_path %>
    </p>
  </div>
</div>
```

Itt is bekerültek most a 'div'-ek, amiket már jól ismerünk. A css fájl pedig szépen a helyére pakolja a dolgokat.
Frissítsük újra az oldalunkat a [http://localhost:3000/ideas](http://localhost:3000/ideas) címen. Ugye, hogy jobb lett?
