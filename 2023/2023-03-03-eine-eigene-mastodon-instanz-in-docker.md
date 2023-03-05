---
title: "Eine eigene Mastodon-Instanz in Docker"
vgwort_public: "7763248beb7646e8beb607e7d18f7e16"
date: 2023-03-03 10:00 +0200
categories:
  - "online"
tags:
  - "communications"
  - "community"
description: "Über das Betreiben einer eigenen Mastodon-Instanz"
image: "/assets/img/community/2023/03/Mastodon_Memory_Usage.jpg"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2023/03/Mastodon_Memory_Usage.jpg') center/cover
  overlay: false
---

Im November 2022 habe ich, [wie bereits am Rande erwähnt](/sozial-unsozial-alles-egal/), mit `konfigurationsmanufaktur.de` meine eigene Mastodon-Instanz aufgezogen.

* Replaced by the toc
{:toc}

Und ich hatte versprochen: wenn ich ein paar Erfahrungen damit gesammelt habe, dann schreibe ich hier mal ein bisschen was dazu.
Nun, ein Vierteljahr ist seither ins Land gezogen, zwei Upgrades habe ich der Instanz derweil verpasst – ein guter Moment, um euch zumindest eine grobe Idee davon zu geben, wie das Ganze hier läuft.

## Ein paar Gedanken vorweg
Ihr braucht eine Domain, und zwar eine möglichst hübsche.[^1]
Ich persönlich wollte keine mit `.social`, einfach weil ich die dämlich finde.
Stattdessen ist es ein buchstabentechnisch quantitatives Monster geworden, das zu lang ist für jedwede gefällige Darstellung im Webinterface.

![Die extralange Domain hat unerwartete Seiteneffekte](/assets/img/community/2023/03/Zu_lang.jpg){:.lead width="100%" loading="lazy"}

Die extralange Domain hat unerwartete Seiteneffekte
{:.figcaption}


