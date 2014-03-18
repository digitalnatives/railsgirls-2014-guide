---
layout: post
title: "Második fejezet -  Hiszen ez él!"
date: 2014-03-20 09:17:10 +0100
comments: false
categories:
published: false
---
Ez a fejezet két dologról fog szólni: verziókövetésről és webszerverekről. 

Azt szeretnénk, hogy a programunk egy olyan szerveren fusson, ami mindig be van kapocsolva, és lehetőleg egy állandó címen legyen elérhető. 
Szóval mire van szükségünk? egy webszerverre, ami: tárhelyet és fix címet ad az alkalmazásunknak, tudja futtatni a ruby on rails alkalmazásokat, fel lehet rá telelpíteni mindazon gem-eket, amikre az alkalmazásunkak szüksége van, ad adatbázis szolgáltatást, lehetőleg legyen ingyen:)

A Heroku pont egy ilyen szolgáltatás (és nem ez az egyetlen). Ezen kívül még van egypár jó tulajdonsága, amiről majd később szó lesz. Most egyelőre örülünk, hogy megtaláltuk, regisztráltunk a https://id.heroku.com/login oldalon, van jelszavunk. 

És most? Fel kéne másolni a programunkat a szerverre. Igen ám, de hogyan?  Git-tel!

<!-- more -->
## Verziókövetés
A verziókövetés azt jelenti, egymás utáni mentések készülnek ugyanarról a fájlról, amik később összevethetők, sőt vissza is állithatók. A különböző verziókat azonban nem külön-külön fájlokba mentjük, hanem a verziókövető rendszer a felhasználó számára láthatatlanul elintézi a mentést és a tárolást és a későbbi hozzáférést is.

A verziókövető rendszereket mentésen kivül közös munka koordinálására használják. A verziókövető rendszerek ugyanis képesek arra, hogy összefésüljenek különböző változásokat, amik egy fájlon történtek. Tehát ha ketten lemásolnak egy verziókövetett fájlt, mindketten belejrnak valamit, majd verziókövetővel elmentik, akkor ezekből a mentésekből előállitható egy olyan fájl, ami mindkettőjük változtatásait tartalmazni fogja. 
A verziókövetők használata nagyon elterjedt,  és a kódok megosztásának és másolásának is kedvelt eszköze. Ugyanis ha verziókövetővel másolunk kódot, akkor később a másolatban könnyen átvehetjük a frissjtéseket is, vagy probléma esetén könnyen visszaállhatunk egy régebbi, hibamentes verzióra, anélkül, hogy az egészet egy az egyben át kéne másolni, vagy kézzel kéne szemezgetni. 

## Git
Az egyik újabb és igen elterjedt verziókövető a Git. Arra dolgozták ki, hogy a linuxot fejlesztő többezer ember munkáját lehessen vele követni. 

A git működési terepe az úgynevezett repository, egy mappa, amiben olyan fájlok vannak, amiket lehet, hogy a gitnek követnie kell (meg ideteszi mindazon egyéb dolgokat, amik a működéséhez szükségesek). 
Hozzunk létre egy repository-t  a *railsgirls* mappánkban:
	git init

Mondjuk meg a git-nek, hogy kik vagyunk és mi az email címünk. Erre azért van szükség, mert közös munak esetén nem árt, ha tudjuk, melyik változtatást ki csinálta. 
	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"

### Mentés 
A gitben a mentés 2 lépésben működik. Először meg kell mondanunk, hogy a következő mentésnél melyik fájlokat akarjuk elmenteni. Ezekről a git nyilván tart egy listát, amire bármikor  fel lehet venni új elemeket (a `git add` paranccsal), vagy levenni róla valami.  Majd pedig amikor úgy érezzük, készen vagyunk a változtatásainkkal, kiadjuk a ` git commit` parancsot. Minden commithoz kötelezően csatolni kell egy kis lejrást arról, hogy mi van az aktuális mentésünkben (ez megint csak a közös munka miatt szükséges). 

Adjuk most hozzá az összes fájlunkat az elmentendő fájlok listájához, és commitoljunk: 
	git add .
	git commit -m"elso commitom"

Egy pár hasznos parancs: a `git status` parancs megmondja, hogy mi van a mentendő fájlok listáján, mi nincs, (és ha vannak mik azok a fájlok, amiket a git egyáltalán nem is követ). A `git log` parancs kiirja az összes eddigi commitot. Mindegyikhez tartozik egy hosszú, betűkből és számokból álló egyedi azonositó (hash). A `git diff` parancs megutatja, hogy mi változott a legutóbbi commit óta. Ha pedig a `git diff` után kék commitnak a hashét irjuk, akkor a két commit közti változtatásokat mutatja meg. 
COACHOK: próbáljátok ki ezeket a pranacsokat egy új commiton. 

