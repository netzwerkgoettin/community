---
title: "Erweiterte ClamAV-Signaturen mit fangfrisch"
vgwort_public: ""
vgwort_private: ""
date: 2021-11-24 08:49 +0200
categories:
  - "offline"
tags:
  - "nachdenkliches"
description: ""
image: "/assets/img/posts/"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/posts/') center/cover
  overlay: false
---

`postfix`, `rspamd` und ClamAV - und wie ich die Virenerkennungsrate auf den Systemen unter Zuhilfenahme weiterer Signaturen spürbar verbessern kann.

* Replaced by the toc
{:toc}

Bei der Arbeit mit Mailservern und allem, was so dazu gehört, könnte man den Eindruck bekommen, dass kaum noch jemand eigene Mailserver betreibt.
Was ist da nur los?
Vor 15 Jahren war jeder (und nein, es gab keine „jede“), mit dem ich mich darüber unterhielt, irgendwie *drin* in dem Thema.
Inzwischen scheint der überwiegende Teil der Leute dieses *unübersichtliche*, *gefährliche* und *komplizierte* Thema nur zu gerne ihren Hostern zu überlassen.
Wie schade!

Das zeigt sich nämlich an vielen Ecken und Enden.
Die Dokumentationen sind teilweise erstaunlich alt, die Menge brauchbarer Blog-Artikel zum Thema überschaubar.
So ist das letzte Release von [amavisd-new](https://www.amavis.org/#new) aus dem Jahr 2018.
Und ganz ehrlich: das ist ein gigantisches Perl-Monster - es fällt mir schwer anzunehmen, dass jemand sich die Weiterentwicklung freiwillig ans Bein binden wollen würde.
Bei [spamassassin](https://spamassassin.apache.org/news.html) geht es allemal irgendwie weiter, zwischenzeitlich hatte ich da auch eher die Vermutung *„das war's jetzt“*.
[ClamAV](https://www.clamav.net/downloads) wird weiterentwickelt, doch mit den mitgelieferten Signaturen ist die Erkennungsrate da auch mehr so-la-la.
An der Stelle werden dann die nicht offiziell mitgelieferten Signaturen interessant: dafür war ursprünglich das Paket [clamav-unofficial-sigs](https://metadata.ftp-master.debian.org/changelogs//main/c/clamav-unofficial-sigs/clamav-unofficial-sigs_3.7.2-2.1_changelog) zuständig, und daran wird seit 2014 praktisch nicht mehr gearbeitet.
Viele Howtos referenzieren deshalb [die gleichnamige One-Man-Show von extremeshok](https://github.com/extremeshok/clamav-unofficial-sigs) – doch auch dieses wurde seit über zwei Jahren nicht mehr angerührt, und die Webseite des Maintainers liefert ein abgelaufenes Zertifikat aus und ist im übrigen *„under maintenance“*.

All das führte mich zu [fangfrisch](https://rseichter.github.io/fangfrisch/), ebenfalls eine One-Man-Show, das Repository ist auf [GitHub](https://github.com/rseichter/fangfrisch) zu finden.
Und wie ich das in der Praxis so umsetze – das zeige ich euch jetzt.
Dabei versuche ich, mich auf das Wesentliche zu konzentrieren und setze einen grundsätzlich lauffähigen `postfix` mit `rspamd` weitestgehend voraus.

# fangfrisch
Wenden wir unsere ungeteilte Aufmerksamkeit erstmal `fangfrisch` zu.

## Installation
Als `root` nehme ich im ersten Schritt die Installation der Abhängigkeiten und anschließend von `fangfrisch` vor.
~~~bash
## als User root
apt install clamav python3-venv
mkdir -m 0770 /opt/python3-venv && cd $_
python3 -m venv fangfrisch
chgrp -R clamav /opt/python3-venv/fangfrisch/
chmod -R g+w fangfrisch/
~~~

Die weiteren Schritte führe ich dann als User `clamav` aus.
~~~bash
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
~~~bash
## als User clamav
cd /opt/python3-venv/fangfrisch
mkdir config
vim config/fangfrisch.conf
~~~

Wichtige Aspekte müssen gegebenenfalls eigenen Belangen angepasst werden.
Ich wollte Beispielsweise, dass die SQLite-DB innerhalb des *virtual environment* liegt und anders heißt (`db_url`).
Relevant natürlich auch `local_directory` - diese bestimmt, wo die heruntergeladenen Signatur-Dateien zum Liegen kommen.

> **Einschub:** Üblicherweise kann man `/var/lib/clamav` einfach so übernehmen – das ist der Ort, an dem `clamd` per Default seine Signaturen sucht. Betreibt ihr allerdings mehrere Mailserver ist anzuraten, die Signaturen einmal zentral einzusammeln und dann lokal weiterzuverteilen; in dem Fall müsste der Pfad gegebenenfalls angepasst werden. Mehr dazu weiter unten im Abschnitt [`freshclam`](#freshclam).

~~~text
db_url = sqlite:////opt/python3-venv/fangfrisch/fangfrisch.sqlite
local_directory = /var/lib/clamav
~~~

In diesem Beispiel jedoch landen die Patterns direkt in `/var/lib/clamav`.
`freshclam`, der in meinem Setup stündlich läuft, weist `clamd`, den Daemon,  dann zum Einlesen geänderter Patterns an.

[Malwarepatrol](https://www.malwarepatrol.net/) habe ich mir angesehen, mich jedoch gegen eine Einbindung entschieden.
Deshalb kann ich an dieser Stelle nicht mehr dazu sagen, und ihr müsst wohl oder übel Google bemühen.
Bei [Securiteinfo](https://www.securiteinfo.com/services-cybersecurite/anti-spam-anti-virus/improve-detection-rate-of-zero-day-malwares-for-clamav.shtml?lg=de) hingegen habe ich mir einen „Basic“-Account geholt und binde somit auch deren Patterns ein.

## Initialisierung und erster Lauf
Jetzt nur noch `fangfrisch.sqlite` bereitstellen.
~~~bash
## als User clamav
cd /opt/python3-venv/fangfrisch
bin/fangfrisch --conf config/fangfrisch.conf initdb 
~~~

Den ersten Lauf schubse ich dann manuell an, um eventuelle Fehler direkt sehen zu können.
~~~bash
## als User clamav
bin/fangfrisch --conf config/fangfrisch.conf refresh 
~~~

In Abhängigkeit von der Anbindung kann das eine Weile dauern.
Die Dateien erscheinen konfigurationsentsprechend in `/var/lib/clamav`.

## Cronjob
Natürlich will ich das nicht ständig von Hand ausführen – ein Cronjob muss her.
Ich habe mich für eine Datei `fangfrisch` in `/etc/cron.d` entschieden.
So ließe sich auch sehr einfach ein Proxy definieren, falls man auf einen angewiesen sein sollte – in meinem Setup ist das nicht der Fall.
Ich arbeite hier mit [`chronic`](/chronic/) – das muss nicht sein, aber ich mag's.
~~~text
## file: /etc/cron.d/fangfrisch
HOME=/opt/python3-venv/fangfrisch
##HTTP_PROXY="proto://set_proxy_when_needed:port"
##HTTPS_PROXY="proto://set_proxy_when_needed:port"
LOG_LEVEL=INFO

45  *  *  *  *  clamav  chronic bin/fangfrisch --conf config/fangfrisch.conf refresh
~~~

# clamd
`/etc/clamav/clamd.conf` weist überwiegend die Defaults auf, ich habe einige wenige Änderungen vorgenommen.
So habe ich mit `PidFile` explizit angegeben, dass eine `clamd.pid` gewünscht ist – auf diese referenziere ich in der Konfiguration von `monit`, aber das soll hier gerade nicht weiter interessieren.
Ich lasse den Dienst auf Port 3310 (`TCPSocket`) lauschen, und das ausschließlich auf `localhost` (`TCPAddr`).
Das ist der Weg, auf dem `rspamd` mit meinem `clamd` sprechen wird.

Zu guter Letzt noch `ConcurrentDatabaseReload no`, was folgenden Hintergrund hat: ClamAV benötigt relativ viel RAM – er hält alle Patterns im RAM vor.
Da kann schon im Leerlauf 1GB draufgehen, und fängt er erst zu scannen an wird das nicht besser.
Neue Virendefinitionen lädt er per Default **zusätzlich** im Rahmen einer zusätzlichen Scan-Engine; in dem Moment wird sozusagen die **doppelte** Menge an RAM fällig.
Alle Scans laufen weiter über die „alte“ Scan-Engine, bis die neue vollständig bereit steht.
Erst dann wird die alte Engine aus dem RAM gekegelt – und genau hier schlägt dann der OOM-Killer zu, wenn der Host nicht über genug RAM verfügt.
[Seit Version 0.103.0](https://blog.clamav.net/2020/09/clamav-01030-released.html) existiert daher die Konfigurationsoption `ConcurrentDatabaseReload`, deren Default auf `yes` gesetzt ist.
Und auch wenn es anders hübscher wäre – ich setze sie auf `no`.
~~~text
PidFile /var/run/clamav/clamd.pid
TCPSocket 3310
TCPAddr localhost
ConcurrentDatabaseReload no
...
~~~

# rspamd
Der Vollständigkeit halber stelle ich hier auch meine `/etc/rspamd/local.d/antivirus.conf` zur Verfügung.
Sie ist vergleichsweise umfassend inzwischen.
Ich musste ziemlich lange suchen und machen, ehe ich bei dieser Version angekommen bin, die auch wirklich macht, was ich von ihr will – und ja, ich lehne wirklich ab.
Lest euch da selbst ein und entscheidet euch für euren Weg.
~~~text
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
~~~bash
swaks \
--to marianne@spiller.me \
--from  root@spiller.me \
--attach - --server localhost < eicar.com.gz
~~~

Aber eine Verbindung von `localost` ist nun einmal eine Verbindung von `localhost`.
Und ich will doch eigentlich vor allem sicherstellen, dass Viren **von außen** sauber gehandhabt werden.

Sich selbst Viren zu schicken, beispielsweise über das Gmail-Konto, könnt ihr vergessen: Gmail (und vermutlich viele andere Hoster) prüfen auch beim Versand, ob ein Virus enthalten ist, und verbieten den Versand.

An dem Punkt erweist sich dann der [E-Mail-Check von Heise](https://www.heise.de/security/dienste/Mails-mit-Viren-Dummies-777839.html) als ziemlich praktisch: gewünschtes Virus auswählen (beispielsweise den Standard [EICAR](https://www.heise.de/security/dienste/emailcheck/virendummies/eicar/)), E-Mail-Adresse angeben, bestätigen und schon marschiert EICAR in die Inbox...

... oder eben auch nicht 🎉

~~~text
## file: /var/log/mail.log
<timestamp> core postfix/submission/cleanup[161122]: 1BBB9602BE: message-id=<E1pwgnI-00DQGx-NA.octo03@web.heise.de>
<timestamp> core postfix/submission/cleanup[161122]: 1BBB9602BE: milter-reject: END-OF-MESSAGE from web.heise.de[2a02:2e0:3fe:1001::beef]: 5.7.1 clamav: virus found: "Win.Test.EICAR_HDB-1"; fr
om=<emailcheck-robot@ct.de> to=<marianne@spiller.me> proto=ESMTP helo=<web.heise.de>
~~~

~~~
## file: /var/log/clamav/clamav.log
<timestamp> -> instream(127.0.0.1@56918): Win.Test.EICAR_HDB-1(44d88612fea8a8f36de82e1278abb02f:68) FOUND
~~~

~~~
## file: /var/log/rspamd/rspamd.log
<timestamp> #2161(rspamd_proxy) <adfb84>; milter; rspamd_milter_process_command: got connection from [2a02:2e0:3fe:1001::beef]:40878
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_message_parse: loaded message; id: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>; queue-id: <1BBB9602BE>; size: 2061; checksum: <be3242c8ff90cdcad4cc1004a224d530>
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_mime_text_part_utf8_convert: converted text part from ISO-8859-1 ('ISO-8859-1' announced) to UTF-8 inlen: 812, outlen: 816 (812 UTF16 chars)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_mime_part_detect_language: detected part language: de
<timestamp> #2161(rspamd_proxy) <adfb84>; lua; spf.lua:160: use cached record for ct.de (0x210fb245cc27a354) in LRU cache for 260 seconds
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; lua_task_insert_result_common: symbol insertion issue: unknown symbol SENDER_REP_HAM; trace: [1]:{/usr/share/rspamd/plugins/reputation.lua:94 - add_symbol_score [Lua]}; [2]:{/usr/share/rspamd/plugins/reputation.lua:472 - ipstats_check [Lua]}; [3]:{/usr/share/rspamd/plugins/reputation.lua:501 - continuation_cb [Lua]}; [4]:{/usr/share/rspamd/plugins/reputation.lua:998 - callback [Lua]}; [5]:{/usr/share/rspamd/lualib/lua_redis.lua:1305 - callback [Lua]}; [6]:{/usr/share/rspamd/lualib/lua_redis.lua:924 - <unknown> [Lua]};
<timestamp> #2161(rspamd_proxy) <adfb84>; lua; common.lua:110: clamav: result - virusfound: "Win.Test.EICAR_HDB-1 - score: 1"
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_add_passthrough_result: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>: set pre-result to 'reject' (no score): 'clamav: virus found: "Win.Test.EICAR_HDB-1"' from clamav(1)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_task_write_log: id: <E1pwgnI-00DQGx-NA.octo03@web.heise.de>, qid: <1BBB9602BE>, ip: 2a02:2e0:3fe:1001::beef, from: <emailcheck-robot@ct.de>, (default: T (reject): [7.31/12.00] [CLAM_VIRUS(4.00){Win.Test.EICAR_HDB-1;},MIME_BAD_EXTENSION(4.00){com;},DMARC_POLICY_ALLOW(-0.50){ct.de;none;},R_SPF_ALLOW(-0.20){+ip6:2a02:2e0:3fe:1001::/64:c;},MIME_GOOD(-0.10){multipart/mixed;text/plain;},ONCE_RECEIVED(0.10){},XM_UA_NO_VERSION(0.01){},ARC_NA(0.00){},ASN(0.00){asn:12306, ipnet:2a02:2e0:3fe::/48, country:DE;},FROM_EQ_ENVFROM(0.00){},FROM_HAS_DN(0.00){},HAS_ATTACHMENT(0.00){},MIME_TRACE(0.00){0:+;1:+;2:-;2:~;},RCPT_COUNT_ONE(0.00){1;},RCVD_COUNT_ONE(0.00){1;},RCVD_TLS_LAST(0.00){},R_DKIM_NA(0.00){},SENDER_REP_HAM(0.00){asn: 12306(0.00), country: DE(-0.00), ip: 2a02:2e0:3fe:1001::(0.00);},TO_DN_NONE(0.00){},TO_MATCH_ENVRCPT_ALL(0.00){}]), len: 2061, time: 43.627ms, dns req: 7, digest: <be3242c8ff90cdcad4cc1004a224d530>, rcpts: <marianne@spiller.me>, mime_rcpts: <marianne@spiller.me>, forced: reject "clamav: virus found: "Win.Test.EICAR_HDB-1""; score=nan (set by clamav)
<timestamp> #2161(rspamd_proxy) <adfb84>; proxy; rspamd_protocol_http_reply: regexp statistics: 0 pcre regexps scanned, 3 regexps matched, 175 regexps total, 85 regexps cached, 0B scanned using pcre, 3.21KiB scanned total
<timestamp> #2161(rspamd_proxy) <283471>; proxy; proxy_milter_finish_handler: finished milter connection
~~~

# Fazit
Ich betreibe „schon immer[tm]“ meinen eigenen Mail-Server.
Früher war's eben ein `sendmail` (**Jaha! Und ich mochte ihn!**), jetzt ist es ein `postfix`.
Und doch muss ich gestehen: ich hab mich ein bisschen zu sehr auf meinem Status Quo ausgeruht.

Doch es lohnt sich, wieder tiefer in die Materie einzusteigen, wieder mehr damit zu machen als es nur zu benutzen – Mail ist ein spannendes Thema.
Und es ist schade, dass es fast schon angstbesetzt ist.