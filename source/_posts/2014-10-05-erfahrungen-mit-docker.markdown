---
layout: post
title: "Erfahrungen mit Docker"
date: 2014-10-05 11:29:43 +0000
comments: true
published: false 
categories: Docker
---
Ein Bekannter bat mich, einmal von meinen Erfahrungen mit Docker zu
berichten. Also hier als Blog-Post dazu. Ich werde nicht beschreiben,
was genau Docker ist und wie es funktioniert. Hierzu gibt es
mittlerweile schon eine Reihe von guten Quellen (bspw. auf der
Docker-Homepage, in der c't war in Heft 17 (Seiten 146-151) auch eine
ganz gute Einführung enthalten).

Hintergrund
-----------

Seit einigen Jahren verwende ich zum Ausrollen von Software OpenVZ als
Container-Lösung. Das funktioniert sehr gut, schont die Ressourcen,
lässt sich gut verwalten und überwachen. In den OpenVZ-Containern
befindet sich ein kleines Linux der gewünschten Distribution mit so
allem, was man im Standard dafür erwartet. Darin installiert und
konfiguriert man seine Software und rollt sie dann auf dem Zielsystem
bequem aus. Der einzige Nachteil hierbei ist, dass der Host einen
eigenen OpenVZ-Kernel benötigt, da nicht oder noch nicht alle Features
im Standard-Kernel bereit stehen. Das macht das Zusammenstellen auf
meinem Notebook etwas schwierig, denn dort habe ich den OpenVZ-Kernel
in der Regel nicht installiert. 

Warum nun Docker?
-----------------

Im April bin ich eher zufällig (tauchte in den Release-Notes von
Ubuntu 14.04 auf) auf das Docker-Projekt gestoßen und habe damit
begonnen herumzuexperimentieren. 

Rund um Docker und den Einsatz haben sich eine Vielzahl von Projekten
angesammelt. Nicht alles verstehe ich auf Anhieb und mir fällt es zum
Teil auch schwer, die unterschiedlichen Projekte zum Ausrollen,
Verwalten etc. zu bewerten und zu entscheiden, was davon auch
"zukunftsfähig" ist. Wahrscheinlich muss man hier auch ein bisschen
Geduld haben.


Einsatzbereiche
---------------

Für mich haben sich im Wesentlichen zwei Haupteinsatzbereiche herauskristallisiert: 
* als Werkzeug in der Software-Entwicklung und 
* als Werkzeug zum Ausrollen und dauerhaften Betrieb von Anwendungen 


### Werkzeug in der Software-Entwicklung

Ich arbeite in Teams zusammen, bei denen die Entwickler mal nen
Windows-Notebook und mal nen Linux-Notebook ihr Eigen nennen. Docker
bot sich hier an, eine definitierte Test- und Entwicklungsplattform
bereit zu stellen. Ich habe dazu alles, was man so braucht in ein
Image zusammengeworfen, dies lässt sich mit dem aktuellen Source Code
starten.

### Dauerhafter Betrieb

Eigenarten und Erfahrungen
--------------------------

Was toll ist:
-------------

* Sehr leicht zu installieren und man kann schnell damit anfangen herumzuspielen. 
* docker build / Dockerfile 
* Es entwickelt sich sehr schnell eine aktive Gemeinschaft, so habe
  ich auch schon an MeetUps in Frankfurt und Darmstadt
  mit Gewinn teilgenommen. 
* Nicht zu vergessen das Logo. 


Was nicht so toll ist:
----------------------

* Images und Container erhalten zur Identifikation Hash-Werte; man
  kann Images und Container auch mit Namen versehen. Ist man
  allerdings ein wenig am experimentieren und herumbasteln, so sammeln
  sich schnell eine Reihe von Containern mit wenig sprechenden Ids an
  und zumindest ich verliere da schnell den Überblick.

* Eigentlich sind die Container nicht so mobil, wie die Analogie es
  vermuten lässt. Am ehesten sind es noch die Images, die man in eine
  Registry einspielen und dann auf unterschiedlichen Systemen
  herunterlädt. Mir würde allerdings auch so etwas wie Live Migration
  (funktioniert beispielsweise bei OpenVZ für meine Einsatzzwecke ganz
  hervorragend) gefallen. 

* Für den dauerhaften Betrieb gibt es eine Reihe von für mich offenen
  Fragen. Hierzu gibt es zwar Lösungsansätze oder Produkte, aber viele
  entwickeln sich erst. Ich will einmal exemplarisch einige offene
  Enden herausgreifen:

  - Irgendwie muss man sich um seine Logfiles kümmern, wo die so
    landen sollen. Hat man in einem "normalen Linux" (oder auch einem
    Standard-OpenVZ-Container) meist so etwas wie ein logrotated am
    laufen, so fehlt das (wenn man der Docker-Philosophie folgt, die
    Container so schlank wie möglich zu halten) in einem
    Docker-Deployment. Also muss man für jede Anwendung prüfen:
    Schreibt sie ihr Log auf Platte? Dann muss man das da irgendwie
    herausholen (bspw. über einen Log-File-Volume) oder überwachen,
    nutzt sie syslogd muss man das auch irgendwie in den Griff
    bekommen oder rotzt sie ihre Meldungen über die Standardausgabe
    heraus, dann sammelt immerhin Docker die auf und man kann darin
    blättern (die können dort aber auch recht groß werden).
  
  - Was tut man mit persistenten Daten? Also beispielsweise einer
    Datenbank? Der Docker-Weg-zum-Glück bietet an, dass man einen
    Container mit der Datenbank erstellt und dann einen für die
    jeweilige Anwendung (ist bspw. für das Wordpress-Image auch so gut
    zu studieren). Damit die Daten der Datenbank nicht in dem
    Container herumliegen, kann man diese noch in ein Volume
    packen. Damit die beiden Container nun miteinander kommunizieren
    können, kann man deren Daten über Links einander bekannt
    machen. Da man sich als Container und Anwendung nicht um
    IP-Adressen etc. kümmern "soll", ist das auch ganz fein. So erhält
    man im Anwendungscontainer dann Umgebungsvariablen zum
    Datenbankcontainer sowie auch einen Eintrag in /etc/hosts. Dumm
    nur, wenn der Datenbankcontainer mal abschmiert und neu gestartet
    werden muss, denn dann erhält er in der Regel eine neue IP-Adresse
    und der Link vom Anwendungscontainer zur Datenbank funktioniert
    nicht mehr. Sicher, es gibt nun Mittel und Wege damit umzugehen
    (DNS-Geschichten, Entdecken der Dienste, etc.), aber ich fände es
    eigentlich toll, wenn das Out-of-the-box funktionieren würde.
    
Fazit
-----

Ich finde es ein spannendes Projekt und wenn sich für mich die einen
oder anderen Unklarheiten lichten, werde ich es sicherlich auch für
den Produktiveinsatz in Erwägung ziehen. Ich vermute, dass das Projekt
sich noch deutlich weiterentwickeln wird, die knapp 65 Mio Venture
Capital werden neben Marketing hoffentlich auch ins Produkt gesteckt.