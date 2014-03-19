---
layout: post
title: "Második fejezet -  Hiszen ez él!"
number: 03
date: 2014-03-20 09:17:10 +0100
comments: false
published: true
categories:
---
Ez a fejezet két dologról fog szólni: verziókövetésről és webszerverekről.

Azt szeretnénk, hogy a programunk egy olyan szerveren fusson, ami mindig be van kapocsolva, és lehetőleg egy állandó címen legyen elérhető. 
Szóval mire van szükségünk? egy webszerverre, ami: tárhelyet és fix címet ad az alkalmazásunknak, tudja futtatni a ruby on rails alkalmazásokat, fel lehet rá telelpíteni mindazon gem-eket, amikre az alkalmazásunkak szüksége van, ad adatbázis szolgáltatást. Ja és lehetőleg legyen ingyen:)

A Heroku pont egy ilyen szolgáltatás (és nem ez az egyetlen). Ezen kívül még van egypár jó tulajdonsága, amiről majd később szó lesz. Most egyelőre örülünk, hogy megtaláltuk, regisztráltunk a https://id.heroku.com/login oldalon, van jelszavunk.

És most? Fel kéne másolni a programunkat a szerverre. Igen ám, de hogyan?  Git-tel!

<!-- more -->
## Verziókövetés
A verziókövetés azt jelenti, egymás utáni mentések készülnek ugyanarról a fájlról, amik később összevethetők, sőt vissza is állíthatók. A különböző verziókat azonban nem külön-külön fájlokba mentjük, hanem a verziókövető rendszer a felhasználó számára láthatatlanul elintézi a mentést és a tárolást és a későbbi hozzáférést is.

A verziókövető rendszereket mentésen kivül közös munka koordinálására használják. A verziókövető rendszerek ugyanis képesek arra, hogy összefésüljenek különböző változásokat, amik egy fájlon történtek. Tehát ha ketten lemásolnak egy verziókövetett fájlt, mindketten beleírnak valamit, majd verziókövetővel elmentik, akkor ezekből a mentésekből előállitható egy olyan fájl, ami mindkettőjük változtatásait tartalmazni fogja. 
A verziókövetők használata nagyon elterjedt,  és a kódok megosztásának és másolásának is kedvelt eszköze. Ugyanis ha verziókövetővel másolunk kódot, akkor később a másolatban könnyen átvehetjük a frissítéseket is, vagy probléma esetén könnyen visszaállhatunk egy régebbi, hibamentes verzióra, anélkül, hogy az egészet egy az egyben át kéne másolni, vagy kézzel kéne szemezgetni. 

## Git
Az egyik újabb és igen elterjedt verziókövető a Git. Arra dolgozták ki, hogy a linuxot fejlesztő többezer ember munkáját lehessen vele követni.

A git működési terepe az úgynevezett repository, egy mappa, amiben olyan fájlok vannak, amiket lehet, hogy a gitnek követnie kell (meg ideteszi mindazon egyéb dolgokat, amik a működéséhez szükségesek).
Hozzunk létre egy repository-t  a *railsgirls* mappánkban:
	$ git init

Mondjuk meg a git-nek, hogy kik vagyunk és mi az email címünk. Erre azért van szükség, hogy közös munka esetén tudni lehessen, hogy melyik változtatást ki csinálta. 
	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"

### Mentés 
A gitben a mentés 2 lépésben működik. Először meg kell mondanunk, hogy a következő mentésnél melyik fájlokat akarjuk elmenteni. Ezekről a git nyilvántart egy listát, amire bármikor  fel lehet venni új elemeket (a `git add` paranccsal), vagy levenni róla valamit.  Amikor úgy érezzük, készen vagyunk a változtatásainkkal, kiadjuk a ` git commit` parancsot, ekkor történik a tényleges mentés. Minden commithoz kötelezően csatolni kell egy kis leírást arról, hogy mi van az aktuális mentésünkben (ez megint csak a közös munka miatt szükséges). 

Adjuk most hozzá az összes fájlunkat az elmentendő fájlok listájához, és commitoljunk:
	$ git add .
	$ git commit -m "elso commitom"

Egy pár hasznos parancs: a `git status` parancs megmondja, hogy mi van a mentendő fájlok listáján, mi nincs, (és ha vannak mik azok a fájlok, amiket a git egyáltalán nem is követ). A `git log` parancs kiirja az összes eddigi commitot. Mindegyikhez tartozik egy hosszú, betűkből és számokból álló egyedi azonositó (hash). A `git diff` parancs megutatja, hogy mi változott a legutóbbi commit óta. Ha pedig a `git diff` után két commitnak a hashét irjuk, akkor a két commit közti változtatásokat mutatja meg. 
COACHOK: próbáljátok ki ezeket a pranacsokat egy új commiton. 

