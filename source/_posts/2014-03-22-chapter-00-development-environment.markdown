---
layout: post
number: 01
title: "Fejlesztői környezet kialakítása"
date: 2014-03-22 13:36:43 +0100
categories:
comments: false
published: true
---

# Bevezetés

Fejlesztői környezetnek azon programok összességét nevezzük, amivel egy fejlesztő a napi munkáját sikeresen el tudja látni a számítógépén. Ide értjük webes fejlesztés esetén a böngészőt illetve a kód szerkesztéséhez szükséges editor-t, de része az összes olyan program, ami a megírt kód futtatásához, illetve kiszolgálásához szükséges. Ruby on Rails fejlesztéshez természetesen szükség van Ruby-ra, magára a Rails keretrendszerre, adatbázisra, webszerverre, stb. Alapesetben egy programozásban már jártas fejlesztő ezeket az eszközöket egyenként feltelepíti a környezetébe, de ez óriási munka. Ráadásul sokféle operációs rendszert használtok, sokatok Windows-on dolgozik, ahol ezeknek a telepítése még nagyobb probléma.
A fentiek miatt kísérletképpen azt találtuk ki, hogy eltérünk a hivatalos RailsGirls folyamattól, és nem az általuk biztosított nehézkes telepítőket használjuk a gépeteken. Ehelyett egy úgynevezett virtuális számítógépet állítottunk nektek össze, amivel remélhetőleg sokkal kényelmesebben tudtok dolgozni.

<!-- more -->

## Virtuális számítógép

