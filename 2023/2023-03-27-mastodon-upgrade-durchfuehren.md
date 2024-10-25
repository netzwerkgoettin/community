---
title: "Mastodon: Upgrades durchführen"
vgwort_public: "6fc0c0672e3e434a9da8a4881fb01b8d"
date: "2023-03-27 07:01 +0200"
last_modified_at: "2024-10-25 15:16 +0200"
categories:
  - "online"
tags:
  - "communications"
  - "community"
description: "Upgrade-Prozess der Mastodon-Instanz konfigurationsmanufaktur.de"
image: "/assets/img/community/2023/03/Holzmann.jpg"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2023/03/Holzmann.jpg') center/cover
  overlay: false
---

In mehr oder minder regelmäßigen Abständen ist es nötig, der Instanz-Software ein Upgrade zu verpassen.

> **Pro-Tipp:** Um die Veröffentlichung neuer Releases nicht zu verpassen, habe ich mir [die GitHub-Announcements per Atom](https://github.com/mastodon/mastodon/releases.atom) in meinen Feed-Reader geworfen.
Antiquiert?
Feeds werden meiner Meinung nach zu Unrecht verlacht!
Ich arbeite nach wie vor total gerne damit, Feeds sind *rock solid*, wiegen nichts – und funktionieren halt einfach.

Ich spiele die Updates möglichst zeitnah ein.
Es ist ein von außen erreichbarer Dienst, da ist es mir wichtig, auf Stand zu sein.

Ich skizziere also jetzt mal (wie [im letzten Artikel versprochen](/eine-eigene-mastodon-instanz-in-docker/)) kurz, wie so ein Upgrade – für mich zumindest – funktioniert.

Ich arbeite bei solchen Dingen üblicherweise in einem `screen`.
```bash
screen -DRS mastodon_upgrade
```

Der erste Schritt besteht darin, einen Dump der Datenbank zu erzeugen:
```bash
cd $HOME/mastodon
docker exec mastodon-db-1 \
pg_dump -Fc -U postgres postgres \
> postgres-$( date +%Y%m%d ).dump
```

Jetzt alle Container stoppen – [in meinem Setup](/eine-eigene-mastodon-instanz-in-docker/) liegen alle Files im `$HOME` des ausführenden Users.
```bash
docker compose down
```

Zur Sicherheit ziehe ich mit eine Kopie der bisherigen funktionierenden Installation; den Cache lasse ich dabei jedoch außen vor.
```bash
cd .. 
rsync -azvpP --progress \
--exclude mastodon/public/system/cache/ \
--exclude mastodon/.git \
mastodon mastodon-$( date +%Y%m%d )
```

Dann wechsle ich wieder in den Ordner und bringe das Repository auf den neuesten Stand.
```bash
cd mastodon
git fetch
```

In meinem Fall weicht die `docker-compose.yml` vom GitHub-Default ab, da ich sie an meine Gegebenheiten anpassen musste; über `git status` lässt sich das einsehen.
Deshalb werfe ich die auf den Stapel nicht eingecheckter Änderungen.
```bash
git stash
```

Ich wusste dank des Feeds schon, dass beim heutigen Update die aktuelle Version die `v4.1.1` sein würde – `git fetch` listet mir aber ebenfalls die jeweils neuen Tags.
```bash
## git checkout <version/tag>
git checkout v4.1.1
```

Jetzt muss ich meine vollgekritzelte `docker-compose.yml` wieder vom Stapel retten.
```bash
git stash pop
```

Anschließend gleiche ich sie mit der des aktuellen Checkouts ab und übernehme eventuelle Änderungen entsprechend.
Und dann erstelle ich das Docker-Image – und führe eventuell anstehende Migrationen durch.
Letztere sind nicht immer nötig.
Das Changelog des Projekts ist bei jedem Upgrade einen Blick wert!
```bash
docker compose build
docker compose run --rm web rails db:migrate
docker compose run --rm web rails assets:precompile
```

Zuguterletzt: ein `force recreate` aller Container!
```bash
docker compose up -d --force-recreate
```

**Welcome back, konfigurationsmanufaktur.de!**

Und das war's dann auch schon 🙂

> **Edit:** Ich betreibe die Instanz nun seit fast zwei Jahren, und für mich funktioniert das so ganz gut.
> Den vorliegenden Artikel habe ich anhand der gerade veröffentlichten `v4.3.1` überarbeitet.
> Allerdings achte ich inzwischen auch penibler darauf, alte Backups, alte Docker-Container und -Volumes regelmäßig zu trashen, denn das summiert sich schon alles ganz ordentlich... 😇
