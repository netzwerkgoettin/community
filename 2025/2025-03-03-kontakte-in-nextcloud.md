---
title: "Kontakte in Nextcloud"
vgwort_public: "d71c000fb4524c39b2d294efd0a0989c"
date: 2025-03-03 23:04 +0200
categories:
  - "online"
tags:
  - "community"
  - "ownyourdata"
description: "Kontakte in Nextcloud statt iCloud verwalten"
image: "/assets/img/community/2025/03/Pfirsichbluete.jpg"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2025/03/Pfirsichbluete.jpg') center/cover
  overlay: false
---

Die aktuelle weltpolitische Lage gibt viel Anreiz zum Nachdenken.
Und bedauerlicherweise sind die meisten dieser Gedanken eher weniger motivierend.
Ich habe noch nie über Politik gebloggt – es gibt genügend Menschen da draußen, die das bedeutend besser können als ich, und deshalb werde ich daran auch nichts ändern.\\
Aber über technische Themen bloggen – **das** kann ich.

* Replaced by the toc
{:toc}

# Vorwort
Und was mir schon immer ein Anliegen war, ist die so-viel-wie-mögliche Unabhängigkeit von [„Big Tech“](https://de.wikipedia.org/wiki/Big_Tech), Clouds, [„Vendor Lock-Ins“](https://www.heise.de/tipps-tricks/Vendor-Lock-in-was-ist-das-6166269.html) und ähnlichem – **„Own Your Data!“** eben.
Darüber habe ich hier nie groß geschrieben – warum eigentlich?
Vielleicht, weil es für mich so *normal* war?
Oder weil ich im Bekanntenkreis üblicherweise auf wenig Verständnis stieß – *„Warum so misstrauisch, so kompliziert?“*

Ja, es stimmt schon: auf diese Cloud-Dinge zu verzichten, kann Dinge tatsächlich komplizierter machen – manche macht es unmöglich.
Es benötigt technische Expertise auf einem bestimmten Level, und häufig geht es mit einem signifikanten Verlust an Komfort einher.
Für mich ist es den Preis jedoch schon immer wert gewesen.\\
Was ich jetzt jedoch auffange ist, dass eine zunehmende Zahl an Menschen sich mit dieser Thematik auseinandersetzt.
Und an diese Gruppe richte ich mich, indem ich in Zukunft mal ein bisschen mehr aus dem Nähkästchen plaudere, wie ich Dinge für mich gelöst habe: Kontakte, Kalender, Fotos, Passwörter...
Das wird – so meine Hoffnung – auch anderen helfen.

Und idealerweise hilft es umgekehrt auch mir – denn an manchen Punkten komme ich selbst nicht recht weiter.

# Voraussetzungen
Meine derzeitigen Voraussetzungen sind folgende:

* ich bin seit über 20 Jahren Apple-Nutzerin, sowohl mit wechselnden Rechnern, als auch mit wechselnden iPhones
* ich betreibe einen Root-Server bei [Netcup](https://www.netcup.com/de) am Standort Nürnberg
* auf diesem Root-Server betreibe ich unter anderem eine [Nextcloud](https://nextcloud.com/de/); und auch wenn ich mit der nicht ausnahmslos glücklich bin (Stichworte „PHP“ und „AI Integration“ und so) ist mir bislang kein kleineres Übel untergekommen, das alle benötigten Funktionalitäten zur Verfügung stellen würde

# Verwaltung der Kontakte
Im heutigen Artikel soll es ja um die Kontaktverwaltung gehen.
Um die zu ermöglichen, habe ich in meiner Nextcloud-Instanz ist die App [Contacts](https://apps.nextcloud.com/apps/contacts) installiert.
Die ermöglicht es mir, per Webinterface meine Kontakte zu erfassen – ihre Namen, Telefonnummern, E-Mail-Adressen, Geburtsdaten etc.

# Einbindung der Nextcloud-Kontakte
Charmant wird das Ganze allerdings erst, wenn ich es auch meinen übrigen Geräten als Quelle hinzufüge.
Um das zu tun, benötige ich die CardDAV-Adresse, und die finde ich auf folgendem Weg:

* das Webinterface von Nextcloud öffnen
* *Contacts* auswählen
* im linken Bereich *All contacts* auswählen
* im linken unteren Bereich *Contacts settings* auswählen &rarr; es öffnet sich ein Overlay
* im Bereich *Address Books* schwirren die Adressbücher herum, die man sich so angelegt hat; das, in dem all meine Kontakte sich befinden, heißt auch wirklich *Contacts*; am Ende der Zeile sind drei Punkte, und unter diesen drei Punkten findet sich ein *Copy link*
* diesen Link (er hat in etwa die Form `https://cloud.example.com/remote.php/dav/addressbooks/users/<USERNAME>/contacts/`) als *Server Address* einsetzen

Über diese Adresse kann die Einrichtung nun erfolgen.

![Contacts settings](/assets/img/community/2025/03/Contacts_Settings.jpg){:.lead width="100%" loading="lazy"}
Screenshot „Contacts settings“
{:.figcaption}

## Desktop
Auf dem Desktop-Mac geht das über *System Settings &rarr; Internet Accounts &rarr; Add Account &rarr; Add Other Account &rarr; CardDAV Account*.
Der *Account Type* ist in dem Fall vom Typ *Manual*, Username und Passwort sind jene, die ich in meiner Nextcloud verwende – oder statt des Passworts ein entsprechendes App-Passwort, sofern ich das in Nextcloud konfiguriert habe.
Als *Server Address* ist die oben ermittelte einzusetzen.

Nach erfolgreicher Einrichtung lassen sich die Kontakte dann auch über die `Contacts.app` von Apple editieren.

## iPhone
Auf dem iPhone geht das über *Settings &rarr; Apps &rarr; Contacts &rarr; Contacts Accounts &rarr; Add account &rarr; Other &rarr; Add CardDAV account*.
Wiederum Zugangsdaten und oben ermittelte *Server Address* angeben.

Nach erfolgreicher Einrichtung lassen sich die Kontakte dann auch über *Telephone &rarr; Contacts* am iPhone direkt editieren.


# Deaktivieren von iCloud für Kontakte
Ein wichtiger abschließender Punkt fehlt noch: den Endgeräten abzugewöhnen, die sensiblen Kontaktdaten in die iCloud zu stopfen.

## Desktop
Am Desktop geht das über *System Settings &rarr; Internet Accounts &rarr; iCloud*, hier bei „Saved to Cloud“ auf *See All* und dann bei *Contacts* den Schieber deaktivieren.

## iPhone
Auf dem iPhone geht das über *Settings &rarr; iCloud*, bei „Saved to iCloud“ auf *See All*  und dann bei *Contacts* den Schieber deaktivieren.

# Abschließend
Ich nutze das schon seit vielen Jahren so, und es hat für mich immer gut funktioniert.
Haben die Endgeräte keine Verbindung, werden etwaige Änderungen übertragen, sobald es eben wieder geht.
An dieser Stelle kann ich für mich keinen Komfortverlust wahrnehmen.

Was in meinem Fall deutlich unterrepräsentiert ist, ist Android in all seinen Erscheinungsformen.
Es ist gut möglich, dass sich das in Zukunft ändern wird – im Moment kann ich lediglich auf Erfahrungswerte mit Apple-Endgeräten zurückblicken.

[**Aber vielleicht magst du diesbezüglich etwas ergänzen?**](https://github.com/netzwerkgoettin/community/tree/main/2025)