Im Moment sind [ich](https://konfigurationsmanufaktur.de/@netzwerkgoettin), [tabsvongesternnacht](https://konfigurationsmanufaktur.de/@tabsvongesternnacht) und [handmade](https://konfigurationsmanufaktur.de/@handmade) die einzigen User auf der Instanz.
Sprich: viele Themen (wie zum Beispiel „Moderation“) stellen für mich schlicht kein Problem dar, da ich allein auf der Instanz herumtobe und entsprechend viel Narrenfreiheit genieße.
Außerdem muss ich mich keinen gespenstischen Spielregeln unterwerfen – beziehungsweise nur denen, die ich mir selbst auferlege.
Das hat Vorteile 😎

Die Instanz will E-Mails versenden.
Ich nutze hierfür derzeit meinen Postfix auf `unixe.eu`, erwäge jedoch, das auf längere Sicht zu entkoppeln.
Mini-Instanzen können vermutlich relativ schmerzfrei über ein bestehendes Gmail-Konto gehen.
Aber größere kommen an der Stelle „um was Richtiges“ meiner Meinung nach nicht herum.

Erforderliche Metriken erfasse ich mit [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/), speichere sie in [InfluxDB](https://www.influxdata.com/) und stelle sie mit [Grafana](https://grafana.com/) dar.
Ziemlich langweilig, aber zuverlässig.

## Der Server
Der Server `klubhaus` verfügt über 32GB RAM und 10 AMD EPYC 7702P Cores.
Doch keine Sorge: für das alleinige Betreiben einer Mastodon-Mini-Instanz wäre das absolut überdimensioniert!
Mein `klubhaus` macht Mastodon mehr so nebenher (Stichwort *„proof of concept“*) und braucht die recht üppigen Kapazitäten an anderer Stelle.
Mit einem kleineren VPS seid ihr für den Anfang erstmal gut gerüstet.
Und auf längere Sicht kommt es dann halt darauf an, was ihr mit der Instanz vorhabt. 

Mein Server läuft unter Debian 11 und hat die jeweils aktuelle Docker-Version sowie die paketierte Version des `docker-compose-plugin` am Start.
(Fast) jeden Montag spiele ich hart alle verfügbaren Upgrades ein und starte die Kiste anschließend durch.
Für den Dauerbetrieb muss sie das abkönnen.
Und das kann sie auch.

## Installation und Inbetriebnahme
Meine Installationsnotizen beziehen sich auf [v4.0.2](https://github.com/mastodon/mastodon/releases/tag/v4.0.2) – das war die im November gültige `stable`.
Soweit ich das überblicken kann, sollte sich seither nicht allzu viel geändert haben.
Zuständig für den Dienst ist mein lokaler User namens `aeffchen`, und in dessen `$HOME` lade ich alles Benötigte ab.

~~~bash
cd /home/aeffchen
mkdir fediverse && cd $_
git clone https://github.com/tootsuite/mastodon
cd mastodon/
cp .env.production.sample .env.production
vim .env.production
~~~

Hier musste ich alle Kleinigkeiten eintragen, die halt relevant sind: als `LOCAL_DOMAIN`, ganz klar, `konfigurationsmanufaktur.de`; wo er Redis und Postgres finden wird; auf welche Art er E-Mails versenden soll.
Und eben auch die `docker-compose.yml` generell nochmal durchsehen – ich zum Beispiel wollte andere Pfade für die Volumes haben.

Dann können die Container schonmal zusammengebaut werden:
~~~bash
docker compose build
~~~

Nun sind in `.env.production` noch zwei Schlüssel zu ergänzen: `SECRET_KEY_BASE` und `OTP_SECRET`.
Beide erzeugt man, in dem man 2x das folgende Kommando ausführt:
~~~bash
docker compose run --rm web bundle exec rake secret
~~~

Abschließend fehlen in `.env.production` nun noch `VAPID_PRIVATE_KEY` sowie `VAPID_PUBLIC_KEY`: auch für deren Erzeugung gibt es ein schmissiges Kommando:
~~~bash
docker compose run --rm web bundle exec rake mastodon:webpush:generate_vapid_key
~~~

Die resultierenden Zeilen in die `.env.production` einfügen – et voilà.
Nun ist es an der Zeit, die eben erfassten Konfigurationen in die Images zu übernehmen und das Ganze final zusammenzubauen. Hierzu dienen die folgenden Aufrufe:
~~~bash
docker compose build
docker compose run --rm web rails assets:precompile
docker compose run --rm web rails db:migrate
~~~

Damit sind alle Vorarbeiten beendet.
Der finale Schritt besteht darin, alle Container zu starten:
~~~bash
docker compose up -d
~~~

Mit `docker ps` lässt sich der Gesamtstatus bewundern:
~~~bash
aeffchen@klubhaus:~/fediverse/mastodon$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED        STATUS                  PORTS                                NAMES
dcb5fa9ac692   tootsuite/mastodon      "/usr/bin/tini -- no…"   28 hours ago   Up 28 hours (healthy)   3000/tcp, 127.0.0.1:4000->4000/tcp   mastodon-streaming-1
7818cd7c9c2c   tootsuite/mastodon      "/usr/bin/tini -- ba…"   28 hours ago   Up 28 hours (healthy)   127.0.0.1:3000->3000/tcp, 4000/tcp   mastodon-web-1
20f5e5c81029   tootsuite/mastodon      "/usr/bin/tini -- bu…"   28 hours ago   Up 28 hours (healthy)   3000/tcp, 4000/tcp                   mastodon-sidekiq-1
818f32e4718b   postgres:14-alpine      "docker-entrypoint.s…"   28 hours ago   Up 28 hours (healthy)                                        mastodon-db-1
409c2ebbb275   redis:7-alpine          "docker-entrypoint.s…"   28 hours ago   Up 28 hours (healthy)                                        mastodon-redis-1
~~~

## nginx
Mit [`dist/nginx.conf`](https://github.com/mastodon/mastodon/blob/main/dist/nginx.conf) steht bereits eine solide Basis für die Webserver-Konfiguration bereit.
Ich habe sie an einigen Stellen noch ein wenig frisiert – mein `nginx` läuft nicht als Container, sondern ist als Paket installiert, und das muss Berücksichtigung finden.
Beispielsweise bei der Einstellung von `proxy_pass`, die für Streaming nicht nach `http://streaming` geht, sondern nach `http://127.0.0.1:4000`.
Die für `@proxy` geht analog dazu nicht nach `http://backend`, sondern nach `http://127.0.0.1:3000`; solche Dinge eben...
Sowohl IPv4 als auch IPv6, harte Weiterleitung von HTTP auf HTTPS und ein [Let's Encrypt](https://letsencrypt.org/de/)-Zertifikat – mehr braucht dann eigentlich nicht mehr, um glücklich zu werden.

## Administrativen User erzeugen
~~~bash
docker exec -it mastodon-streaming-1 /bin/bash \
RAILS_ENV=production bin/tootctl accounts create handmade \
--email handmade@tabsvongesternnacht.de \
--confirmed \
--role Owner
~~~

Funktioniert alles, erhält der neu erzeugte Account auf der Konsole direkt sein Initialpasswort.
~~~text
OK
New password: XnPXz4lKUXvvsPUqQ2yfM5QJDyfhc0ZJ
~~~

Der so erzeugte Account hat die Rolle *„Owner“*.
Mit dem genannten Passwort kann er sich am Webinterface einloggen und damit beginnen, die Instanz mit grundsätzlichem Leben zu füllen.
Beispielsweise, indem er Datenschutzhinweise und Server-Spielregeln hinterlegt, ein Instanz-Bildchen hochlädt, ...
Und, **wichtig**, unter *„Einstellungen &rarr; Administration &rarr; Serverregeln“* festlegt, ob neue Konten angelegt werden dürfen!
(An dieser Stelle habe ich *„Niemand kann sich registrieren“* ausgewählt.)
Insgesamt gilt es nun, sich mit der Administration und dem Interface vertraut zu machen.

Die Installation von Mastodon ist das kleinste Übel – **vor allem mit steigender Nutzerzahl fällt der Moderation eine deutlich relevantere Bedeutung zu**.

## Noch ein paar Worte zu Postgres
Bei der initialen Inbetriebnahme hatte ich mehrere Probleme mit Postgres.
In erster Linie liegt das sicherlich daran, dass ich praktisch nie damit gearbeitet habe – egal wo ich hinkam, die Präferenz lag eigentlich immer in MySQL bzw. später dann MariaDB.

Um das Ganze ans Laufen zu bringen, habe ich in Postgres erst einmal ein Passwort gesetzt und dieses dann als `environment` in `docker-compose.yml` hinterlegt.
Wichtig im Zusammenhang mit Mastodon und der vorliegenden `docker-compose.yml` ist beispielsweise auch `DB_HOST`, denn voreingestellt ist `/var/run/postgresql`.
Laut `docker-compose.yml` heißt der Service jedoch `db` – ich habe daher `DB_HOST=db` in `.env.production` eingetragen.
Ähnliches gilt für Redis – hier also genau hinschauen und alles dem eigenen Setup entsprechend anpassen.
Dem Postgres-Container kann man über `docker-compose.yml` auch ein noch weiter angepasstes `environment` mitgeben, falls das nötig und sinnvoll erscheint.
Das könnte dann folgendermaßen aussehen:

~~~yaml
version: '3'
services:
  db:
    restart: always
    image: postgres:14-alpine
    shm_size: 256mb
    networks:
      - internal_network
    healthcheck:
      test: ['CMD', 'pg_isready', '-U', 'postgres']
    volumes:
      - ./postgres14:/var/lib/postgresql/data
    environment:
      - 'POSTGRES_HOST_AUTH_METHOD=trust'
      - 'POSTGRES_PASSWORD=jFCd96rmLHlySz0D6PJNeyqgmboy9FX9'
      - 'POSTGRES_DB=postgres'
~~~

## Fehlermeldung: paperclip und permission denied
~~~text
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] [paperclip] Trying to link /tmp/61df4299c37319f86beec01f67d7263a20221120-11-vx36f6 to /tmp/5649906f6cc54f10de5cf1d36f7f265320221120-11-g0mmjv
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] method=PUT path=/settings/profile format=html controller=Settings::ProfilesController action=update status=500 error='Errno::EACCES: Permission denied @ dir_s_mkdir - /opt/mastodon/public/system/accounts' duration=95.64 view=0.00 db=4.55
[853b8ea9-8176-4a97-9e9e-16b0023e85d4]
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] Errno::EACCES (Permission denied @ dir_s_mkdir - /opt/mastodon/public/system/accounts):
[853b8ea9-8176-4a97-9e9e-16b0023e85d4]
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] lib/paperclip/attachment_extensions.rb:87:in `block in save'
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] lib/paperclip/attachment_extensions.rb:93:in `save'
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] app/services/update_account_service.rb:8:in `call'
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] app/controllers/settings/profiles_controller.rb:11:in `update'
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] app/controllers/concerns/localized.rb:11:in `set_locale'
[853b8ea9-8176-4a97-9e9e-16b0023e85d4] lib/mastodon/rack_middleware.rb:9:in `call'
[1ba6b08e-faf8-4fbd-b1dd-d5d131ac9695] method=HEAD path=/health format=*/* controller=HealthController action=show status=200 duration=0.41 view=0.20 db=0.00
~~~

Das lässt sich durch Korrektur der Berechtigungen beheben.
~~~bash
docker compose run --user=root --rm web chown -R mastodon /opt/mastodon/public/
~~~

## Fazit
Die Installation ist im Grunde genommen mehr Fleißarbeit als sonstwas.
Es sind die vielen Kleinigkeiten links und rechts, die das Ganze aufblasen und zeitintensiv werden lassen (können): die automatische Erneuerung des Zertifikats, beispielsweise, die Backups, das Monitoring.
Vieles davon erledige ich inzwischen über [Ansible](https://www.ansible.com/)-Playbooks, aber auch die wollen erstellt und gepflegt werden – was den Aufwand im Grunde genommen nur um eine Ebene verlagert 😎

Das Setup läuft stabil, nur selten erhalte ich in [Metatext](https://github.com/metabolist/metatext)[^2] überraschende *„too many requests“* Meldungen – welche dann aber auch direkt wieder verschwinden. Die RAM-Auslastung des Servers liegt konstant bei etwa 11%, wohingegen die CPUs sich eher langweilen.

Dem Update-Prozess widme ich dann demnächst noch einen eigenen kleinen Artikel.
Und vielleicht habe ich euch ja jetzt Lust drauf gemacht, es selbst mal zu versuchen – wir ihr seht, es ist keine Raketenwissenschaft.

[^1]: *„Wichtig: Rumspielen nicht mit einer wertvollen Domain. Geht das Rumspielen schief, ist die Domain im Fediverse verbrannt.“* – Dank an [Klampfradler](https://konfigurationsmanufaktur.de/@Natureshadow@floss.social)
[^2]: Das ich nach wie vor verwende – ich muss mich dringend um eine Alternative kümmern, da die Weiterentwicklung eingestellt ist!
