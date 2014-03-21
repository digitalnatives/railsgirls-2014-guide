---
layout: post
title: "Negyedik fejezet - Ötletgazdák"
date: 2014-03-18 09:19:42 +0100
published: true
comments: false
categories:
---

Gondoljuk csak végig, hol tart most a alkalmazásunk? Vannak felhasználóink, akik be tudnak jelentkezni, és van lehetőségük arra, hogy az ötleteiket feltöltsék a rendszerbe.
Viszont akad egy bökkenő: jelenleg nem tudjuk megmondani, hogy melyik ötlet melyik felhasználóhoz tartozik.
Ez a fejezet így arról fog szólni, hogy hogyan tudjuk összekötni az ötleteket az őket létrehozó felhasználókkal.

<!-- more -->

Ahhoz, hogy össze tudjuk kapcsolni a már meglévő entitásainkat, nem árt, ha van valamiféle elképzelésünk arról, hogy milyen formában tárolódnak az adataink.
A számítógépes programok a működésükhöz szükséges adatokat rendszerint valamilyen *adatbázisban* tárolják: a korszerű *adatbázis-kezelő rendszerek* lehetővé teszik, hogy adatokat gyorsan tudjunk visszakeresni.
Ebben az alkalmazásban az *SQLite* adatbázis-kezelőt használjuk. Az SQLite direkt kicsire és egyszerűre tervezett kis motor, de attól még nem szabad lebecsülni: igazi nagy programok is használják, például a Firefox böngésző is.
De hogyan is kell elképzelni egy ilyen adatbázist? Lényegében nem több, mint táblázatok összessége, melyeknek minden sora egy úgynevezett rekord - összekapcsolódó adatok halmaza -, egy oszlopba pedig az ugyanolyan jellegű adatok kerülnek.

Nade pontosan hogyan is néz ki ez esetünkben? Kétféleképpen is kereshetünk támpontokat.
Egyrészt belenézhetünk a `db/schema.rb` fájlba, ez az adatbázisunk felépítését tartalmazza, részletesen leírva, hogy milyen tábláink vannak, és azoknak milyen a szerkezete, vagyis melyik oszlopnak mi a neve és a típusa.

``` ruby db/schema.rb
ActiveRecord::Schema.define(:version => 20140310153638) do

  create_table "ideas", :force => true do |t|
    t.string   "title"
    t.text     "description"
    t.datetime "created_at",  :null => false
    t.datetime "updated_at",  :null => false
  end

  create_table "users", :force => true do |t|
    t.string   "email",                  :default => "", :null => false
    t.string   "encrypted_password",     :default => "", :null => false
    t.string   "reset_password_token"
    t.datetime "reset_password_sent_at"
    t.datetime "remember_created_at"
    t.integer  "sign_in_count",          :default => 0,  :null => false
    t.datetime "current_sign_in_at"
    t.datetime "last_sign_in_at"
    t.string   "current_sign_in_ip"
    t.string   "last_sign_in_ip"
    t.datetime "created_at",                             :null => false
    t.datetime "updated_at",                             :null => false
  end

  add_index "users", ["email"], :name => "index_users_on_email", :unique => true
  add_index "users", ["reset_password_token"], :name => "index_users_on_reset_password_token", :unique => true

end
```

Ha nem csak erre, hanem magára az adatbázis tartalmára is kíváncsiak vagyunk, akkor használhatjuk például a Mozzillához írt *SQLIte Manager* nevű modult, ez elvileg mindenkinél használatra kész (ha esetleg mégsem, az alábbi linkre kattintva telepíthető: [https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/?src](https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/?src)).
Kattintsunk a böngészőben kattintsunk a *Tools* menüpontra, majd ott az *SQLite Manager*-re. Ezek után kapcsolódjuk az adatbázisunkhoz: a *Database* menüpont alatt keressük és nyomjuk meg a *Connect Database* opciót, és a felugró fájlkeresőben válasszuk ki a `db/development.sqlite3` fájlt (abból a mappából kiindulva, ahol a projektünk van).

{% img center /images/chapter_04/sqlite_manager.png 1366 768 %}

