---
title: "Monitoring Basics"
vgwort_public: "4fa7200d51fc4ae88cedcb2565f3fff3"
date: 2025-04-02 11:17:45 +0200
categories:
  - "online"
tags:
  - "bestpractices"
  - "community"
  - "monitoring"
description: "Grundlegende Gedanken zum Thema Monitoring, bevor es an die konkrete Umsetzung geht"
image: "/assets/img/community/2025/04/Kaffeeblume.jpg"
hide_description: true
hide_image: false
accent_image:
  background: url('/assets/img/community/2025/04/Kaffeeblume.jpg') center/cover
  overlay: false
---

Ein System-Monitoring ist, vor allem während der Einrichtung, viel Arbeit.
Und ganz generell gesprochen: Es ist teuer.
Deshalb ist das auch eine der Stellen, an denen Betriebe sehr gerne sparen.
Mit fatalen Folgen: die reichen von technischen Ausfällen über unvorhergesehene Kosten bis hin zu Unzufriedenheit und steigendem Stress-Level auf Seiten der Mitarbeitenden.

**Ein *ordentliches* Monitoring hingegen versetzt Verantwortliche in die Lage, zu *agieren* statt lediglich zu *reagieren*.**
Es gibt ihnen die Chance, sich aus dem Modus des „Feuerlöschens“ herauszubewegen, es nimmt sich anbahnenden Problemen den Überraschungsfaktor, es macht Vorgänge transparenter – das ist unbezahlbar.

Und Grund genug, sich eingehend damit zu beschäftigen.

* Replaced by the toc
{:toc}