### Közös munka, távoli repository-k 
A git repository-k tudhatnak egymásról. Ha egy repository tud egy másikról, akkor elkérheti tőle a benne lévő fájlokat, azoknak az egész verziótörténetével együtt (`git clone`), és később a bekövetkező változtatásokat is könnyen lehívhatja (`git pull`). Sőt, ha van jogosultsága, akár saját, új commitokat visszatolhat (`git push`). 
A közös munka attól válik egyszerűvé, hogy azok a repository-k, amikben a különböző fejlesztők dolgoznak, tudnak egymásról, és a fejlesztők könnyen lekérhetik egymástól az új változásokat, amiket a git szépen összefésül nekik a saját változásaikkal (de nem kell az egészet másolni). Azt is sokszor csinálják, hogy egy szerveren fenntartanak egy központi repository-t, mindenki oda pushol, és csak onnan pullol. Ráadásul a több repository biztonsági mentésként is szolgál: ha az egyik fejlesztőnek elfüstöl a gépe, a munkája továbbra is meglesz az összes többi fejlesztő repositoryjában.

 
És ezzel vissza is kanyarodhatunk: úgy fogunk másolni, hogy a Herokunk fogunk kapni egy üres repository-t, amibe be fogjuk tolni az imént elkészített, itteni repository-nk tartalmát. 

## HEROKU
Miért éppen a Heroku? leginkább azért, mert sokmindent megcsinál helyettünk, és főleg, mert nagyon kényelmesen kezelhető távolról. Ehhez rendelkezésre áll egy kliensprogram.

Először is installáljuk a klienst:
	$ gem install heroku

A heroku kliens a saját gépünkün fut, de mindenfélét el tud intézni nekünk a távoli heroku szerveren. Persze azért tudnia kell, hogy kik vagyunk, úgyhogy lépjünk be:
	$ heroku login
Kérni fogja a regisztrációhoz szükséges emailt és jelszót, adjuk meg.

### Egy kis előkészület
A Heroku szerver környezete kicsit más, mint a mi saját fejlesztői környezetünk a saját gépünkön, ezért némi változtatást kell eszközölnünk a programunkon. A *config/application.rb* fájlhoz az utolsó sor (az `end`) előtt adjuk hozzá ezt a sort:
``` ruby config/application.rb 
	config.assets.initialize_on_precompile = false

```

A Gemfájlban a

``` ruby Gemfile
gem 'sqlite3'

```
sort változtassuk meg erre:

``` ruby Gemfile
gem 'sqlite3', group: [:development, :test]

```

és adjuk hozzá ezeket a sorokat:

``` ruby Gemfile
group :production do
  gem 'thin'
  gem 'pg'
end

```

majd futtassuk le a konzolban:
	$ bundle install --without production

A `git status` paranccsal megnézhetjük, hogy 3 fájl változott (az a kettő, amit szerkesztettünk, és a *Gemfile.lock*). Nagyon fontos, hogy ezeket a változtatásokat is elmentsük: ezt egyetlen sorban megtehetjük a következő paranccsal
	git commit -a -m"heroku elokeszites"

### Másolás!
A heroku kliens segjtségével létrehozunk egy új, üres alkalmazást a szerveren:
	$ heroku create

A kimenetből látszik, hogy létrejött az alkalmazás webcíme, meg egy másik cím, ami .git-re végződik. Ez a szerveren lévő repository-nak a címe, amibe a programunkat másolni fogjuk, ráadásul az itteni repository-nkból elérhető heroku becenéven.  
Most jött el az ünnepélyes pillanat, pusholunk:
	git push heroku master

Ha nem engedne be, akkor készítsünk ssh kulcsot és töltsük fel:
	$ ssh-keygen -t rsa
	$ heroku keys:add

A kimenetből látszik, hogy a Heroku szerver felismerte hogy ruby on rails alkalmazást töltünk be, és telepítette az összes szükséges programokat és gem-eket, sőt azt is ellenőrzi, hogy futtatható-e az alkalmazásunk. 

A későbbiekben, ha változtatunk a programon, akkor (commit után) megint bepusholhatjuk a változtatásokat.

Már majdnem készen vagyunk. De. A git ugyan követi a fájljaink szöveges tartalmát, de az adatbázisba sajnos nem lát bele. Úgyhogy a Heroku szerveren még nem jött létre adatbázis, ott is le kell futtani a `rake db:migrate` parancsot. Szerencsére a `heroku run` parancs akármit lefuttat nekünk, amit mögé írunk:
	$ heroku run rake db:migrate

Készen is vagyunk, meg akarjuk nyitni a programunkat böngészőben, a `git push` kimenetének a végén ott volt a link. De ha nem figyeltünk volna, a `heroku apps:info` parancsra a kliens kiírja az alkalmazásunk adatait, és ezek közt megtaláljuk az url-t, amin az alkalmazás elérhető. (Ha ezt egérrel kijelüljük, akkor a jobb egérgombbal menüből megnyitható.) 
Nézzünk körül, mindennek ugyanúgy kell működnie, mint a saját gépünkön. A heroku.com oldalon belépés után láthajuk a létrejött alkalmazást, és hogy vannak minednféle beállítási lehetőségek.

