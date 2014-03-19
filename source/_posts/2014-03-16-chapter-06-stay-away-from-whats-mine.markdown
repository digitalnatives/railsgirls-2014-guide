---
layout: post
title: "Hatodik fejezet - Mi a helyzet a magántulajdonnal?"
date: 2014-03-16 09:21:15 +0100
published: false
comments: false
categories:
---

Nézzük csak, hol is tartunk most? Az alkalmazásunk szép, a felhasználók be tudnak lépni, fel tudják tölteni az ötleteiket. Kezdjük elérni azokat a célokat, amiket a nap elején kitűztünk.
Van viszont sajnos egy apró problémánk, ami miatt a felhasználóink elég mérgesek lennének ránk, ha ez így maradna: jelenleg az összes felhasználó hozzáfér az összes ötlethez, és bárki bármit szerkeszthet, illetve törölhet. Valljuk be, azért ezt nem így képzeltük el :)

Ebben a fejezetben lépéseket teszünk annak érdekében, hogy a felhasználók tényleg magukénak érezhessék az ötleteiket: a cél, hogy szerekeszteni és törölni mindegyik felhasználó csak az általa létrehozott ötleteket tudja majd.

<!-- more -->

Gondoljuk végig, mi okozza a problémánkat elsődlegesen? Az például biztos nem egészséges, hogy minden ötlethez feltétel nélkül megjelenítünk egy szerkesztéshez és egy törléshez való linket. Ezeknek a linkeknek csak akkor kellene megjelenniük, ha a az adott felasználó hozta létre az adott ötletet.
Első lépésben tehát ha van egy ötletünk és van egy felhasználónk, el kellene tudni dönteni, hogy a felhasználó-e a látrehozó, vagy valaki más. Ez alapvetően modell szintű logika, magának az ötletnek a felelőssége, hogy tudja magáról, ki hozta őt létre. Ennek fényében írjunk egy metódust az ötlet modellen, ami megválaszolja a kérdésünk: a paraméterül kapott felhasználóról eldönti, ő volt-e a létrehozó, és ennek függvényében igaz vagy hamis értéket ad vissza.

``` ruby /app/models/idea.rb
def created_by?(user)
  self.user == user
end
```

Ha ez megvan, akkor ezt fel tudjuk használni a megfelelő view fájlokban, és az eredénye alapján el tudjuk dönteni, hogy szükség van-e a linkre, vagy sem:

``` erb app/views/ideas/index.html.erb
<% @ideas.each do |idea| %>
  <tr>
    <td><%= idea.title %></td>
    <td><%= idea.description %></td>
    <td><%= idea.user.try(:email) %></td>
    <td><%= link_to 'Show', idea %></td>

    <% if idea.created_by?(current_user) %>
      <td><%= link_to 'Edit', edit_idea_path(idea) %></td>
      <td><%= link_to 'Destroy', idea, method: :delete, data: { confirm: 'Are you sure?' } %></td>
    <% else %>
      <td></td>
      <td></td>
    <% end %>
  </tr>
<% end %>
```

``` erb app/views/ideas/show.html.erb
<% if @idea.created_by?(current_user) %>
  <%= link_to 'Edit', edit_idea_path(@idea), class: "btn btn-default btn-lg", role: "button" %>
<% end %>
```

Most már picit megnyugodtahunk, legalább explicit módosításra és törlésre buzdító linkeket nem fog látni a felhasználó mások ötleteihez.
De felmerül a kérdés: elég "csak" elrejteni a linkeket?

Sajnos nem, egy szemfülesebb felhasználó a link nélkül is rá tud találni mások ötleteinek szerkesztés oldalára, tovább kell gondolnunk tehát a megoldást. Az ideális az volna, hogy ha a ötleteket kezelő kontroller módosítást és törlést célzó metódusai csak akkor lennének elérhetőek, ha az aktuális felhasználó hozta létre az szóban forgó ötletet.
Megtehetjük például azt, hogy még mielótt megmutatnák az szerkesztés oldalt, leellenőrizzük a felhasználót, és ha nem jogosult szerkeszteni az ötletet, akkor nem a szerkesztés oldalra jut, hanem visszairányítjuk valamelyik olyan oldalra, amihez van jogosultsága.
Ezt a következőképp tudjuk megvalósítani a kontrollerben:

``` ruby app/controllers/ideas_controller.rb
before_filter :check_creator, only: [:edit, :update, :destroy]

   ...

def check_creator
  @idea = Idea.find(params[:id])
  unless @idea.created_by?(current_user)
    redirect_to root_path, alert: "You cannot mess with someone else's idea!"
  end
end
```

A `before_filter` függvény tökéletes eszköz számunkra: segítségével még az adott metódus előtt le tudjuk ellenőrizni a felhasználót, és ha nem övé az ötlet, akkor ahelyett, hogy továbbengedjük szerkeszteni vagy törölni, visszairányítjuk az ötleteket tartalmazó főoldalra egy kis üzenettel együtt, ami figyelmezteti, hogy tiltott dolgot próbált meg.
És ezzel készen is vagyunk, most már lényegtelen, hogy a felhasználó tudja-e, milyen linken keresse az adott ötlethez tartozó szerkesztő formot, nem fog oda eljutni, ha nem övé az ötlet :)
