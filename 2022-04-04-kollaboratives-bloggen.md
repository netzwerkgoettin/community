---
title: "Kollaboratives Bloggen"
vgwort_public: "1fa216df7487408f85e7bcfed3cc09ca"
date: 2022-04-04 09:38:28 +0200
categories:
  - "online"
tags:
  - "community"
  - "unixeschreibt"
description: "Ein Feldtest"
image: "/assets/img/community/2022/04/Eisbecher.jpg"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2022/04/Eisbecher.jpg') center/cover
  overlay: false
---

Mein Plan eines *kollaborativen Bloggens* geistert mir schon länger im Kopf herum; jetzt setze ihn endlich auch um.

* Replaced by the toc
{:toc}

Ich habe in den letzten Tagen mal wieder großes Blog-Umgraben veranstaltet: JS auf aktuellen Stand gebracht, das Laden der Google-Fonts von extern eliminiert, ein paar (wenn auch noch nicht alle) *Unschönigkeiten* gefixt, das Repository aufgeräumt.

Es läuft wirklich gut [seit der Umstellung auf Jekyll](/von-wordpress-zu-jekyll/) – es kommt wieder etwas Bewegung in das Projekt, Artikel-Entwürfe stecken in der Queue. Die Umgewöhnung auf den neuen Workflow war enorm, und ich hab mich nicht an allen Stellen leicht damit getan; inzwischen weiß ich aber schon gar nicht mehr, wie es je anders gehen konnte.

Und endlich hatte ich nun auch Zeit und Lust, den Plan des „kollaborativen Bloggens“ final umzusetzen.

## Die Idee...
Andere machen das schon länger und umfassender, [so wie Kris zum Beispiel](https://blog.koehntopp.info/contribute/), und mir gefällt die Idee ausnehmend gut: liest man einen Artikel und hat Ergänzungen/ findet Schreibfehler/ whatever, so wandert man auf GitHub und *macht selbst einfach mal eben* – statt den Urhebenden (gibt es das Wort?) direkt anzuschreiben (was bei großer Nachrichtenmenge auch gerne mal untergehen kann). Unterm Strich und wenn es gut läuft profitieren da alle von.

Für einen Teilbereich meines Blogs habe ich das nun auch umgesetzt: [Artikel mit dem Tag `community`](/tag-community/) stecken in Zukunft [in einem öffentlichen Repository auf GitHub](https://github.com/netzwerkgoettin/community).
Ich überlege noch, welche der bestehenden Artikel ich eventuell ebenfalls dorthin umziehen werde.
Solltet ihr Ergänzungen haben, von denen ihr der Meinung seid sie bieten einen **unbedingten Mehrwert für die Netzgemeinde** (und damit meine ich nicht *„dieses Shell-Kommando wäre `$anders` aber `$schöner`“*) steht es euch zukünftig also frei, PRs zu erstellen.

## Warum nicht das gesamte Blog auf github.io?
Dafür gibt es mehrere Gründe, und jeder ist für sich gesehen schon K.O.-Kriterium für mich: die sehr alten technischen Inhalte zum Beispiel (das Blog ist im 16. Jahr) sind generell *frozen* – sie stellen den Ist-Zustand von Damals dar, mehr nicht und nicht weniger.

Weiter habe ich mehrere Jekyll-Plugins eingebunden, deren Einsatz auf `github.io` nicht erlaubt wäre – ein Verzicht käme mir aber einem Rückschritt gleich. Und den möchte ich nicht.

Nicht zuletzt würde ich nicht alles und jedes, was ich schreibe, von einer gesichtslosen Menge „verbessern“ lassen wollen: `unixe.de` ist kein reines Tech-Blog (und war es auch nie), viele Artikel sind sehr persönlicher Natur und **mein** sozusagen, **mein ganz allein**.

## Bitte seid konstruktiv – und nett!
Ich freue mich über jeden PR, es sei denn, er ist blöd – dann nämlich nicht. Vergreift er sich im Ton oder ist reine Selbstdarstellung, lese ich ihn mit hoher Wahrscheinlichkeit nicht zu Ende und schließe ihn.

## Fazit: schaunmermal!
Wenn man solche Dinge frisch angeht, bildet man sich ja gerne ein, innerhalb kürzester Zeit in PRs zu ertrinken... ich vermute eher nicht, dass das der Fall sein wird 😂 Ich mach das und schaue, inwiefern es sich bewährt; übersteigt der Nervfaktor den Nutzen, ist es (*„Git sei Dank“*, haha!) ja auch schnell wieder rückgängig gemacht.