Amit a nézelődés során érdemes észrevenni: minden táblában találunk egy olyan oszlopot, ami valamiféle *id*-nak, úgynevezett egyedi azonosítónak van elnevezve. Ez az oszlop egész számokat tartalmaz, és azt lehet róla tudni, egy adott táblában minden rekordé garantáltan különbözik, ezzel téve lehetővé, hogy egyértelműen azonosítani tudjuk a táblázat egy-egy sorát.

Ez fogja a kezünkbe adni a kulcsot a probléma megoldására, hogy hogyan is tudnánk összekapcsolni az ötleteket a gazdáikkal: ha az ötleteket tartalmazó táblába felveszünk egy új oszlopot, ami a hozzá tartozó felhasználó egyedi azonosítószámát tartalmazza, onnantól ha ebben vagy egy érték, akkor meg tudjuk keresni, hogy melyik felhasználó az, aki az ötletet létrehozta.

Lényegében arra van itt szükség, hogy válztassunk az adatbázis sémán, a Rails keretein belül pegig ezt egy úgynevezett *migration* létrehozásával és futtatásával tehetjük meg.
Generáljunk egy migrationt az alábbi paranccsal:
    $ rails generate migration add_user_id_to_ideas

Ez létrehoz számunkra egy új fájlt a `db/migrate` mappába, ebben tudjuk megmondani, hogy szereznék egy új oszlopot hozzáadni az öleteket tároló táblához, aminek a neve `user_id`, a típusa pedig `integer`, vagyis egy egész szám.

``` ruby db/migrate/20140322ooppmm__add_user_id_to_ideas.rb
class AddUserIdToIdeas < ActiveRecord::Migration
  def change
    add_column :ideas, :user_id, :integer
  end
end
```

Majd migráljuk az adatbázist, ezzel fog valóban frissülni a séma:
    $ rake db:migrate

Ellenőrizzük, hogy a változás valóban megtörtént-e, nézzük meg, kibővült-e a `db/schema.rb` fájl tartalma.


Ha ezzel készen vagyunk, akkor már nincs akadálya, hogy úgymond modell oldalon is megtegyük az összeköttetést. Mint már az korábban említve volt, a Rails egy MVC keretrendszer, ennek az első betűje, M, a modell szót rövidíti. Esetünkben gondoljuk úgy a modellekre, mint egy-egy táblára az adatbázisban, megspékelve jónéhány kellemes segédfüggvénnyel, ami megkönnyíti számunkra az adatokhoz való hozzáférérést, illetve azok létrehozását, módosítását, törlését.
Ennek remek példája, hogy ruby oldalon két modell közötti kapcsolat létrehozását előre megírt, beszédes nevű függvények segítik:

``` ruby app/models/idea.rb
  belongs_to :user
```

``` ruby app/models/user.rb
  has_many :ideas
```

Ha ilyen módon összekötjuk a két modellt, akkor mostantól bármelyik ötleten meg tudunk hívni egy `user` methódust, ami visszaadja a hozzá teratozó felhasználót, illetve bármelyik felhasználón rendelkezésre fog állni egy `ideas` methódus, ami egy tömbben visszaadja az általa létrehozott ötleteket.


Most már azt gondolhatnánk, hogy minden rendben, összekötöttük a két modellt, akkor bizonyára mostantól minden ötletről tudni fogjuk, hogy ki hozta létre, igaz?
Sajnos azért ennyire nem egyszerű a helyzet, hiszen az egy dolog, hogy létrehoztuk az új mezőt, ahol tárolható a létrehozó felhasználó azonosítója, de ha belegondolunk, nem állítjuk be ennek az értékét soha.
A cél tehát most az, hogy elkapjuk az a pillanatot a kódban, mikor egy ötlet létrejön. Annyit most már tudhatunk, hogy ez azzal jár, hogy az ötletek táblája az adatbázisban egy új sorral bűvül.
Ezzel el is érjük az MVC egy újabb betűjét, a C-t, ami a kontroller szót rövidíti. Azt mondhatjuk, hogy a kontrollerek kötik össze a felületet, amit oldalunkból látunk azzal, ami a háttérben történik, például a modellekkel is.
Ha meg akarjuk jeleníteni a például az összes eddigi ötletet, amit csak találunk, akkor ezt kontroller szinten a megfelelő függvényhívással lekérdezzünk, és a felületen már csak meg kell jelenítenünk. Ezt például a `app/controllers/ideas_controller.rb`-ben található `index` metódus végzi:

