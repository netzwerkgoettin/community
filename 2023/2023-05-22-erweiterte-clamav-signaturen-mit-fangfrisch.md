---
title: "Erweiterte ClamAV-Signaturen mit fangfrisch"
vgwort_public: "1d5c5c8c36194139850fd076dd4b676d"
vgwort_server: "vg03"
date: 2023-05-22 01:54 +0200
categories:
  - "online"
tags:
  - "communications"
  - "community"
description: "Virenerkennungsrate von ClamAV unter Zuhilfenahme externer Signaturen verbessern"
image: "/assets/img/community/2023/05/Blume2.jpg"
hide_description: true
hide_image: false
accent_image:
  background: url('/assets/img/community/2023/05/Blume2.jpg') center/cover
  overlay: false
---

`postfix`, `rspamd` und ClamAV – und wie ich die Virenerkennungsrate auf den Systemen unter Zuhilfenahme weiterer Signaturen spürbar verbessern kann.

* Replaced by the toc
{:toc}

Bei der Arbeit mit Mailservern und allem, was so dazu gehört, könnte man den Eindruck bekommen, dass diesbezüglich kaum noch jemand eine eigene Infrastruktur betreibt.
Was ist denn nur los?
Vor 15 Jahren war jeder (und nein, es gab keine „jede“), mit dem ich mich darüber unterhielt, irgendwie *drin* in dem Thema.
Inzwischen scheint der überwiegende Teil der Leute dieses *unübersichtliche*, *gefährliche* und *komplizierte* Thema nur zu gerne den jeweiligen Hostern zu überlassen.
Wie schade!

