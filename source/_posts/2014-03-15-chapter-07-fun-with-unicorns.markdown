---
layout: post
title: "Hetedik fejezet - Meseország"
date: 2014-03-15 09:21:40 +0100
published: false
comments: false
categories:
---

Lazításképpen a nap végére még egy apró feladatunk maradt: ez pedig az, hogy kicsit egyedibbé, könnyebben felismerhetővé tegyük a felhasználóinkat.
A fejezet célja, hogy minden felhasználóhoz meg tudjuk jeleníteni egy egyedi képet, egy úgynevezett avatart.

<!-- more -->

Ahhoz, hogy minden felhasználónak tudjunk mutatni egy egyedi profilképet, a következő eszközt fogjuk használni: [http://unicornify.appspot.com/use-it](http://unicornify.appspot.com/use-it).

A linkre kattintva ki tudjuk próbálni, hogy hogyan működik, illetve le van írva, hogy mi kell a használatához. Az alapvető koncepció az, hogy az adott felhasználó e-mail címe alapján generálódik egy egyedi unikornisos kép, aminek még szabadon megadhatjuk a méretét is bizonyos keretek között.

Ahhoz, hogy ezt integrálni tudjuk az alkalmazásunkba, kát dolgot kell megtennünk: egyrészt meg kell tudnunk mondani minden felhasználó e-mail címmének md5 hash értékét, másrészt pedig minden felhasználóhoz meg kell tudnunk mondani azt a linket, ahol elérhető az ő egyszarvús képe.

Felhasználó e-mail md5 hash kiszámítása:
``` ruby app/models/user.rb
def email_md5
  Digest::MD5.hexdigest(self.email)
end
```

Az avatar link összeállítása:
``` ruby app/helpers/ideas_helper.rb
module IdeasHelper

  def creator_unicorn_img(idea, size=64)
    if idea.user.present?
      image_tag "http://unicornify.appspot.com/avatar/#{idea.user.email_md5}?s=#{size}",
                alt: idea.user.email, title: idea.user.email
    end
  end

end
```

Ezek után pedig nincs más dolgunk, mint a megfelelő viewkban beszúrni a felhasználó avatarját az alábbi kódrészlet szerint:
``` erb app/views/ideas/index.html.erb
  <% group.compact.each do |idea| %>
    <div class="span1"><%= creator_unicorn_img(idea) %></div>
    <div class="span3">
      <h4><%= link_to idea.title, idea %></h4>
      <%= idea.description %><br/>
      <em><%= idea.user.try(:email) %></em>
      <% if idea.created_by?(current_user) %>
        <p>
          <%= link_to 'Edit', edit_idea_path(idea) %> |
          <%= link_to 'Destroy', idea, confirm: 'Are you sure?', method: :delete %>
        </p>
      <% end %>
    </div>
  <% end %>
```

``` erb app/views/ideas/show.html.erb
  <%= creator_unicorn_img(@idea, 128) %>
  <p><b>By: </b><%= @idea.user.try(:email) %></p>
```

Utolsó lépésként rakjuk ki a kész alkalmazásunkat a Herokura:
    $ git add .
    $ git commit -m 'felhasznalok es otletek'
    $ git push heroku master
    $ heroku run rake db:migrate

és csodáljuk meg a nap munkájának gyümölcsét :)