# Vorwort
Was genau bedeutet eigentlich „Monitoring“?
Die [Wikipedia](https://de.wikipedia.org/wiki/Monitoring) liefert eine Definition:

> Monitoring ist die Überwachung von Vorgängen. Es ist ein Überbegriff für alle Arten von systematischen Erfassungen (Protokollierungen), Messungen oder Beobachtungen eines Vorgangs oder Prozesses mittels technischer Hilfsmittel oder anderer Beobachtungssysteme.
> Eine Funktion des Monitorings besteht darin, bei einem beobachteten Ablauf oder Prozess festzustellen, ob dieser den gewünschten Verlauf nimmt und bestimmte Schwellwerte[^1] eingehalten werden, um andernfalls steuernd eingreifen zu können. Monitoring ist deshalb ein Sondertyp des Protokollierens.

[^1]: Falsch übernommen aus der Wikipedia, auch wenn mir das körperlich weh tut; es heißt *Schwellenwert*, das sagt auch [der Duden](https://www.duden.de/rechtschreibung/Schwellenwert). Und Punkt.

Es gibt keine allgemeingültigen Antworten auf Fragen wie *„Monitoring, wie mache ich das?“* oder *„Welche Monitoring-Lösung soll ich nutzen?“*, zu unterschiedlich sind Anforderungen, Umgebungen und Ziele.
Das bedeutet, dass du dir die passenden Antworten für deine Umgebung *erarbeiten* musst.
Sprich: *Bevor* du eine technische Lösung favorisierst – oder gar bereits mit deren konkreter Umsetzung beginnst – solltest du

* die Ziele, die mit der Etablierung eines solchen Systems verfolgt werden, hinterfragen und definieren,
* die vorliegende Infrastruktur inklusive eventueller Blocker analysieren und
* die Anforderungen an das zukünftige System herausarbeiten.

# Ziele
Der Punkt erscheint dir möglicherweise redundant...
„Ziele eines Monitoring-Systems“, die liegen doch auf der Hand, oder?\\
Liegen sie aber tatsächlich nicht, und es lohnt sich, sie genau zu hinterfragen.
Also: Welche Ziele wollen *wir* mit der Etablierung eines solchen Systems eigentlich erreichen?
Denn *wir*, das bist nicht nur *du*; und das, was *du* mit dem Monitoring erreichen möchtest, unterscheidet sich sehr wahrscheinlich in einigen Punkten von dem, was dein Chef sich vorstellt.
Oder deine Kollegin.
Die anderen Teams.
Von dem, was du irgendwo im Netz gelesen oder auf einer Konferenz aufgeschnappt hast.
Vielleicht ja auch nicht – dann wirst du diese Evaluierung schnell abgeschlossen haben; überspringen solltest du sie aber keinesfalls.

> **Beispiel:** Für die Menschen, die die Server betreiben, sind zum Beispiel CPU-Load, Speicher- und Festplattenauslastung wichtige Informationen, die sie für ihre tägliche Arbeit benötigen: *„Auf dem Server läuft die Log-Partition voll, und ein anderer benötigt dringend mehr RAM.“*\\
Der Vorstand hingegen bevorzugt eine „Business Process“-Sicht auf die Dinge: *„Zwar ist einer von drei Datenbank-Servern gerade vollständig aus, aber die anderen beiden laufen und stellen alle benötigten Daten bereit, also ist der Geschäftsprozess als solcher weiterhin in Ordnung. (Und warum der eine Server umgefallen ist, ist uns im Grunde auch völlig egal.)“*\\
Und Marketing interessiert sich für Fragestellungen wie *„Wie viele User brechen, nach Tageszeiten gestaffelt, prozentual gesehen den Bestellvorgang ab?“*.

Ich empfehle deshalb *dringend*, einige Dinge schon sehr früh zu diskutieren, so zum Beispiel:

* die **Erwartungshaltung**, die die jeweiligen User/Teams gegenüber dem Monitoring – aus technischer Sicht, aus kaufmännischer Sicht etc. – haben,
* auf welche Art **eindeutige Zuständigkeiten** sichergestellt werden können, und daraus resultierend
* eine Art übergreifendes **Selbstverständnis** in Bezug auf das Monitoring, aus dem sich verpflichtende Regeln im Umgang damit *für alle* ergeben.

> **Beispiel:** Während ein Team die Ausgaben und Benachrichtigungen des Monitorings als umgehende Arbeitsaufforderung auffasst und als Idealzustand *„das Monitoring soll grün sein“* anstrebt, begreift ein kooperierendes Team diese eher als eine Art wohlwollende „ToDo-Liste“.
Es herrscht Uneinigkeit über Dringlichkeiten, vor allem in Bezug auf Alarme und deren Behebung.
(Ein nicht seltenes Zitat in diesem Zusammenhang: *„Ach **der** Server? Der ist immer rot, das ist OK...“*.)
Zuständigkeiten sind nicht in jedem Fall klar und müssen schlimmstenfalls aufwändig recherchiert werden.\\
Das Monitoring wird nicht als unterstützend und hilfreich wahrgenommen, sondern als tägliches Ärgernis, das zusätzliche Arbeit generiert.
Häufige Diskussionen und Unzufriedenheit sind dann *auf allen Seiten* vorprogrammiert – und werden irgendwann den erhofften Erfolg des Systems torpedieren.

Wenden wir uns also der Frage nach möglichen Zielen eines Monitoring-Systems zu.
Ich zeige hier einige denkbare Antworten auf, ohne Anspruch auf Vollständigkeit, ohne Entweder-Oder.
Du kannst dir greifen, was für dein System passt; du musst ergänzen, was für eure Umgebung unerlässlich ist.

* grundsätzliche Überwachung von Hosts sowie deren Services
* gezielte Überwachung betriebsrelevanter und/oder geschäftskritischer Prozesse
* die Möglichkeit, (Betriebs-)Verantwortliche *schnell*, *effizient* und *detailliert* bezüglich Ausfällen oder drohender Schwierigkeiten informieren zu können
* die Möglichkeit umfangreicher und möglichst schneller *Analysen* – idealerweise ohne umständliche Kontextwechsel
* Dashboards, die idealerweise auch {neue, fachfremde, außenstehende} Personen in die Lage versetzen, ernsthafte Probleme sofort erkennen und angemessen adressieren zu können
* Erkennung von Trends anhand dauerhaft gespeicherter Metriken, beispielsweise zur Kapazitätsplanung

# Analyse der Infrastruktur
Der jeweilige Anforderungskatalog ist so bunt, wie Infrastrukturen unterschiedlich sind.
Daher ist ein guter Startpunkt, die eigene Infrastruktur genau zu betrachten, ihr sozusagen Fragen zu stellen.
Die Antwort auf so manche Frage könnte zum Dealbreaker werden – halte fest, um welche es dabei geht.
Und manche der Fragen mögen *zum jetzigen Zeitpunkt* keine Relevanz für eure Umgebung aufweisen; versuche jedoch, an dieser Stelle großzügig in die Zukunft zu denken.

> **Beispiel:** Ist ein Austausch der derzeit genutzten Router-Plattform geplant?
Soll angesichts der politischen Lage vielleicht vermehrt Linux der Vorzug gegeben werden, wo bisher häufig Windows im Einsatz war?
Ihr arbeitet derzeit nicht mit Containern, in Zukunft soll aber so manches über Docker passieren?

* Was soll überwacht werden? Beispiele hierfür sind Hardware-Hosts, Appliances, Netzwerkgeräte, Drucker, Sensoren, Klimaanlagen,...
* Musst du Geräte an externen Standorten einbinden? Wie stabil ist deren Anbindung, und kannst du an allen Stellen die benötigten Zugriffsrechte erhalten?
* Wie soll das Monitoring von diesen Systemen erfahren? Möchtest du sie manuell erfassen? Existiert so etwas wie eine [Configuration Management Database (CMDB)](https://de.wikipedia.org/wiki/Configuration_Management_Database) oder ein DataCenter Information Management (DCIM) System (z.B. NetBox), das als Quelle dienen kann? Wie ließe sich dieses anbinden?
* Unter welchen Betriebssystemen laufen die Endgeräte? Ist eventuell erforderliche Software für diese Betriebssysteme unkompliziert verfügbar?
* Welche Schnittstellen bieten die zu überwachenden Endgeräte (SNMP, API oder Ähnliches)?
* Was willst du *wie* und *möglichst aussagekräftig* überwachen? Welche Checks musst du hierzu ausführen?

> **Beispiel:** Ein Ping-Check zur Netzwerküberwachung, der alle 5 Minuten läuft, hat eine hohe Chance, überwiegend im Status „grün“ zu bleiben, weil er Losses auf einer nur temporär instabilen Leitung eher zufällig mitbekommen wird.
Außerdem sagt ein Ping-Check allein grundsätzlich nichts über die Funktionsfähigkeit eines Endgeräts aus.
So wie ein Check auf „Anzahl der Prozesse mit Namen x“ nicht besagt, dass einer dieser Prozesse noch etwas Sinnvolles tut.
Wo also musst du ansetzen, um nicht *irgend etwas* zu überwachen, sondern *genau das*, was du für einen störungsfreien Betrieb wissen musst?

* Wie oft sollen Checks durchgeführt werden? Hier musst du abwägen zwischen Genauigkeit einerseits und Belastung der Systeme durch (gegebenenfalls zu) häufige Checks andererseits.
* Inwiefern spielen die Themen Kubernetes und/oder Container eine Rolle? Der alleinige „klassische“ Monitoring-Ansatz (AKA reines Up-Down-Monitoring) ist hier üblicherweise unbrauchbar.
* Existiert ein [Configuration Management](https://de.wikipedia.org/wiki/Konfigurationsmanagement), mit dessen Hilfe sich (Teil-)Aufgaben automatisieren ließen? 
* Welche Relevanz weisen die Themen [Service Level Agreement (SLA)](https://de.wikipedia.org/wiki/Service-Level-Agreement) und [Reporting](https://de.wikipedia.org/wiki/Berichtswesen) auf (sowohl im eigenen Haus, aber auch gegenüber Externen/Kunden)?

Das Monitoring ist hierbei nicht nur dafür da, einen Ausfall als solchen zu identifizieren, sondern insbesondere auch, um automatisch den Eskalationsprozess in Gang setzen zu können, sprich: Verantwortliche über definierte Kommunikationskanäle zu informieren.
Erfolgt innerhalb festgelegter Zeiträume keine Reaktion, wird die Alarmierung entweder wiederholt, oder die nächst höhere Eskalationsstufe wird ausgelöst.

> **Beispiel:** Der Webserver, der den Web-Shop bedient, fällt aus.
Ist er zwei Minuten lang definitiv nicht erreichbar, erstellt das Monitoring per E-Mail ein Ticket für den Front-Support.
Ist der Dienst nach weiteren 15 Minuten in unverändertem Zustand, erhalten alle Teamleads des Supports jeweils eine SMS.
Passiert innerhalb weiterer 10 Minuten nichts, ist absehbar, dass die SLAs nicht eingehalten werden; nun erhält jedes Mitglied des Vorstands eine E-Mail.

Dem Eskalations-Management kommt im Alltag eine hohe Bedeutung zu.
Deshalb solltet ihr eingehend Fragen wie die folgenden diskutieren:

* Welche Eskalationsstufen müssen in eurer Umgebung strikt eingehalten und im Monitoring entsprechend abgebildet werden?
* Wie sind die Verantwortlichkeiten verteilt?
* Mit wem muss sich im Notfall abgestimmt werden, wer hat Entscheidungshoheit?
* Müssen eventuelle Mitspracherechte, beispielsweise die des Betriebsrats, der/des Datenschutzbeauftragten, der/des Informationssicherheitsbeauftragten berücksichtigt werden?
* Müssen eventuell Betriebsvereinbarungen geschlossen oder angepasst werden – beispielsweise für Rufbereitschaft, Nachtarbeit, Aufbewahrungsfristen und Ähnliches?
* Welche sind eure Kommunikationskanäle?
  * Gängige Beispiele hierfür wären E-Mail, SMS, Ticket-System, IRC, Mattermost, Telegram, Slack, Mobiltelefone der Rufbereitschaft etc.
* Wie realisierst du die Anbindung des Monitorings an diese Kanäle?
* Welche zusätzlichen Dienste sind hierzu eventuell nötig?
* Müssen gegebenenfalls externe Dienste oder Lizenzen zugekauft werden?

# Anforderungen an das zukünftige System
Wenn du an diesem Punkt angelangt bist, hast du viele Gespräche mit vielen Verantwortlichen geführt, dich eingehend mit der Infrastruktur beschäftigt, dir viele Gedanken gemacht.
Nun rückt die eigentliche technische Umsetzung vermehrt in den Fokus.

Nachfolgend daher eine unvollständige Liste mit Fragestellungen, die für dich und dein zukünftiges System interessant sein könnten.
Mit hoher Wahrscheinlichkeit hast du längst einen Software-Favoriten, dennoch musst du für *all deine Fragen* im weiteren Verlauf ehrlich klären, welche Kriterien dein Favorit erfüllen *muss* und auch *kann*, wo du gegebenenfalls Abstriche akzeptieren kannst, und – wichtiger noch – wo nicht.
Und: ab wann du auf eine andere technische Lösung umdenken musst.

Die ersten Beispiele sind eher genereller Natur:

* Gibt das Unternehmen hart vor, welches Betriebs-/Datenbanksystem für das Monitoring zwingend genutzt werden *muss*? Weist die favorisierte Software die benötigte Datenbankschnittstelle auf, und ist sie für dieses Betriebssystem unkompliziert verfügbar?
* Muss das zukünftige Monitoring-System ein bestimmtes *„Label“*, sprich: Muss es nach TÜV-Standard, ISO XY oder Ähnlichem zertifiziert sein?
* Soll die Monitoring-Software aktiv weiterentwickelt werden? (Na, wenn wir ehrlich sind: **ja**, das darf eigentlich kein Diskussionspunkt sein. Ist es aber manchmal.)
* Soll das Monitoring sich gut in die bestehende Infrastruktur einpassen lassen, eventuell vorhandene Strukturen berücksichtigen und diese idealerweise integrieren? Oder beginnst du *„auf der grünen Wiese&trade;“*?
* Soll dein neues Monitoring ein bereits existierendes ablösen? Hier gilt es dann unter anderem auch, sich mit den Gewohnheiten der Nutzenden auseinanderzusetzen; zu schauen, wo es zu Unterschieden kommen wird, welche Features gegebenenfalls wegfallen; einen Parallelbetrieb und später den Übergang vom alten zum neuen System zu schaffen, und den möglichst ohne unnötigen „Lärm“ wie doppelte Benachrichtigungen.
* Soll es möglichst kostenneutral sein, oder ist Geld an der Stelle eher kein Thema?
* Ist dir Support durch eine Community wichtig?
* Oder würdest du, zumindest manchmal und gegebenenfalls auch kurzfristig, (eventuell auch zusätzlichen) professionellen Support gegen Einwurf von Münzen favorisieren?
* Soll das Monitoring selbst ein bestimmtes Maß an Hochverfügbarkeit aufweisen, und wie lässt sich diese umsetzen und überwachen?

An dem Punkt hast du aber vermutlich auch schon konkrete Vorstellungen, auf was es bei der Umsetzung ankommen wird.

* Benötigt ihr eine *gesicherte Kommunikation* zwischen Monitoring-Server(n) und Clients? (**Ja** – auch das sollte heutzutage keine Diskussion mehr wert sein. Ist es aber manchmal.)
* Möchtet ihr die Möglichkeit der Einsortierung eurer Hosts und Services in *Gruppen* beziehungsweise eine automatische Zuordnung anhand verschiedener, idealerweise frei definierbarer Kriterien?
* Möchtet ihr Teile der Problembehebung automatisieren?

> **Beispiel:** Der Webserver fällt aus.
Ehe das Monitoring jedoch ein Ticket an den Front-Support erstellt soll es selbsttätig versuchen, den Webserver neu zu starten.
Erst, wenn auch das missglückt, soll es den üblichen Eskalationsprozess in Gang setzen.

* Legt ihr Wert auf ein rollenbasiertes User-Management?
  * Möchtet ihr ein bestehendes LDAP/AD oder Ähnliches für die User-Verwaltung integrieren?
  * Ist euch die Möglichkeit der Einsortierung von Benutzern in Gruppen (um Sicht auf die Daten zu gewährleisten) beziehungsweise die Nutzung von bestehenden LDAP- oder AD-Gruppen wichtig?
  * Möchtest du davon unabhängig Nutzende in Kontaktgruppen einsortieren können?

* Ist es dir wichtig, die Zahl der Benachrichtigungen auf das Nötigste zu beschränken?
  * Dann sind [Downtimes](https://de.wikipedia.org/wiki/Downtime) beziehungsweise *Scheduled/Planned Downtimes für wiederkehrende Tasks* (Beispiel: ein nächtlich laufendes Backup, das kurzfristig, aber vorhersehbar zu einer hohen Load führt) ein wichtiges Stichwort.
  * Oder auch die Erfassung von *Abhängigkeiten* zwischen Hosts und/oder Services (Beispiel: bei Ausfall des Routers soll nicht für alle Server, die davon abhängen, je eine SMS versendet werden, sondern nur eine – aus der die vorliegende Dringlichkeit klar hervorgeht).

* Welche Tasks möchtest du den Verantwortlichen selbst in die Hand geben können? Vielleicht sollen sie ja
  * zusätzliche Service-Checks für ihre Hosts umsetzen dürfen,
  * Schwellenwerte möglichst unkompliziert selbst anpassen können,
  * Acknowledgements mit Kommentar setzen können,
  * Downtimes für geplante Wartungsarbeiten setzen können,
  * gegebenenfalls auch Benachrichtigungen dediziert abschalten können?

* Darauf aufbauend möchtest du dann vielleicht auch ein gewisses Maß an *Nachvollziehbarkeit* – zum Beispiel in Form eines *„Aktivitäts-Logs“*, um im Fehlerfall auf einen vorherigen Versionsstand zurückspringen zu können?

* Wie wichtig ist dir die Möglichkeit, Metriken gezielt erfassen und auswerten zu können?
  * Welche Werkzeuge kommen für Erfassung und Auswertung in Frage?
  * Können auch diese gemäß deines Redundanz-Konzepts betrieben werden?
  * Sind sie kostenfrei verfügbar?

* Wie wichtig sind dir konfigurierbare [Dashboards](https://de.wikipedia.org/wiki/Dashboard_(Informationsmanagement))?
  * Zum einen, um die Sicht der Gruppen und Verantwortlichen auf genau ihre Zuständigkeitsbereiche zu gewährleisten und
  * zum anderen, um gegebenenfalls auch Kunden, Zuständigen an anderen Standorten, Externen etc. Zugriff zu bieten?

* Was möchtest du den Nutzenden an die Hand geben, um sie bei der Behebung von Fehlerzuständen zu unterstützen?
  * Denkbar ist hier beispielsweise spezifische Dokumentation, auf die schon aus dem Monitoring-Dashboard verlinkt wird.

Sollte sich die von dir favorisierte Lösung als nicht passend für deine Umgebung herausstellen, ist es wichtig, darauf angemessen zu reagieren.
Das könnte zum Beispiel bedeuten, eine andere Software für das Monitoring zu nutzen.
Oder nicht alle Systeme mit der gleichen Software zu überwachen, sondern für bestimmte Endgeräte einer anderen Lösung den Vorzug zu geben.
An diesem Punkt der Planung ist grundsätzlich *alles noch möglich*.

Das gilt tatsächlich auch dann, wenn du in der Vergangenheit schon gute Erfahrungen mit einer bestimmten Lösung hattest sammeln können.
Doch wenn sie die Anforderungen des aktuellen (beziehungsweise zukünftigen) Szenarios nicht abdeckt, machen auch gute Erfahrungen eine unpassende Lösung nicht passender.
Und je weiter eine konkrete Umsetzung bereits fortgeschritten ist, desto schmerzhafter wird das Umschwenken auf eine alternative Lösung – und auch, dieses zu verargumentieren.

# Nachwort
Es ist leicht, ein Monitoring-System als rein technische Herausforderung zu sehen – ein Konglomerat von replizierten Datenbanken, Skripten, Hardware, Software.
Der „soziale“ Aspekt wird gerne unterschätzt – die Abstimmung mit anderen Abteilungen, Menschen, das Ermitteln des Ist-Zustands, der gewünschten Ziele und gangbarer Wege dorthin; und schlußendlich die gemeinsame Arbeit an einem System, das nie fertig sein wird.

Denn auch das ist ein nicht zu unterschätzender Punkt: das Monitoring und (seine, im Grunde genommen aber jegliche) Dokumentation müssen sich stets weiterentwickeln, mit dem Unternehmen wachsen, sich an neue Gegebenheiten anpassen.
Tun sie das nicht, mutiert beides eher früher als später zu einem Frustfaktor.

Dabei ist es wünschenswert, die Arbeit zeitnah auf mehrere Schultern zu verteilen, zum Beispiel Anwendende frühzeitig angemessen mit Kompetenzen auszustatten.
Ein solches System zu etablieren und zu betreiben ist grundsätzlich keine One-Admin-Show.
Und falls das aus den bisherigen Ausführungen nicht deutlich genug wurde: du musst die Beteiligten möglichst frühzeitig mit ins Boot holen.

Sonst hat dein Boot beim ersten Windstoß schon ein Problem.