A [virtuális számítógép](http://hu.wikipedia.org/wiki/Virtu%C3%A1lis_sz%C3%A1m%C3%ADt%C3%B3g%C3%A9p) egy szimulált számítógépet jelent. Lényegében egy új számítógép egy másik számítógépen belül.

# Fejlesztői környezet telepítése

## Virtuális környezet

Összeállítottunk számotokra egy virtuális számítógépet, amire előre feltelepítettünk mindent, amire szükségetek lesz a fejlesztéshez. Az érdeklődők számára információ, hogy ez a virtuális gép amúgy egy 12.04-es verziószámű, 32 bites Linux (Ubuntu), amire nem telepítettünk semmilyen ablakkezelő felületet, mivel nem is lesz rá szükségetek. Így mindenki a saját, megszokott környezetében (operációs rendszerén) írhatja a kódot, illetve a kedvenc böngészőjét használhatja, a virtuális gépre csak a Rails-nek kiadott parancsokhoz van szükség, amit amúgy majd szintén böngészőből tudtok az egyszerűség kedvéért megtenni, de erről majd később.

### VirtualBox

A VirtualBox az Oracle szoftvercég virtualicáziós platformja, amely ingyenesen letölthető mind a három általunk támogatott operációs rendszerre. A https://www.virtualbox.org/wiki/Downloads címről töltsük le az operációs rendszeretekhez illő telepítőt, és a letöltés után telepítsük az alkalmazást.

### RailsGirls virtuális gép

Töltsük le [ide kattintva](http://www.digitalnatives.hu/demos/railsgirls/railsgirls_virtualbox_vm.zip) a virtuális gépet. Letöltés után csomagoljuk ki a zip file-t egy olyan könyvtárba, amit később is megtalálunk.
Nyissuk meg a már feltelepített VirtualBox alkalmazást. A menüben keressük meg a Machine/Add funkciót. Tallózzuk be az imént kicsomagolt könyvtárat, és válasszuk ki a vbox kiterjesztésű file-t.
Ha minden jól ment, akkor a VirtualBox program bal oldali részén megjelent a RailsGirls nevű virtuális gép, eljött az idő, hogy elindítsuk!

{% img center /images/chapter_00/virtualbox.png 600 600 %}

#### Elindítás

Válasszuk ki a virtuális gépet, majd nyomjunk rá a start gombra! Ha minden jól megy, akkor megjelenik egy új (csúnya fekete) ablak amiben 10-60 másodperc múlva egy `precise32 login:` felirat olvasható. Ez azt jelenti, hogy fut a virtuális gépünk!!! :) Erre az ablakra többet nem lesz szükségünk, de ne zárjuk be, mert muszáj, hogy a háttérben fusson.

#### Leállítás

Ha nincs szükség többet a virtuális gépre, akkor le is állíthatjuk (ezt most egyelőre ne tegyük meg). Ehhez klikkeljünk jobb egérgombbal a VirtualBox programon belül a RailsGirls virtuális gépre, és válasszuk a Close / Power Off parancsot. A virtuális gép ezek után gyakorlatilag kikapcsol, és tárhelyen kívül nem foglal erőforrást a gépen a legközelebbi elindításig.

#### Eltűnő egér

Ha a nem használt VirtualBox ablakba beleklikkelünk, az "elkapja" az egeret, és gyakorlatilag eltűnik. Az ablak jobb alsó sarkában látszik, hogy milyen billentyűt kell megnyomni az egér elengedéséhez. Mac-en ez a bal oldali command gomb, a többi operációs rendszeren pedig a jobb oldali ctrl gomb.

### Használat

Most, hogy a virtuális gép már fut, jelentkezzünk be rá. A fent megjelent, nem használt virtualbox ablakból is megtehetnénk, de nagyon kényelmetlen lenne a használata (nem venné figyelembe a gépünk billentyűzetkiosztását, nem működne a copy-paste, stb-stb). Távolról is be tudnátok rá jelentkezni (úgynevezett SSH kapcsolat segítségével), de a Windows-on nincs beépített SSH kliens, a telepítése meg elég körülményes.
A fentiek miatt megoldottuk, hogy a virtuális gépeteket el tudjátok érni a böngészőtökön keresztül! Nyissuk meg a böngészőt, és írjuk be a [http://localhost:57575](http://localhost:57575) címet.
Ha mindent jól csináltunk eddig, akkor a megjelenő oldalon egy pillangót láthatunk:

{% img center /images/chapter_00/butterfly_login.png 600 600 %}

Mostantól erre az ablakra konzol-ként fogunk mindenhol hivatkozni, később ide kell beírni rails-es parancsokat.

#### Copy-paste

Linux-on és Windows-on nem a megszokott ctrl-v billentyűzetkombinációval működik a beillesztés. Helyette a shift-insert kombinációt használjuk, az esetek többségében működik

### Bejelentkezés

Jelentkezzünk be a virtuális gépbe az alábbi azonosítokkal! (A jelszó beírásakor nem jelenik meg semmi, ez ne zavarjon minket!)

    login: vagrant
    password: vagrant

Sikeres bejelentkezés után a következő fogad minket:

    vagrant@precise32:~$

FONTOS: akárhányszor jelentkezünk is be a fentiek szerint, bejelentkezés után mindig írjuk be a `/bin/bash --login` parancsot!!!

### Közös könyvtár

Ha idáig eljutottunk, a telepítés nehezén túl vagyunk. Fut a virtuális gépünk, és gyakorlatilag előállt a fejlesztői környezet. Egy dolog van már csak hátra: meg kell osztani azt a könyvtárat a virtuális géppel, ahová a kódot írni fogjuk.
Hozzunk létre egy railsgirls nevű könyvtárat a gépünkön (mondjuk a felhasználói fiókunk alá). Állítsuk le a virtuális gépet a már említett módon. Leállítás után jelöljük ki a gépet, majd nyomjunk a Settings gombra. Klikkeljünk a Shared Folders tab-ra, és a jobb oldali gombok közül kattintsunk a kis plusz ikonra. A megjelenő kis ablakban Folder Path-ként tallózzuk be a már létrehozott railsgirls könyvtárat, Folder Name-nek pedig a railsgirls nevet adjuk. Pipáljuk be továbbá az Auto Mount opciót (a másik két opció NE legyen bepipálva).
Indítsuk el megint a virtuális gépet, majd nyissuk meg a konzolt böngészőben ([http://localhost:57575](http://localhost:57575)). Jelentkezzetek be a már megismert módon (és ne felejtsétek el bejelentkezés után kiadni a `/bin/bash --login` parancsot).

#### Ellenőrzés

A megosztott könyvtár a virtuális gépen a /media/sf_railsgirls útvonalon található meg. Ellenőrizzük, hogy minden működik. Lépjünk először be ebbe a könyvtárba a konzolban:

    $ cd /media/sf_railsgirls

Hozzunk létre egy üres file-t:

    $ touch test.txt

Most nézzük meg a saját operációs rendszerünkben, hogy a megosztott könyvtárban ott van e a `test.txt` file. Ha igen, akkor készen van a virtuális környezetünk! :)

## Böngésző

Az egységes kezelhetőség szempontjából kérünk titeket, hogy telepítsétek Firefox nevű böngészőt a gépetekre. Ezt megtehetjük minden platformon a [http://www.getfirefox.com](http://www.getfirefox.com) címen.

## SQLite nézegető

Adatbázisként a gépünkön SQLite-ot fogunk használni. Hogy meg tudjuk majd nézni, hogy mit és hogy tárol az adatbázis, szükség van egy nézegetőre. Szerencsére van Firefox app erre.
A telepítéshez Firefox alól nyissuk meg a [https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/?src](https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/?src) címet, és kattintsunk a Download gombra.

## Editor

Az editor egy nagyon okos szövegszerkesztő, amiben gyakorlatilag a webes alkalmazásunk forráskódját írjuk. Mi nagyon szeretjük a Sublime Text Editor nevű szerkesztőt, ezt a [http://www.sublimetext.com/3](http://www.sublimetext.com/3) töltsük le, és telepítsük.