Das zeigt sich nämlich an vielen Ecken und Enden.
Die Dokumentationen sind oftmals erstaunlich alt, die Menge brauchbarer Blog-Artikel zum Thema überschaubar.
Und auch manche Software befindet sich im Kryoschlaf: so ist das letzte Release von [amavisd-new](https://www.amavis.org/#new) aus dem Jahr 2018.
Und ganz ehrlich: das ist ein so gigantisches Perl-Monster – es fällt mir schwer anzunehmen, dass jemand sich die Weiterentwicklung freiwillig ans Bein binden wollen wird.
Bei [spamassassin](https://spamassassin.apache.org/news.html) geht es allemal irgendwie weiter, nachdem sich da zwischenzeitlich auch für mehrere Jahre so gar nichts mehr tat.
[ClamAV](https://www.clamav.net/downloads) wird weiterentwickelt, doch mit den mitgelieferten Signaturen ist die Erkennungsrate da auch mehr so-la-la.
An der Stelle werden dann die nicht offiziell mitgelieferten Signaturen interessant: dafür war ursprünglich das Paket [clamav-unofficial-sigs](https://metadata.ftp-master.debian.org/changelogs//main/c/clamav-unofficial-sigs/clamav-unofficial-sigs_3.7.2-2.1_changelog) zuständig, und daran wird seit 2014 praktisch nicht mehr gearbeitet.
Viele Howtos referenzieren deshalb [die gleichnamige One-Man-Show von extremeshok](https://github.com/extremeshok/clamav-unofficial-sigs) – doch auch die wurde seit über zwei Jahren nicht mehr angerührt, und die Webseite des Maintainers liefert ein abgelaufenes Zertifikat aus und ist im übrigen *„under maintenance“*.

All das führte mich zu [fangfrisch](https://rseichter.github.io/fangfrisch/), ebenfalls eine One-Man-Show, das Repository ist auf [GitHub](https://github.com/rseichter/fangfrisch) zu finden.
Und wie ich das in der Praxis so umsetze – das zeige ich euch jetzt.
Dabei versuche ich, mich auf das Wesentliche zu konzentrieren und setze einen grundsätzlich lauffähigen `postfix` mit `rspamd` weitestgehend voraus.

# fangfrisch
Wenden wir unsere ungeteilte Aufmerksamkeit erstmal `fangfrisch` zu.

## Installation
Als `root` nehme ich im ersten Schritt die Installation der Abhängigkeiten und anschließend von `fangfrisch` vor.
~~~shell
## als User root
apt install clamav python3-venv
mkdir -m 0770 /opt/python3-venv && cd $_
python3 -m venv fangfrisch
chgrp -R clamav /opt/python3-venv/fangfrisch/
chmod -R g+w fangfrisch/
~~~

Die weiteren Schritte führe ich dann als User `clamav` aus.
~~~shell
## als User clamav
su - clamav -s /bin/bash
cd /opt/python3-venv/fangfrisch/
source bin/activate
pip install fangfrisch
~~~

Damit ist die Installation abgeschlossen.
Stand heute habe ich `fangfrisch` in Version `1.6.1` auf dem System.

## Konfiguration
Auf der [Dokumentationsseite](https://rseichter.github.io/fangfrisch/) stellt der Maintainer sowohl [eine wirklich minimale Minimalkonfiguration zum Testen](https://rseichter.github.io/fangfrisch/#_configuration) bereit als auch [eine für den Betrieb geeignete](https://rseichter.github.io/fangfrisch/#sampleeff).
Ich habe letztere als Ausgangsbasis genommen und an Ort und Stelle gepackt.
~~~shell
## als User clamav
cd /opt/python3-venv/fangfrisch
mkdir config
vim config/fangfrisch.conf
~~~

Wichtige Aspekte müssen gegebenenfalls eigenen Belangen angepasst werden.
Ich wollte beispielsweise, dass die SQLite-DB innerhalb des *virtual environment* liegt und anders heißt (`db_url`).
Relevant natürlich auch `local_directory` - diese bestimmt, wo die heruntergeladenen Signatur-Dateien zum Liegen kommen.

> **Einschub:** Üblicherweise könnt ihr `/var/lib/clamav` einfach so übernehmen – das ist der Ort, an dem `clamd` per Default seine Signaturen sucht. In manchen Fällen kann eine Änderung des Pfades allerdings sinnvoll erscheinen. Betreibt ihr beispielsweise mehrere Mailserver, so empfehle ich, die Signaturen einmal zentral einzusammeln und dann lokal weiterzuverteilen. Der zentral einsammelnde `fangfrisch` könnte die Dateien dann direkt in ein Verzeichnis packen, das für die [`freshclam`](#freshclam)-Instanzen aller Hosts erreichbar ist.

~~~shell
## file: "config/fangfrisch.conf"
db_url = sqlite:////opt/python3-venv/fangfrisch/fangfrisch.sqlite
local_directory = /var/lib/clamav
...
~~~

In diesem Beispiel jedoch landen die Patterns direkt in `/var/lib/clamav`.
Und wie ihr [weiter unten](#clamd) sehen werdet, prüft `clamd` den Ordner regelmäßig auf geänderte Dateien hin – und lädt diese dann automatisch.

## Initialisierung und erster Lauf
Jetzt einmalig `fangfrisch.sqlite` erstellen.
~~~shell
## als User clamav
cd /opt/python3-venv/fangfrisch
bin/fangfrisch --conf config/fangfrisch.conf initdb 
~~~

Den ersten Lauf schubse ich dann manuell an, um eventuelle Fehler direkt sehen zu können.
~~~shell
## als User clamav
bin/fangfrisch --conf config/fangfrisch.conf refresh 
~~~

In Abhängigkeit von der Anbindung kann das eine Weile dauern.
Die Dateien erscheinen konfigurationsentsprechend in `local_directory` – in diesem Fall also `/var/lib/clamav`.

## Cronjob
Natürlich will ich das nicht ständig von Hand ausführen – ein Cronjob muss her.
Ich habe mich für eine Datei `fangfrisch` in `/etc/cron.d` entschieden.
So ließe sich auch sehr einfach ein Proxy definieren, falls man auf einen angewiesen sein sollte – in meinem Setup ist das nicht der Fall.
Ich arbeite hier mit [`chronic`](/chronic/) – das muss nicht sein, aber ich mag's.
~~~shell
## file: "/etc/cron.d/fangfrisch"
HOME=/opt/python3-venv/fangfrisch
##HTTP_PROXY="proto://set_proxy_when_needed:port"
##HTTPS_PROXY="proto://set_proxy_when_needed:port"
LOG_LEVEL=INFO

45  *  *  *  *  clamav  chronic bin/fangfrisch --conf config/fangfrisch.conf refresh
~~~

## Externe Quellen
[Malwarepatrol](https://www.malwarepatrol.net/) habe ich mir angesehen, mich jedoch gegen eine Einbindung entschieden.
Deshalb kann ich an dieser Stelle nicht mehr dazu sagen, und ihr müsst wohl oder übel Google bemühen.

Bei [Securiteinfo](https://www.securiteinfo.com/services-cybersecurite/anti-spam-anti-virus/improve-detection-rate-of-zero-day-malwares-for-clamav.shtml?lg=de) hingegen habe ich mir einen „Basic“-Account geholt und binde somit auch deren Patterns ein.
Und ich hab schon den Eindruck, dass diese auch in der Basic-Version die Erkennungsrate sehr deutlich voranbringen.
Er steht den kommerziellen Lösungen, die ich zum Vergleich betrachten kann, jedenfalls erstmals in nichts nach (wie sich das aufs Jahr gesehen verhält, bleibt abzuwarten).

# ClamAV
Wenn `fangfrisch` einsatzbereit ist, wenden wir uns ClamAV zu.

## clamd
`/etc/clamav/clamd.conf` weist überwiegend die Defaults auf, ich habe einige wenige Änderungen vorgenommen.
So habe ich mit `PidFile` explizit angegeben, dass eine `clamd.pid` gewünscht ist – auf diese referenziere ich in der Konfiguration von `monit`, aber das soll hier gerade nicht weiter interessieren.
Ich lasse den Dienst auf Port 3310 (`TCPSocket`) lauschen, und das ausschließlich auf `localhost` (`TCPAddr`).
Das ist der Weg, auf dem `rspamd` mit meinem `clamd` sprechen wird.

Mit `SelfCheck` weise ich ClamAV an, in dem in Sekunden definierten Zeitintervall zu prüfen, ob sich an den Signaturen etwas geändert hat.
Es genügt somit, geänderte Signatur-Dateien in `/var/lib/clamav` abzulegen – der Rest geschieht innerhalb des definierten Intervalls automatisch.
`3600`, also einmal pro Stunde, hat sich für mich als ausreichend erwiesen.
Ob das bei euch genauso ist, müsst ihr testen.

Zu guter Letzt noch `ConcurrentDatabaseReload no`, was folgenden Hintergrund hat: ClamAV benötigt relativ viel RAM – er hält alle Patterns im RAM vor.
Da kann schon im Leerlauf 1GB draufgehen, und fängt er erst zu scannen an, wird das nicht besser.
Neue Virendefinitionen lädt er per Default **zusätzlich** im Rahmen einer **zusätzlichen** Scan-Engine; in dem Moment wird sozusagen die **doppelte** Menge an RAM fällig.
Alle Scans laufen weiter über die „alte“ Scan-Engine, bis die neue vollständig bereitsteht.
Erst **dann** wird die alte Engine aus dem RAM gekegelt – und genau hier schlägt dann der OOM-Killer zu, wenn der Host nicht über genug RAM verfügt.
[Seit Version 0.103.0](https://blog.clamav.net/2020/09/clamav-01030-released.html) existiert daher die Konfigurationsoption `ConcurrentDatabaseReload`, deren Default auf `yes` gesetzt ist.
Und auch wenn es anders hübscher wäre – ich setze sie auf `no`.
~~~shell
## file: "/etc/clamav/clamd.conf"
PidFile /var/run/clamav/clamd.pid
TCPSocket 3310
TCPAddr localhost
SelfCheck 3600
ConcurrentDatabaseReload no
...
~~~

## freshclam
Jetzt gilt es noch, `freshclam` anzuweisen, alle Quellen regelmäßig nach neuen Signaturen abzuklappern; das geschieht üblicherweise über die `/etc/clamav/freshclam.conf`.

Lädt `fangfrisch` seine Dateien direkt in `/var/lib/clamav` ab, ist hier gar nichts weiter zu tun, da [`clamd`](#clamd) diese sowieso regelmäßig prüft.
Haltet ihr die `frangfrisch`-Daten jedoch zentral vor, könnt ihr `freshclam` anweisen, sie gemeinsam mit gegebenenfalls allen anderen einzusammeln.
Die hierfür zuständige Option ist `DatabaseCustomURL`, und diese lässt sich, wenn nötig, auch mehrfach definieren.

# rspamd
Der Vollständigkeit halber stelle ich hier auch meine `/etc/rspamd/local.d/antivirus.conf` zur Verfügung.
Sie ist vergleichsweise umfassend inzwischen.
Ich musste ziemlich lange suchen und machen, ehe ich bei dieser Version angekommen bin, die auch wirklich tut, was ich von ihr will – und ja, ich lehne wirklich ab.
Lest euch da selbst ein und entscheidet euch für euren Weg.
~~~shell
## file: "/etc/rspamd/local.d/antivirus.conf"
clamav {
  type = "clamav";
  servers = "127.0.0.1:3310";
  action = "reject";
  symbol = "CLAM_VIRUS";
  message = '${SCANNER}: virus found: "${VIRUS}"';
  scan_mime_parts = true;
  scan_text_mime = true;
  scan_image_mime = true;
  log_clean = true;

  patterns {
    ## EICAR for testing purposes
    JUST_EICAR = "^Eicar-Test-Signature$";

    CLAM_DOC_MALWARE = "^Doc\.Malware\..*";
    CLAM_HTML_PHISH = "^Html\.Phishing\..*";
    CLAM_HTML_EXPLOIT = "^Html\.Exploit\..*";
    CLAM_BROKEN_EXEC = "^Broken\.Executable.*";
    CLAM_WIN_WORM = "^Win.Worm.Mydoom-*";

    ## Heuristics
    CLAM_HEUR_PHISHING =  "^Heuristics\.Phishing\.Email\.SpoofedDomain";
    CLAM_HEUR_OLE2_VBA_MACRO = "^Heuristics\.OLE2\.ContainsMacros$";
    CLAM_HEUR_ENCRYPTED = "^Heuristics\.Encrypted\..*";
    CLAM_HEUR_STRUCT_CC = "^Heuristics.Structured.CreditCardNumber$";
    CLAM_PUA_WIN = "^PUA\.Win\..*";
    CLAM_PUA_DOC = "^PUA\.Doc\..*";

    ## ERROR not virus
    CLAM_LIMITS_EXCEEDED = "^Heuristics\.Limits\.Exceeded";

    ## Extra Signatures
    CLAM_G_SAFEBROWSING = "^Heuristics.Safebrowsing.*";
    CLAM_PORCUPINE_JUNK = "^Porcupine\.Junk.*";
    CLAM_SANESEC_JURBL = "^Sanesecurity\.Jurlbl.*";
    CLAM_SANESEC_JUNK = "^Sanesecurity\.Junk.*";
    CLAM_SANESEC_BLURL = "^Sanesecurity\.Blurl.*";
    CLAM_SANESEC_SCAM = "^Sanesecurity\.Scam.*";
    CLAM_WINNOW_SPAM = "^winnow\.spam.*";
    CLAM_SECI_JS_PUA = "^PUA\.SecuriteInfo\.com\.JS\.Malware.*";
    CLAM_SECI_JS_AD = "^SecuriteInfo\.com\.JS\.AdInject.*";
    CLAM_SECI_JS_REDIR = "^SecuriteInfo\.com\.JS\.Redir.*";
    CLAM_SECI_SUSPICIOUS = "^SecuriteInfo\.com\.Suspicious.*"
    CLAM_SECI_HTML = "^SecuriteInfo\.com\.HTML.*";
    CLAM_SECI_PHIS = "^SecuriteInfo\.com\.Phish.*";
    CLAM_SECI_JPG = "^SecuriteInfo\.com\.JPG.*";
    CLAM_MP_EVILMACRO = "^MiscreantPunch.EvilMacro\..*";
    CLAM_YARA = "^YARA\..*\.UNOFFICIAL$";
  }

  patterns_fail {
    CLAM_PROTOCOL_ERROR = '^unhandled response';
  }
  
  # whitelist points to a map of IP addresses. Mail from these addresses is not scanned.
  whitelist = "/etc/rspamd/antivirus.wl";
}
~~~

# Heise E-Mail-Check
Nun muss man das Ganze natürlich auch gut testen – es sollen ja keine Dinge verloren gehen.
Natürlich schnappt man sich dazu [EICAR](https://www.eicar.org/download-anti-malware-testfile/) und schickt es sich selbst.
~~~shell
wget -qO - https://secure.eicar.org/eicar.com | gzip -c9 > eicar.gz
swaks \
--to to@example.com \
--from  from@example.com \
--attach - --server localhost < eicar.gz
~~~

Aber eine Verbindung von `localhost` ist nun einmal eine Verbindung von `localhost`.
Und ich will doch eigentlich vor allem sicherstellen, dass Viren **von außen** sauber gehandhabt werden.

Sich selbst Viren zu schicken, beispielsweise über das Gmail-Konto, könnt ihr vergessen: Gmail (und vermutlich viele andere Hoster) checken sowohl in- als auch ausgehend auf Viren – und verweigern entsprechend den Versand.

An dem Punkt erweist sich dann der [E-Mail-Check von Heise](https://www.heise.de/security/dienste/Mails-mit-Viren-Dummies-777839.html) als ziemlich praktisch: gewünschtes Virus auswählen (beispielsweise den Standard [EICAR](https://www.heise.de/security/dienste/emailcheck/virendummies/eicar/)), E-Mail-Adresse angeben, bestätigen und schon marschiert EICAR in die Inbox...

... oder eben auch nicht 🎉

~~~shell
## file: "/var/log/mail.log"
<timestamp> core postfix/submission/cleanup[161122]: 1BBB9602BE: message-id=<E1pwgnI-00DQGx-NA.octo03@web.heise.de>
<timestamp> core postfix/submission/cleanup[161122]: 1BBB9602BE: milter-reject: END-OF-MESSAGE from web.heise.de[2a02:2e0:3fe:1001::beef]: 5.7.1 clamav: virus found: "Win.Test.EICAR_HDB-1"; fr
om=<emailcheck-robot@ct.de> to=<to@example.com> proto=ESMTP helo=<web.heise.de>
~~~

~~~shell
## file: "/var/log/clamav/clamav.log"
<timestamp> -> instream(127.0.0.1@56918): Win.Test.EICAR_HDB-1(44d88612fea8a8f36de82e1278abb02f:68) FOUND
~~~

~~~shell
## file: "/var/log/rspamd/rspamd.log"
<timestamp> #2161(rspamd_proxy) <adfb84>; milter; rspamd_milter_process_command: got connection from [2a02:2e0:3fe:1001::beef]:40878
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_message_parse: loaded message; id: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>; queue-id: <1BBB9602BE>; size: 2061; checksum: <be3242c8ff90cdcad4cc1004a224d530>
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_mime_text_part_utf8_convert: converted text part from ISO-8859-1 ('ISO-8859-1' announced) to UTF-8 inlen: 812, outlen: 816 (812 UTF16 chars)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_mime_part_detect_language: detected part language: de
<timestamp> #2161(rspamd_proxy) <adfb84>; lua; spf.lua:160: use cached record for ct.de (0x210fb245cc27a354) in LRU cache for 260 seconds
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; lua_task_insert_result_common: symbol insertion issue: unknown symbol SENDER_REP_HAM; trace: [1]:{/usr/share/rspamd/plugins/reputation.lua:94 - add_symbol_score [Lua]}; [2]:{/usr/share/rspamd/plugins/reputation.lua:472 - ipstats_check [Lua]}; [3]:{/usr/share/rspamd/plugins/reputation.lua:501 - continuation_cb [Lua]}; [4]:{/usr/share/rspamd/plugins/reputation.lua:998 - callback [Lua]}; [5]:{/usr/share/rspamd/lualib/lua_redis.lua:1305 - callback [Lua]}; [6]:{/usr/share/rspamd/lualib/lua_redis.lua:924 - <unknown> [Lua]};
<timestamp> #2161(rspamd_proxy) <adfb84>; lua; common.lua:110: clamav: result - virusfound: "Win.Test.EICAR_HDB-1 - score: 1"
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_add_passthrough_result: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>: set pre-result to 'reject' (no score): 'clamav: virus found: "Win.Test.EICAR_HDB-1"' from clamav(1)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_task_write_log: id: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>, qid: <1BBB9602BE>, ip: 2a02:2e0:3fe:1001::beef, from: <emailcheck-robot@ct.de>, (default: T (reject): [7.31/12.00] [CLAM_VIRUS(4.00){Win.Test.EICAR_HDB-1;},MIME_BAD_EXTENSION(4.00){com;},DMARC_POLICY_ALLOW(-0.50){ct.de;none;},R_SPF_ALLOW(-0.20){+ip6:2a02:2e0:3fe:1001::/64:c;},MIME_GOOD(-0.10){multipart/mixed;text/plain;},ONCE_RECEIVED(0.10){},XM_UA_NO_VERSION(0.01){},ARC_NA(0.00){},ASN(0.00){asn:12306, ipnet:2a02:2e0:3fe::/48, country:DE;},FROM_EQ_ENVFROM(0.00){},FROM_HAS_DN(0.00){},HAS_ATTACHMENT(0.00){},MIME_TRACE(0.00){0:+;1:+;2:-;2:~;},RCPT_COUNT_ONE(0.00){1;},RCVD_COUNT_ONE(0.00){1;},RCVD_TLS_LAST(0.00){},R_DKIM_NA(0.00){},SENDER_REP_HAM(0.00){asn: 12306(0.00), country: DE(-0.00), ip: 2a02:2e0:3fe:1001::(0.00);},TO_DN_NONE(0.00){},TO_MATCH_ENVRCPT_ALL(0.00){}]), len: 2061, time: 43.627ms, dns req: 7, digest: <be3242c8ff90cdcad4cc1004a224d530>, rcpts: <to@example.com>, mime_rcpts: <to@example.com>, forced: reject "clamav: virus found: "Win.Test.EICAR_HDB-1""; score=nan (set by clamav)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_protocol_http_reply: regexp statistics: 0 pcre regexps scanned, 3 regexps matched, 175 regexps total, 85 regexps cached, 0B scanned using pcre, 3.21KiB scanned total
<timestamp> #2161(rspamd_proxy) <283471>; proxy; proxy_milter_finish_handler: finished milter connection
~~~

# Fazit – und ein Appell
Ich betreibe „schon immer[tm]“ meinen eigenen Mail-Server.
Früher war's eben ein `sendmail` – und **jaha! Ich mochte ihn!**
Jetzt ist es eben ein `postfix`.
Ganz früher war es gar nix, früher war es dann `spamassassin`, jetzt ist es `rspamd`.
Früher war es `Cyrus IMAP`, jetzt halt `dovecot`.
Werkzeuge ändern sich, die Aufgabenstellung im Kern allerdings über all die Jahre hinweg nicht.

Ich sehe es kritisch, dass immer weniger Leute sich damit befassen.
Das ist Wissen, das verloren geht, nicht weitergetragen werden kann... das ist ein Risiko.
Gleichzeitig behaupten Bewerber gerne, Ahnung vom Mailserver-Betrieb und gesteigertes Interesse an Datenschutzthemen zu haben – und senden ihren Kram dann über zum Beispiel ein Gmail-Konto ein?
Das weckt denn doch unmittelbar mein Interesse.

Es lohnt sich, tiefer in die Materie einzusteigen; mehr damit zu machen, als es nur zu benutzen – es ist ein spannendes Thema.
(Und allen Unkenrufen zum Trotz: Mail ist nicht tot.
Wird es so schnell auch nicht sein.)
Wie schade, dass es fast schon angstbesetzt ist.

Immer wieder höre ich als Argument, dass bei Konfigurationsfehlern Dinge verloren gehen könnten, die Erreichbarkeit nicht mehr gewährleistet wäre, etc. pp.
Und klar: betreibt man Doom-Copy-Pasting nach bester Stackoverflow-Manier, hat kein Monitoring, keine beziehungsweise ungetestete Backups und liest und/oder versteht keine Logfiles – dann passieren `$DINGE`.
Das ist bei anderer Software aber nicht anders.
Bei E-Mails tut es aber anders weh – und in erster Linie einem selbst 😎

Es hilft (insgesamt und in allen Arbeitsbereichen, übrigens), das als Herausforderung anzunehmen – und sich eine Arbeitsweise anzueignen, die es ermöglicht, „angstfrei zu töpfern“ 😇
Ihr könnt mir nicht erzählen, dass ihr nicht jährlich für mindestens eine Domain bezahlt, die total cool, aber völlig ungenutzt ist?
Das ist eine 1A-Chance, sie einer Verwendung zuzuführen!
Nur Mut 💚
