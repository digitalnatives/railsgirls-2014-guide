---
layout: post
title: "Első fejezet - Milyen remek ötlet!"
date: 2014-03-21 09:15:28 +0100
published: false
comments: false
categories:
---

Ebben a fejezetben létrehozzuk a Ruby on Rails alkalmazásunkat. Megismerjük a bundler-t (a Ruby-s világ csomagkezelőjét) és megtanuljuk, hogy kell elindítanunk a Rails szervert. Elkezdjük felépíteni az alkalmazásunkat is, a Rails úgynevezett scaffold generátorának segítségével már egy működő funkciónk lesz a fejezet végére: ötleteket hozhatunk létre, listázhatunk, törölhetünk.

<!-- more -->

# Bundler, gem

A bundler a Ruby-s világ csomagkezelője. Csomagnak, amit Ruby-ban gem-nek hívunk, egy olyan harmadik fél által fejlesztett kódkészletet nevezünk, amit hozzáadva a mi kódbázisunkhoz bővül annak funkciókészlete. Érdekesség, hogy maga a Ruby on Rails keretrendszer is egy gem. A virtuális gépetekre már előre feltelepítettük az eseményen használt legtöbb gem-et, így már a Ruby on Rails is telepítve van.
A felhasznált gem-eknek is vannak függőségei, és ezek is használnak egyéb gem-eket. A bundler-re pontosan emiatt van szükség, ennek az eszköznek a felelőssége, hogy minden függőség a megfelelő verziószámmal kerüljön feltelepítésre.

# Ruby on Rails

## Projekt létrehozás

Ahogy már említettük, a Ruby on Rails is egy gem. A Rails, ahogy sok másik gem is, rendelkezik egy parancssorból (konzolból) futtatható paranccsal, amivel sok mindent tudunk csinálni. Most a projektünk létrehozására fogjuk használni.

Lépjünk be a konzolban a megosztott könyvárunkba:

    $ cd /media/sf_railsgirls

Hozzuk létre az alkalmazásunk vázát, az alkalmazásunknak legyen a neve ideapad:

    $ rails new ideapad -T --skip-bundle

A `-T` kapcsoló azt jelenti, hogy nem hoz létre automatikus tesztekhez környezetet (ezt most kihagyjuk, pedig izgalmas téma...), a `--skip-bundle` kapcsolóval pedig megkérjük a rails-t arra, hogy egyelőre ne futtassa a `bundle install` parancsot, amivel a csomagfüggőségeket rakja fel. Sikeres futtatás után létrejött egy új könyvtár ideapad néven, benne egy csomó előre kigenerált könyvtárral és file-lal. Ez az alkalmazásunk, ezt fogjuk tovább bővíteni. Nyissuk meg a könyvtárat gépünk operációs rendszerén az editor-ban, és nézzünk körbe: nyissuk meg a feltelepített Sublime Text Editor-t (mostantól editor-ként hivatkozunk rá), a File/Open paranccsal keressük meg a megosztott könyvtárunkban az ideapad könyvtárat, majd nyomjunk az open gombra.

## Csomagfüggőségek, Gemfile

Keressük meg a Gemfile nevű file-t az editor-ban, és nyissuk meg. A Gemfile tartalmazza a projektünkben általunk használni kívánt csomagot. Láthatjátok, hogy nem hazudunk, a file-ban ott van maga a Rails gem is. Ahhoz, hogy le tudjuk futtatni a `bundle install` parancsot (amivel minden függőséget telepíteni tudunk), még hozzá kell adnunk egy gem-et (csomagot) az alkalmazásunkhoz. Adjuk hozzá a Gemfile-hoz a következő sort, majd mentsük el a file-t (ezt a későbbiekben is mindig meg kell tenni, ha arra kérünk titeket, hogy módosítsatok valamit a kódbázison):

``` ruby Gemfile
  gem 'therubyracer'

```

Ha mindent jól csináltunk, elhárult az akadály, hogy feltelepíthessünk minden csomagfüggőséget. Ehhez a konzolban adjuk ki a következő parancsot:

    $ bundle install

Ha minden jól ment, a futás végén egy ilyen szöveget láthatunk:

    Your bundle is complete!
    Use `bundle show [gemname]` to see where a bundled gem is installed.