``` ruby app/controllers/ideas_controller.rb
def index
  @ideas = Idea.all

  respond_to do |format|
    format.html # index.html.erb
    format.json { render json: @ideas }
  end
end
```

Itt láthatjuk, ahogyan a kontroller kommunikál a modellel, elkéri tőle az összes létező ötletünket az `Idea.all` hívással, majd ezt elérhetővé teszi a megjelenítési réteg -az eddig kimaradt V betű - számára. De nem csak megjelenítésben van szerepe a kontrollernek, hanem például abban is, amikor a formon keresztül létre szeretnénk hozni egy ötletet. Itt a kapcsolat iránya megfordul: a kontroller értelmezi, hogy milyen adatokat kaptunk a felületről a formon keresztül, majd ezek alapján meghívja a megfelelő modell metódust, amivel létre lehet hozni egy új ötletet.
Ez az a pont, ahova nekünk kicsit bele kell nyúlni, a formból jövő adatokat még ki kell annyival egészíteni, hogy ki a létrehozó felhasználó.

Egészítsük ki az ötletekért felelős controller `create` függvényét:
``` ruby app/controllers/ideas_controller.rb
  def create
    @idea = Idea.new(params[:idea])

    @idea.user = current_user

    respond_to do |format|
      if @idea.save
        format.html { redirect_to @idea, notice: 'Idea was successfully created.' }
        format.json { render json: @idea, status: :created, location: @idea }
      else
        format.html { render action: "new" }
        format.json { render json: @idea.errors, status: :unprocessable_entity }
      end
    end
  end
```
A `@idea.user = current_user` sor az újonnan bekerült kódrészlet, ezzel biztosíthatjuk, hogy az éppen belejentkezett felhasználó rá is legyen mentve a létrejövő ötletre.
Elvileg készen vagyunk, ha mostantól létrehozunk egy új ötletet az eddigi formon keresztül, és belenézünk az adatbázisba, láthatjuk, hogy az új ötlet `user_id` mezőjének értéke megegyezik az aktuális felhasználónk `id` értékével.

Egy fontos dolog maradt még hátra: most, hogy már minden ötletről tudjuk, hogy ki a gazdája, fel is kellene ezt tüntetnük a felületen, hogy mindenkinek nyilvánvaló legyen.
Két helyen mutatunk információkat az ötletekről, egyrészt a listaoldalon, másrészt pedig minden ötlet saját oldalán, ez a két hely tehát az, ahova be kellene szúrnunk az alkotóról valamiféle információt, például az e-mail címét.

A lista oldalon egészítsük ki a táblázatot egy újabb oszloppal:
``` erb app/views/ideas/index.html.erb
<table>
  <tr>
    <th>Title</th>
    <th>Description</th>
    <th>By</th>
    <th></th>
    <th></th>
    <th></th>
  </tr>

<% @ideas.each do |idea| %>
  <tr>
    <td><%= idea.title %></td>
    <td><%= idea.description %></td>
    <td><%= idea.user.try(:email) %></td>
    <td><%= link_to 'Show', idea %></td>
    <td><%= link_to 'Edit', edit_idea_path(idea) %></td>
    <td><%= link_to 'Destroy', idea, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  </tr>
<% end %>
</table>
```

A részletes oldalon pedig hozzunk létre egy új bekezdést:
``` erb app/views/ideas/show.html.erb
<p>
  <b>By:</b>
  <%= @idea.user.try(:email) %>
</p>
```

Ha ezzel készen vagyunk, már senkinek nem vonhatja kétségbe, hogy melyik remek ötletet melyik felhasználó találta ki és töltötte fel a rendszerünkbe :)

Hogy éles környezetben is ki tudjuk próbálni a változásokat valódi felhasználókkal, rakjuk ki ismét az alkalmazást a Herokura:

    $ git add .
    $ git commit -m 'felhasznalok es otletek'
    $ git push heroku master
    $ heroku run rake db:migrate
