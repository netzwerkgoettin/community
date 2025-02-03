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
description: ""
image: "/assets/img/community/2025/02/Kaffeeblume.jpg"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2025/02/Kaffeeblume.jpg') center/cover
  overlay: false
---

Allgemeingültige Antworten gibt es auf die meisten Fragen nicht – zu unterschiedlich sind die Anforderungen, Umgebungen, Ziele.

* Replaced by the toc
{:toc}

Im Umkehrschluss ergibt sich daraus: **bevor** wir eine technische Lösung fokussieren (oder gar bereits mit deren Umsetzung beginnen), sollten wir

* unsere Anforderungen herausarbeiten,
* unsere Ziele definieren und
* unsere Umgebung analysieren.

## Ziele des Monitorings
Der Punkt erscheint euch vielleicht redundant, aber tatsächlich ist er wichtig: welche Ziele wollen wir mit der Etablierung eines Monitoring-Systems eigentlich erreichen?
Denn das, was du mit dem Monitoring erreichen möchtest, entscheidet sich vielleicht doch in einigen Punkten von dem, was dein Chef erreichen möchte.
Oder deine Kollegin.
Die anderen Teams.

Ich empfehle daher dringend, einige Dinge schon sehr früh zu diskutieren, nämlich:

* die **Erwartungshaltung**, die die jeweiligen User/ Teams gegenüber dem Monitoring – aus technischer Sicht, aus kaufmännischer etc. – haben,
* auf welche Art **eindeutige Zuständigkeiten** sichergestellt werden können und daraus resultierend
* eine Art firmenweites **Selbstverständnis** in Bezug auf das Monitoring, aus dem sich verpflichtende Regeln im Umgang damit **für alle** ergeben.

> **Ein Beispiel:** Während ein Team die Ausgaben und Benachrichtigungen des Monitorings als umgehende Arbeitsaufforderung auffasst und den Idealzustand *„das Monitoring soll grün sein“* anstrebt, begreift das andere Team diese eher als eine Art wohlwollende „ToDo-Liste“. Es herrscht Uneinigkeit über die Dringlichkeit der Alarme und deren Behebung. Zuständigkeiten sind nicht in jedem Fall klar und müssen schlimmstenfalls aufwändig recherchiert werden. Häufige Diskussionen und Unzufriedenheit sind hier **auf beiden Seiten** vorprogrammiert.

Wenden wir uns also der Frage nach Zielen des Monitorings zu, zeige ich euch hier einige mögliche Antworten auf – ohne Anspruch auf Vollständigkeit oder darauf, dass das in eurer Umgebung wirklich relevant ist!

* grundsätzliche Überwachung von Hosts sowie deren Services
* gezielte Überwachung betriebsrelevanter und/oder geschäftskritischer Prozesse
* die Möglichkeit, (Betriebs-)Verantwortliche **schnell**, **effizient** und **detailliert** bezüglich Ausfällen oder drohender Schwierigkeiten informieren zu können
* die Möglichkeit umfangreicher und möglichst schneller **Analysen** – idealerweise ohne umständliche Kontextwechsel
* Dashboards, die idealerweise auch {neue, fachfremde, außenstehende} Personen in die Lage versetzen, ernsthafte Probleme sofort erkennen und angemessen adressieren zu können
* Erkennung von Trend, beispielsweise zur Kapazitätsplanung anhand gesammelter Performance-Daten
* eine Lösung, die unter "active development" steht und zumindest Support durch die Community bietet
* eine Lösung, die mit dem vorliegenden Netzwerk-Konzept kompatibel ist

## Technische Fragen
* **Was** soll überwacht werden? - Hardware-Hosts, virtuelle Appliances, Netzwerkgeräte, Drucker, Sensoren, Klima, etc.
* **Welche Schnittstellen** bieten diese Endgeräte? - SNMP? API? Oder lässt sich ggf. erforderliche Software darauf installieren?
* Unter welchen **Betriebssystemen** laufen die Endgeräte? - Linux? Windows? *BSD? Solaris? Andere? Aktuell vs. in 3 Jahren
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
* inwiefern spielt das Thema Automatisierung/ CMDB eine Rolle (aktuell vs. in 3 Jahren)?
* inwiefern spielt das Thema Kubernetes/ Cloud/ Container eine Rolle (aktuell vs. in 3 Jahren)?
* inwiefern spielt das Thema SLA/ Reporting eine Rolle (sowohl inhouse, Kunden gegenüber, aktuell vs. in 3 Jahren)?
* Möglichkeit, Performance Data gezielt auswerten zu können (z.B. Grafana)