Jó hírünk van! Van egy Rails alkalmazásunk, és feltelepítettük hozzá az összes függőséget, kezdhetünk kódolni! :)

## Scaffolding

A scaffolding (aminek jelentése amúgy az építkezéseken használt 'állványzat') egy szuper jó cucc. Az MVC keretrendszerek sajátossága, arra szolgál, hogy pár alapvető tulajdonság meghatározásával a keretrendszer gyorsan, egy már működő kódot generáljon, amit tovább tudunk fejleszteni. Lássuk, hogyan is működik.

A konzolban futtassuk le a következő parancsot:

    $ rails generate scaffold Idea title:string description:text

Ezzel a paranccsal a következőt mondtuk meg a Rails-nek: "generálj nekem kérlek egy olyan 'állványzatot', ahol ötleteket kezelhetek. Egy ötletnek két attribútuma van: title (cím), és description (leírás). Címnek string (rövid szöveg), míg a leírásnak text (hosszú szöveg) típusú értéket adhatok."

A parancs kiadása után látszik, hogy milyen file-okat hozott létre a scaffold generátor. Látszik, hogy főleg az app könyvtárunk alkönyvtáraiban (model, controller és view könyvtárakban) keletkeztek file-ok.

Létrejött továbbá a *db/migrate* könyvtár alatt is egy új file. Ez tartalmazza, hogy az adatbázisunkban létre kell hozni egy új táblát (ideas néven), amiben olyan rekordok lesznek, amiknek egy rövid szöveg típusú title-jük és egy hosszú szöveg típusú description-ük kerül eltárolásra.

A konzolban futtasuk le a következő parancsot, ahhoz, hogy az adatbázis-szintű változások megtörténjenek:

    $ rake db:migrate

## Rails szerver elindítása

Most, hogy hozzáadtunk egy új funkciót az alkalmazásunkhoz, nézzük is meg böngészőben. Ahhoz, hogy a böngészőben el tudjuk érni az alkalmazásunkat, el kell indítanunk a webszerver-t, ehhez adjuk ki a konzolban a következő parancsot:

    $ rails server

Egy kis idő elteltével, a konzolban a következő kimenetet láthatjuk:

    => Booting WEBrick
    => Rails 3.2.17 application starting in development on http://0.0.0.0:3000
    => Call with -d to detach
    => Ctrl-C to shutdown server
    [2014-03-17 11:42:49] INFO  WEBrick 1.3.1
    [2014-03-17 11:42:49] INFO  ruby 1.9.3 (2012-04-20) [i686-linux]
    [2014-03-17 11:42:49] INFO  WEBrick::HTTPServer#start: pid=1874 port=3000

Mindez azt jelenti, hogy fut az alkalmazásunk, nézzük is meg böngészőben. Írjuk be a [http://localhost:3000/ideas](http://localhost:3000/ideas) címet (vagy kattintsunk a linkre). Az ötletek listaoldalára érkeztünk. Mivel nincs még egy darab ötlet sem felvéve, hozzunk létre egyet: nyomjunk a *New idea* gombra, adjunk meg címet és leírást, és mentsük el az ötletet. A listaoldara visszaérkezve már látszik is az ötletünk, amit a *Show* link segítségével megnézhetünk, az *Edit* link segítségével szerkeszthetünk, a *Delete* link segítségével pedig törölhetünk.

Gratulálunk, készen van az első, saját funkcióval rendelkező Rails-es alkalmazásotok!

## Alapértelmezett útvonal beállítása

Mindez szép és jó, de mit kapunk, ha nem írjuk be a */ideas* útvonalat, hanem csak a [http://localhost:3000](http://localhost:3000)-es címet nyitjuk meg? Amíg nem változtatjuk meg, addig a Ruby on Rails köszöntőoldala fogad minket. De mi persze megváltoztatjuk, mert jó volna, ha az ötletek listaoldalával nyílna meg az alkalmazásunk. Ehhez a következőket kell tennünk:

Először is töröljük ki a *public/index.html* fájt, majd adjuk hozzá az alábbi sort a *config/routes.rb* fájlhoz:

``` ruby config/routes.rb
  root to: "ideas#index"

```

Ha minden-t jól csináltunk, akkor a [http://localhost:3000](http://localhost:3000)-es címen már az ötletek listaoldalát látjuk!