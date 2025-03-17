---
title: "Monitoring Basics"
vgwort_public: ""
vgwort_private: ""
date: 2025-02-11 10:58:45 +0200
categories:
  - "online"
tags:
  - "bestpractices"
  - "community"
  - "monitoring"
description: "Grundlegende Gedanken zum Thema Monitoring, bevor es an die konkrete Umsetzung geht"
image: "/assets/img/community/2025/02/Kaffeeblume.jpg"
hide_description: true
hide_image: false
accent_image:
  background: url('/assets/img/community/2025/02/Kaffeeblume.jpg') center/cover
  overlay: false
---

Allgemeingültige Antworten gibt es auf die meisten Fragen nicht – zu unterschiedlich sind Anforderungen, Umgebungen und Ziele – beim Monitoring wie auch bei allen anderen Themen.
Doch hier und heute werde ich mich ganz auf das Thema „Monitoring“ konzentrieren.

* Replaced by the toc
{:toc}

# Vorwort
Ohne allgemeingültige Antworten heißt: **bevor** du eine technische Lösung favorisierst – oder gar bereits mit deren konkreter Umsetzung beginnst – solltest du

* die Ziele – und das bedeutet: deine persönlichen Ziele, die deines Teams, die teamübergreifenden, die deines Arbeitgebers etc. – hinterfragen und definieren,
* die vorliegende Infrastruktur analysieren und
* die Anforderungen an das zukünftige System herausarbeiten.

Lass uns hier nun tiefer einsteigen.

# Die Ziele
Der Punkt erscheint dir möglicherweise redundant – Ziele eines Monitoring-Systems, liegt doch auf der Hand, oder?\\
Liegt es tatsächlich nicht.
Es ist unerlässlich, genau das zu hinterfragen: welche Ziele wollen **wir** mit der Etablierung eines solchen Systems eigentlich erreichen?
Denn das, was **du** mit dem Monitoring erreichen möchtest, unterscheidet sich vielleicht *doch* in einigen Punkten von dem, was dein Chef erreichen möchte.
Oder deine Kollegin.
Die anderen Teams.
Dem, was du irgendwo im Netz gelesen oder auf einer Konferenz aufgeschnappt hast.

Vielleicht ja auch nicht – dann wirst du diese Evaluierung schnell abgeschlossen haben; überspringen solltest du sie aber keinesfalls.
Und: ein Monitoring-System von größerem Umfang zu betreiben, ist grundsätzlich keine One-(Wo)M(a|e)n-Show – allein wirst du es nicht schaffen.
Du musst die Beteiligten ins Boot holen, frühzeitig – oder du wirkst sinken.

Ich empfehle deshalb *dringend*, einige Dinge schon sehr früh zu diskutieren, so zum Beispiel:

* die **Erwartungshaltung**, die die jeweiligen User/ Teams gegenüber dem Monitoring – aus technischer Sicht, aus kaufmännischer Sicht etc. – haben,
* auf welche Art **eindeutige Zuständigkeiten** sichergestellt werden können, und daraus resultierend
* eine Art übergreifendes **Selbstverständnis** in Bezug auf das Monitoring, aus dem sich verpflichtende Regeln im Umgang damit **für alle** ergeben.

> **Beispiel:** Während ein Team die Ausgaben und Benachrichtigungen des Monitorings als umgehende Arbeitsaufforderung auffasst und als Idealzustand *„das Monitoring soll grün sein“* anstrebt, begreift ein kooperierendes Team diese eher als eine Art wohlwollende „ToDo-Liste“. Es herrscht Uneinigkeit über Dringlichkeiten, sowohl die der Alarme als auch die von deren Behebung. Zuständigkeiten sind nicht in jedem Fall klar und müssen schlimmstenfalls aufwändig recherchiert werden. Das Monitoring wird nicht als unterstützend und hilfreich wahrgenommen, sondern als tägliches Ärgernis, das zusätzliche Arbeit generiert. Häufige Diskussionen und Unzufriedenheit sind dann **auf allen Seiten** vorprogrammiert – und werden irgendwann den erhofften Erfolg des Systems vereiteln.

Wenden wir uns also der Frage nach möglichen Zielen eines Monitoring-Systems zu.
Ich zeige hier einige denkbare Antworten auf – ohne Anspruch auf Vollständigkeit, ohne Entweder-Oder.
Du kannst dir greifen, was für dich passt; du musst ergänzen, was für dein System unerlässlich ist.

