---
layout: post
number: 01
title: "Fejlesztői környezet kialakítása"
date: 2014-03-22 13:36:43 +0100
categories:
published: false
comments: false
---

# Bevezetés

Fejlesztői környezetnek azon programok összességét nevezzük, amivel egy fejlesztő a napi munkáját sikeresen el tudja látni a számítógépén. Ide értjük webes fejlesztés esetén a böngészőt illetve a kód szerkesztéséhez szükséges editor-t, de része az összes olyan program, ami a megírt kód futtatásához, illetve kiszolgálásához szükséges. Ruby on Rails fejlesztéshez természetesen szükség van Ruby-ra, magára a Rails keretrendszerre, adatbázisra, webszerverre, stb. Alapesetben egy programozásban már jártas fejlesztő ezeket az eszközöket egyenként feltelepíti a környezetébe, de ez óriási munka. Ráadásul sokféle operációs rendszert használtok, sokatok Windows-on dolgozik, ahol ezeknek a telepítése még nagyobb probléma.
A fentiek miatt kísérletképpen azt találtuk ki, hogy eltérünk a hivatalos RailsGirls folyamattól, és nem az általuk biztosított nehézkes telepítőket használjuk a gépeteken. Ehelyett egy úgynevezett virtuális számítógépet állítottunk nektek össze, amivel remélhetőleg sokkal kényelmesebben tudtok dolgozni.

<!-- more -->

## Virtuális számítógép

# Fejlesztői környezet telepítése

## Virtuális környezet

Összeállítottunk számotokra egy virtuális számítógépet, amire előre feltelepítettünk mindent, amire szükségetek lesz a fejlesztéshez. Az érdeklődők számára információ, hogy ez a virtuális gép amúgy egy 12.04-es verziószámű, 32 bites Linux (Ubuntu), amire nem telepítettünk semmilyen ablakkezelő felületet, mivel nem is lesz rá szükségetek. Így mindenki a saját, megszokott környezetében (operációs rendszerén) írhatja a kódot, illetve a kedvenc böngészőjét használhatja, a virtuális gépre csak a Rails-nek kiadott parancsokhoz van szükség, amit amúgy majd szintén böngészőből tudtok az egyszerűség kedvéért megtenni, de erről majd később.

### VirtualBox

A VirtualBox az Oracle szoftvercég virtualicáziós platformja, amely ingyenesen letölthető mind a három általunk támogatott operációs rendszerre. A https://www.virtualbox.org/wiki/Downloads címről töltsétek le az operációs rendszeretekhez illő telepítőt, és a letöltés után telepítsétek az alkalmazást.

### RailsGirls virtuális gép

Kérlek töltsétek le [ide kattintva](http://www.google.com) a virtuális gépet. Letöltés után csomagolátok ki a zip file-t egy olyan könyvtárba, amit később is megtaláltok.
Nyissátok meg a már feltelepített VirtualBox alkalmazást. A menüben keressük meg a Machine/Add funkciót. Tallózzuk be az imént kicsomagolt könyvtárat, és válasszuk ki a vbox kiterjesztésű file-t.
Ha minden jól ment, akkor a VirtualBox program bal oldali részén megjelent a RailsGirls nevű virtuális gép, eljött az idő, hogy elindítsuk! Válasszuk ki a virtuális gépet, majd nyomjuk rá a start gombra! Ha minden jól megy, akkor megjelenik egy új (csúnya fekete) ablak, amiben 10-60 másodperc múlva egy `precise32 login:` felirat. Ez azt jelenti, hogy fut a virtuális gépünk!!! :) Erre az ablakra többet nem lesz szükségünk, de ne zárjátok be, mert muszáj, hogy a háttérben fusson.

### Használat

Most, hogy a virtuális gép már fut, jelentkezzünk be rá. A fent megjelent, nem használt virtualbox ablakból is megtehetnénk, de nagyon kényelmetlen lenne a használata (nem venné figyelembe a gépetek billentyűzetkiosztását, nem működne a copy-paste, stb-stb). Távolról is be tudnátok rá jelentkezni (úgynevezett SSH kapcsolat segítségével), de a Windows-on nincs beépített SSH kliens, a telepítése meg elég körülményes.
Pont emiatt megoldottuk, hogy a virtuális gépeteket el tudjátok érni a böngészőtökön keresztül! Nyissátok meg a böngészőtöket, és írjátok be a [http://localhost:57575](http://localhost:57575) címet.
Ha mindent jól csináltatok eddig, akkor a megjelenő oldalon egy pillangót fogtok látni! Jelentkezzetek be a virtuális gépbe az alábbi azonosítokkal!

    login: vagrant
    password: vagrant

FONTOS: kérlek akárhányszor jelentkeztek is be a fentiek szerint, bejelentkezés után mindig írjátok be a `/bin/bash --login` parancsot.

## Böngésző

## Editor



<!-- more -->