A továbbiakban nyugodtan használhatjátok a gitet a munkátok elmentésére, pl minden fejezet végén. 
A következő paranccsal ez egy lépésben megtehető:
	git commit -a -m "commit uzenet"

### Közös munka, távoli repository-k 
A git repository-k tudhatnak egymásról. Ha egy repository tud egy másikról, akkor elkérheti tőle a benne lévő fájlokat, azoknak az egész verziótörténetével együtt (`git clone`), és később a bekövetkező változtatásokat is könnyen lehjvhatja (`git pull`). SŐt, ha van jogosultsága, akár saját, új commitokat visszatolhat (`git push`). 
A közös munka attól válik egyszerűvé, hogy azok a repository-k, amikben a különböző fejlesztők dolgoznak, tudnak egymásról, és a fejlesztők könnyen lekérhetik egymástól az új változásokat, amiket a git szépen összefésül nekik a saját változásaikkal. Illetve, azt is sokszor csinálják, hogy fenntartanak egy központi repository-t, mindenki oda pushol, és csak onnan pullol. Ráadásul a több repository biztonsági mentésként is szolgál: ha az egyik fejlesztőnek elfüstöl a gépe, a munkája azért megvan az összes többi fejlesztő repositoryjában.

 
És ezzel vissza is kanyarodhatunk: úgy fogunk másolni, hogy a Herokunk fogunk kapni egy üres repository-t, amibe be fogjuk tolni az imént elkészjtett, itteni repository-nk tartalmát. 

## HEROKU
Miért éppen a Heroku? leginkább azért, mert sokmindent megcsinál helyettünk, és főleg, mert nagyon kényelmesen kezelhető távolról. Ehhez rendelkezésre áll egy kliensprogram.

Először is installáljuk a klienst:
	gem install heroku

A heroku kliens a saját gépünkün fut, de mindenfélét el tud intézni nekünk a távoli heroku szerveren. Persze azért tudnia kell, hogy kik vagyunk, úgyhogy lépjünk be:
	heroku login
Kérni fogja a regisztrációhoz szükséges emailt és jelszót, adjuk meg. 

### Egy kis előkészület
A Heroku szerver környezete kicsit más, mint a mi saját fejlesztői környezetünk a saját gépünkön, ezért némi változtatást kell eszközölnünk a programunkon. A *config/application.rb* fájlhoz az utolsó sor( az end) előtt adjuk hozzá ezt a sort:
```config/application.rb linenos:false
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

``` Gemfile
 group :production do
     gem 'thin'
     gem 'pg'
 end

``` 

majd futtassuk le a konzolban:
	bundle install --without production


### Másolás!
A heroku kliens segjtségével létrehozunk egy új, üres alkalmazást a szerveren: 
	heroku create

A kimenetből látszik, hogy létrejött az alkalmazás webcime, meg egy másik cjm, ami .git-re végződik. Ez a szerveren lévő repository-nak a cjme, amibe a programunkat másolni fogjuk, ráadásul az itteni repositorynkból elérhető heroku becenéven.  
Most jott el az ünnepélye spillanat, pusholunk:
	git push heroku master

(Ha nem engedne be, akkor készítsünk ssh kulcsot és töltsük fel:
	ssh-keygen -t rsa
	heroku keys:add
)

A kimenetből látszik, hogy a Heroku szerver felismerte hogy ruby on rails alkalmazást toltunk be, és telepjtette asz összes szükséges programokat és gem-eket, sőt azt is ellenőrzi, hogy futtatható-e az alkalmazásunk. 

A későbbiekben, ha változtatunk a programon, akkor (commit után) megint bepusholhatjuk a változtatásokat. 

Már majdnem készen vagyunk. De. A git ugyan követi a fájljaink szöveges tartalmát, de az adatbázisba sajnos nem lát bele. Úgyhogy a Heroku szerveren még nem jött létre adatbázis, ott is le kell futtani a `rake db: create` parancsot. Szerencsére a `heroku run` parancs akármit lefuttat nekünk, amit mögé jrunk:
	heroku run rake db:create

Készen is vagy`unk, meg akarjuk nyitni a programunkat böngészőben, a `git push` kimenetének a végén ott volt a link. De ha nem figyeltünk volna, a `heroku apps` parancsra a kliens kiírja, hogy milyen nevű alkalmazásaink vannak (hiszen lehet több is), a `heroku open programneve` pedig megnyitja nekünk böngészőben. 
Nézzünk körül, mindennek ugyanúgy kell működnie, mint a saját gépünkön. A heroku.com oldalon belépés után láthajuk a létrejött alkalmazást, és hogy vannak minednféle beállítási lehetőségek. 
 