* grundsätzliche Überwachung von Hosts sowie deren Services
* gezielte Überwachung betriebsrelevanter und/oder geschäftskritischer Prozesse
* die Möglichkeit, (Betriebs-)Verantwortliche **schnell**, **effizient** und **detailliert** bezüglich Ausfällen oder drohender Schwierigkeiten informieren zu können
* die Möglichkeit umfangreicher und möglichst schneller **Analysen** – idealerweise ohne umständliche Kontextwechsel
* Dashboards, die idealerweise auch {neue, fachfremde, außenstehende} Personen in die Lage versetzen, ernsthafte Probleme sofort erkennen und angemessen adressieren zu können
* Erkennung von Trends anhand gesammelter Performance-Daten (beispielsweise zur Kapazitätsplanung)

# Analyse der Infrastruktur
Der jeweilige Anforderungskatalog ist so bunt, wie Infrastrukturen unterschiedlich sind.
Daher ist ein guter Startpunkt, die eigene Infrastruktur genau zu betrachten, ihr sozusagen Fragen zu stellen.
Manche dieser Fragen mögen *zum jetzigen Zeitpunkt* keine Relevanz für deine Umgebung aufweisen; versuche hier, großzügig in die Zukunft zu denken.

> **Beispiel:** Ist ein Austausch der derzeit genutzten Router-Plattform geplant? Soll angesichts der politischen Lage vielleicht vermehrt Linux der Vorzug gegeben werden, wo bisher häufig Windows im Einsatz war? Ihr arbeitet derzeit nicht mit Containern, in Zukunft soll aber so manches über Docker passieren?

* **Was** soll überwacht werden? - Hardware-Hosts, virtuelle Appliances, Netzwerkgeräte, Drucker, Sensoren, Klimaanlagen etc.
* **Welche Schnittstellen** bieten diese Endgeräte? - SNMP? API? Lässt sich darauf eventuell erforderliche Software installieren? Falls nicht: wäre das ein Dealbreaker?
* Unter welchen **Betriebssystemen** laufen die Endgeräte – aktuell vs. in `n` Jahren? Linux? Windows? *BSD? Solaris? Andere? Und ist eventuell erforderliche Software für diese Betriebssysteme unkompliziert verfügbar?
* Inwiefern spielen die Themen **Kubernetes und/ oder Container** eine Rolle – aktuell vs. in `n` Jahren?
* Inwiefern spielen die Themen **Config Management/ Automatisierung/ CMDB** eine Rolle – aktuell vs. in `n` Jahren? Gibt es beispielsweise Daten in Netbox, PuppetDB o.ä., die herangezogen werden können? Inwiefern soll ein bestehendes Ansible, Salt o.ä. eingebunden werden?
* Inwiefern spielt das Thema **SLA/ Reporting** eine Rolle (sowohl inhouse, Externen bzw. Kunden gegenüber, aktuell vs. in `n` Jahren)?

# Die Anforderungen an das zukünftige System
Wenn du an diesem Punkt angelangt bist, hast du viele Gespräche mit vielen Verantwortlichen geführt, dich eingehend mit der Infrastruktur beschäftigt, dir viele Gedanken gemacht.
Nun rückt also die eigentliche technische Umsetzung vermehrt in den Fokus.

Nachfolgend daher eine unvollständige Liste mit Fragestellungen, die für dich und dein zukünftiges System interessant sein könnten.
Mit hoher Wahrscheinlichkeit hast du längst einen Software-Favoriten, dennoch musst du für **all deine Fragen** im weiteren Verlauf ehrlich klären, welche Kriterien dein Favorit erfüllen *muss* und auch *kann*, wo du gegebenenfalls Abstriche akzeptieren kannst, und – wichtiger noch – wo nicht.
Und: ab wann du auf eine andere technische Lösung umdenken musst.
Denn an diesem Punkt der Planung ist grundsätzlich alles noch möglich.
Und je weiter eine Umsetzung bereits fortgeschritten ist, desto schmerzhafter wird das Umschwenken auf eine alternative Lösung – und auch, dieses zu verargumentieren.

Die ersten Beispiele sind eher genereller Natur:

* Soll die Monitoring-Software unter *„active development“* stehen? (Na, wenn wir ehrlich sind: **ja**, das darf eigentlich kein Diskussionspunkt sein. Ist es aber manchmal.)
* Soll das Monitoring sich gut in die bestehende Infrastruktur einpassen lassen, eventuell vorhandene Strukturen berücksichtigen und diese idealerweise integrieren? Oder beginnst du *„auf der grünen Wiese&trade;“*?
* Soll es möglichst kostenneutral sein?
* Ist dir Support durch eine Community wichtig?
* Oder würdest du, zumindest manchmal und gegebenenfalls auch kurzfristig, auch professionellen Support gegen Einwurf von Münzen favorisieren?
* Soll das Monitoring selbst ein bestimmtes Maß an Hochverfügbarkeit aufweisen, und wie lässt sich diese umsetzen und überwachen?

An dem Punkt hast du aber vermutlich auch schon konkrete Vorstellungen, auf was es bei der Umsetzung ankommen wird.

* Benötigt ihr eine *gesicherte Kommunikation* zwischen Monitoring-Server(n) und Clients? **Ja** – auch das sollte heutzutage keine Diskussion mehr wert sein. Ist es aber manchmal.
* Möchtet ihr die Möglichkeit der Einsortierung eurer Hosts und Services in *Gruppen*?
* Legt ihr Wert auf ein rollenbasiertes User-Management?
  * Möchtet ihr gar ein bestehendes LDAP/ AD o.ä. für die User-Verwaltung integrieren?
  * Ist euch die Möglichkeit der Einsortierung von Benutzern in Gruppen (um Sicht auf die Daten zu gewährleisten) beziehungsweise die Nutzung von bestehenden {LDAP, AD-}Gruppen wichtig?
  * Wollt ihr, davon unabhängig, Nutzende in Kontaktgruppen (Beispiel: zur Realisierung von Benachrichtigungen) einsortieren können?
* Wie relevant ist in deinem Umfeld die Alarmierung Verantwortlicher (Stichwort *„Alerting“*)?
  * Was sind deine favorisierten in Eskalationsstufen (E-Mail, SMS, IRC, Mattermost, Telegram, Slack, um nur einige Optionen zu nennen – wie ist die Nachtbereitschaft angebunden?), wie sieht die technische Umsetzung aus? Welche zusätzlichen Dienste sind nötig, welche müssen gegebenenfalls extern zugekauft werden?
  * Wie fein möchtest du die Renotifizierungen konfigurieren oder auch Ausnahmen definieren können?
  * Definition von **Time Periods** - zu unterschiedlichen Zeiten unterschiedliche Dinge bewerkstelligen
* Ist es dir wichtig, die Zahl der Benachrichtigungen auf das Nötigste zu beschränken?
  * Dann sind *Downtimes* beziehungsweise *Scheduled Downtimes für wiederkehrende Tasks* (Beispiel: ein nächtlich laufendes Backup, das kurzfristig, aber vorhersehbar zu einer hohen Load führt) ein wichtiges Stichwort.
  * Oder auch die Erfassung von *Abhängigkeiten* zwischen Hosts und/oder Services (Beispiel: bei Ausfall des Routers soll nicht für alle Server, die davon abhängen, je eine SMS versendet werden, sondern nur eine – aus der die vorliegende Dringlichkeit klar hervorgeht).
* Wie wichtig sind dir konfigurierbare *Dashboards*?
  * Zum einen, um die Sicht der Gruppen und Verantwortlichen auf genau ihre Zuständigkeitsbereiche zu gewährleisten und
  * zum anderen, um gegebenenfalls auch Kunden, Zuständigen an anderen Standorten, Externen etc. Zugriff zu gewährleisten?
* Welche Tasks möchtest du den Verantwortlichen selbst in die Hand geben können? Vielleicht sollen sie ja
  * zusätzliche Service-Checks für ihre Hosts umsetzen dürfen,
  * Schwellenwerte möglichst unkompliziert selbst anpassen können,
  * Acknowledgements mit Kommentar setzen können,
  * Downtimes für geplante Wartungsarbeiten setzen können,
  * gegebenenfalls auch Benachrichtigungen dediziert abschalten können?
* Darauf aufbauend möchtest du dann vielleicht auch ein gewisses Maß an *Nachvollziehbarkeit* (Beispiel *„Aktivitäts-Log“*: wer hat wann welche Änderung am System vorgenommen?) – auch und gerade, um im Fehlerfall auf einen vorherigen Versionsstand zurückspringen zu können.
* Wie wichtig ist dir die Möglichkeit, *Performance-Daten* gezielt erfassen (Graphite, InfluxDB etc.) und auswerten (Grafana etc.) zu können?