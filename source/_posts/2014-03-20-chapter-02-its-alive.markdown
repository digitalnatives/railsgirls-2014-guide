---
layout: post
title: "Második fejezet -  Hiszen ez él!"
date: 2014-03-20 09:17:10 +0100
comments: false
categories:
published: true
---
Ez a fejezet két dologról fog szólni: verziókövetésről és webszerverekről. 

A Heroku egy webszerver szolgáltatás, ami webes alkalmazások futtatására lett kiépítve. Ha az alkalmazásunk itt fut, akkor mindig mindenhonnan elérhető lesz. Ehhez azonban fel kell másolnunk az alkalmazást a szerverre. Node hogyan? Az egyik széles körben elterjedt megoldás erre a git. 

<!-- more -->
## GIT
A git egy verziókövető rendszer. A verziókövetés azt jelenti, hogy a git ugyanannak a fájlnak különböző verzióit eltárolja, így a különböző verziókat összevethetjük, vagy egy régebbit visszaállíthatunk. 

A git működési terepe az úgynevezett repository, egy mappa, amiben olyan fájlok vannak, amiket lehet, hogy a gitnek követnie kell. 
Hozzunk létre egy repository-t:
	git init

Mondjuk meg a git-nek, hogy kik vagyunk és mi az email címünk:
	git config --global user.email "you@example.com"
	git config --global user.name "Your Name"

Adjuk hozzá az összes fájlunkat az elmentendő fájlok halmazához és mentsük el: 
	git add .
	git commit -m"elso commitom"

COACHOK: magyarázzátok el ezeket a parancsokat. Hozzatok létre még egy commitot, és vessétek össze az előző állapottal. Mutassátok meg a status, a log és diff parancsokat. 

A továbbiakban nyugodtan használhatjátok a gitet a munkátok elmentésére, pl minden fejezet végén. 
A következő paranccsal ez egy lépésben megtehető:
	git commit -a -m "commit uzenet"

Szóval mire kellett nekünk ez az egész? Azért mert a git nem csak a verziókövetés eszköze, hanem a közös munkáé, illetve a megosztásé is. Segítségével a helyi repository-nk tartalmát könnyen átmásolhatjuk egy másikba, pl egyenesen fel a Heroku szerverre. 

## HEROKU
(Ugye mindenkinek van már Heroku regisztrációja? A https://id.heroku.com/login oldalon e-mail címmel és egy jelszóval kell tudni belépni.)

Miért éppen a Heroku? leginkább azért, mert sokmindent megcsinál helyettünk, és mert nagyon kényelmesen kezelhető távolról.  Ehhez rendelkezésre áll egy lokálisan futtatható kliensprogram, aminek a segítségével egyszerűen tudjuk a szerveren lévő alkalmazást és a környezetét is kezelni. 

Először is installáljuk a klienst:
	gem install heroku
majd lépjünk be:
	heroku login
Kérni fogja a regisztrációhoz szükséges emailt és jelszót, adjuk meg. 

A Heroku szerver környezete kicsit más, mint a mi saját fejlesztői környezetünk a saját gépünkön, ezért némi változtatást kell eszközölnünk a programunkon. A `config/application.rb` fájlhoz az utolsó sor( az end) előtt adjuk hozzá ezt a sort:
	config.assets.initialize_on_precompile = false

A Gemfájlban a 
	gem 'sqlite3' 
sort változtassuk meg erre:
	gem 'sqlite3', group: [:development, :test]

és adjuk hozzá ezeket a sorokat:
	group :production do
	  gem 'thin'
	  gem 'pg'
	end
majd futtassuk le a konzolban:
	bundle install --without production



A heroku klienssel hozzunk létre egy új, üres alkalmazást a szerveren: 
	heroku create

Az üres alkalmazással együtt létrejött a szerveren egy git repository, ahova betolhatjuk az alkalmazásunkat:  
	git push heroku master

(Ha nem engedne be, akkor készítsünk ssh kulcsot és töltsük fel:
	ssh-keygen -t rsa
	heroku keys:add
)

A későbbiekben, ha változtatunk a programon, akkor (commit után) megint bepusholhatjuk a változtatásokat. 


Még egy pár apróság kell:
	heroku run rake db:create
	heroku ps:scale web=1

A heroku apps parancsra a kliens kiírja, hogy milyen nevű alkalmazásaink vannak (hiszen lehet több is). 
	heroku apps

Az alkalmazás neve mindenkinek más, a Heroku generálja. Rögtön meg is nyithatjuk a heroku open paranccsal:
	heroku open <programneve>

Nézzünk körül, mindennek ugyanúgy kell működnie, mint a saját gépünkön. A heroku.com oldalon belépés után láthajuk a létrejött alkalmazást, és hogy vannak minednféle beállítási lehetőségek. 
 
