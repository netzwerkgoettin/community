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

Allgemeingültige Antworten gibt es auf die meisten Fragen nicht – zu unterschiedlich sind die Anforderungen, Umgebungen, Ziele.

* Replaced by the toc
{:toc}

Im Umkehrschluss ergibt sich daraus: **bevor** wir eine technische Lösung fokussieren (oder gar bereits mit deren Umsetzung beginnen), sollten wir

* unsere Ziele definieren,
* unsere Infrastruktur analysieren und
* unsere Anforderungen herausarbeiten.

## Die Ziele
Der Punkt erscheint euch vielleicht redundant, aber tatsächlich ist er wichtig: welche Ziele wollen wir mit der Etablierung eines Monitoring-Systems eigentlich erreichen?
Das, was **du** mit dem Monitoring erreichen möchtest, unterscheidet sich vielleicht **doch** in einigen Punkten von dem, was dein Chef erreichen möchte.
Oder deine Kollegin.
Die anderen Teams.
Vielleicht auch nicht.

Ich empfehle dringend, einige Dinge schon sehr früh zu diskutieren, nämlich:

* die **Erwartungshaltung**, die die jeweiligen User/ Teams gegenüber dem Monitoring – aus technischer Sicht, aus kaufmännischer Sicht etc. – haben,
* auf welche Art **eindeutige Zuständigkeiten** sichergestellt werden können, und daraus resultierend
* eine Art übergreifendes **Selbstverständnis** in Bezug auf das Monitoring, aus dem sich verpflichtende Regeln im Umgang damit **für alle** ergeben.

> **Beispiel:** Während ein Team die Ausgaben und Benachrichtigungen des Monitorings als umgehende Arbeitsaufforderung auffasst und als Idealzustand *„das Monitoring soll grün sein“* anstrebt, begreift ein kooperierendes Team diese eher als eine Art wohlwollende „ToDo-Liste“. Es herrscht Uneinigkeit über die Dringlichkeit der Alarme und deren Behebung. Zuständigkeiten sind nicht in jedem Fall klar und müssen schlimmstenfalls aufwändig recherchiert werden. Häufige Diskussionen und Unzufriedenheit sind hier **auf allen Seiten** vorprogrammiert.

Wenden wir uns also der Frage nach möglichen Zielen eines Monitoring-Systems zu, zeige ich euch hier einige denkbare Antworten auf – ohne Anspruch auf Vollständigkeit oder darauf, dass diese in eurer Umgebung wirklich relevant sind:

* grundsätzliche Überwachung von Hosts sowie deren Services
* gezielte Überwachung betriebsrelevanter und/oder geschäftskritischer Prozesse
* die Möglichkeit, (Betriebs-)Verantwortliche **schnell**, **effizient** und **detailliert** bezüglich Ausfällen oder drohender Schwierigkeiten informieren zu können
* die Möglichkeit umfangreicher und möglichst schneller **Analysen** – idealerweise ohne umständliche Kontextwechsel
* Dashboards, die idealerweise auch {neue, fachfremde, außenstehende} Personen in die Lage versetzen, ernsthafte Probleme sofort erkennen und angemessen adressieren zu können
* Erkennung von Trends, beispielsweise zur Kapazitätsplanung anhand gesammelter Performance-Daten

## Analyse der Infrastruktur
Der jeweilige Anforderungskatalog ist so bunt, wie Infrastrukturen unterschiedlich sein können.
Daher ist ein guter Startpunkt, die eigene Infrastruktur genau zu betrachten, ihr sozusagen Fragen zu stellen.

* **Was** soll überwacht werden? - Hardware-Hosts, virtuelle Appliances, Netzwerkgeräte, Drucker, Sensoren, Klima, etc.
* **Welche Schnittstellen** bieten diese Endgeräte? - SNMP? API? Lässt sich darauf eventuell erforderliche Software installieren? Falls nicht: wäre das ein Dealbreaker?
* Unter welchen **Betriebssystemen** laufen die Endgeräte – aktuell vs. in 3 Jahren? Linux? Windows? *BSD? Solaris? Andere? Und ist eventuell erforderliche Software für diese Betriebssysteme unkompliziert verfügbar?
* Inwiefern spielt die Themen **Kubernetes/ Container** eine Rolle – aktuell vs. in 3 Jahren?
* Inwiefern spielen die Themen **Config Management/ Automatisierung/ CMDB** eine Rolle – aktuell vs. in 3 Jahren? Gibt es beispielsweise Daten in Netbox, PuppetDB o.ä., die herangezogen werden können? Inwiefern soll ein bestehendes Ansible, Salt o.ä. eingebunden werden?
* Inwiefern spielt das Thema **SLA/ Reporting** eine Rolle (sowohl inhouse, Externen bzw. Kunden gegenüber, aktuell vs. in 3 Jahren)?

## Die Anforderungen
* eine Lösung, die sich gut in unsere bestehende Infrastruktur einpassen lässt
* eine Lösung, die unter "active development" steht
* eine Lösung, die möglichst kostenneutral sein soll
* eine Lösung, für die wir kurzfristig professionellen Support erhalten können
* eine Lösung, die uns zumindest Support durch die Community bietet


* **Gesicherte Kommunikation** zwischen Monitoring-Server(n) und Clients
* Definition von **Abhängigkeiten** zwischen Hosts/ Services  - nur das alarmieren, was zwingend erforderlich ist
* Möglichkeit der Einsortierung in Host- bzw. Service-Gruppen
* rollenbasiertes User-Management
* Möglichkeit, bestehende User zu nutzen => Integration AD/LDAP
  * Möglichkeit der Einsortierung von Benutzern in Gruppen (um Sicht auf die Daten zu gewährleisten) bzw. Nutzung von bestehenden {LDAP, AD-}Gruppen
  * Möglichkeit der Einsortierung von Benutzern in Kontaktgruppen (zur Realisierung von Benachrichtigungen)
  * Darauf aufbauend Alarmierung Verantwortlicher in Eskalationsstufen - IRC, E-Mail, SMS, Thema Nachtbereitschaft?
* **Dashboards** - Sicht der Gruppen auf genau ihre Zuständigkeitsbereiche gewährleisten
* innerhalb des Zuständigkeitsbereichs sollen die User
  * Schwellenwerte möglichst unkompliziert anpassen können
  * Acknowledgements mit Kommentar setzen können
  * Downtimes für geplante Wartungsarbeiten setzen können
  * ggf. Benachrichtigungen dediziert abschalten können
* **Dashboards** - gibt es {Kunden, Zuständige an anderen Standorten, whatever}, die ebenfalls Zugriff erhalten müssen (aktuell vs. in 3 Jahren)?
* **Nachvollziehbarkeit** - wer hat wann welche Änderung am System vorgenommen? ("Aktivitäts-Log")
* **Scheduled Downtimes** für wiederkehrende Tasks - Vermeidung überflüssiger Notifications
* Definition von **Time Periods** - zu unterschiedlichen Zeiten unterschiedliche Dinge bewerkstelligen
* wie hochverfügbar muss das Monitoring selbst sein?
* Möglichkeit, Performance Data gezielt auswerten zu können (z.B. Grafana)