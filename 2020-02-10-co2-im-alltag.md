---
title: "CO2 im Alltag"
vgwort_public: "0833d7d27ade4f8fb87b175688e32926"
date: 2020-02-10 14:27:13 +0200
categories: 
  - "online"
tags:
  - "community"
  - "metrics"
  - "smarthome"
excerpt: >
  „Kein Fitzelchen Sauerstoff in diesem Raum!“ - Warum diese Aussage so oft nicht stimmt; warum Stoßlüften wichtig, aber nicht alles ist; warum schlechte Luft uns schlapp macht; und wir man die Luftqualität selbst messen kann
description: >
  Ein sogar für meine Verhältnisse umfangreicher Artikel zum Thema CO<sub>2</sub>
image: "/assets/img/community/2020/02/Grafana_Heatmap.png"
hide_description: true
hide_image: true
accent_image:
  background: url('/assets/img/community/2020/02/Grafana_Heatmap.png') center/cover
  overlay: false
---

* Replaced by the toc
{:toc}

Dieser Artikel ist groß.
Und zwar nicht nur ein bisschen groß, sondern ziemlich **riesig**.
Möglich war und ist das nur durch eure Unterstützung – deshalb meinen ausdrücklichen Dank an dieser Stelle, vor allem an [Wolfgang Stief](https://about.me/stiefkind), der mir außerdem seine Notizen zur Verfügung stellte! 💚
{:.note title="Hinweis"}

## Zur Geschichte
Es war im August 2018, als ich mir den [Home Coach von Netatmo](https://www.netatmo.com/de-de/aircare/homecoach) kaufte; im Schlafzimmer sollte er stehen, und ich nutzte ihn in erster Linie für die Erfassung von Temperaturwerten sowie Lärmpegel.
Die Messung des CO<sub>2</sub> (Kohlen­stoff­dioxid) lief nebenher mit, blieb aber weitestgehend unbeachtet.
Das änderte sich, als ich das Gerät im Schlafgemach des kleinen Kindes aufstellte.

> _In genutzten Innenräumen hängt die CO<sub>2</sub>\-Konzentration im Wesentlichen von folgenden Faktoren ab: (1) Anzahl der Personen im Innenraum, (2) Raumvolumen, (3) Aktivität der Innenraumnutzer, (4) Zeitdauer, die Raumnutzer im Innenraum verbringen, (5) Verbrennungsvorgänge im Innenraum, (6) Luftwechsel bzw. Außenluftvolumenstrom[^1]_

Hierbei handelte es sich alsdann um (1) eine einzelne fünf Jahre alte Person mit (3) sehr geringer Aktivität, da schlafend; der (2) Raum hat etwa 3x5m Grundfläche und ist gute 3m hoch; die (4) Zeitdauer ist natürlich höher, das Kind geht gegen 20:00 zu Bett und schläft dort bis gegen 6:00 in der Früh; Verbrennungsvorgänge (5) finden in diesem Raum nicht statt, und Fenster und Türen (6) sind aufgrund des Lärmpegels üblicherweise geschlossen. Wenn ich dann zu Bett gehe, öffne ich die Tür dieses Raumes (im Winter) oder kippe das Fenster auf (im Sommer).

![Exemplarischer CO2-Verlauf](/assets/img/community/2020/02/Exemplarischer_CO2_Verlauf.png){:.lead width="800" height="100" loading="lazy"}
Der CO<sub>2</sub>-Verlauf einiger Tage im März 2019
{:.figcaption}

Was an diesem Graphen deutlich wird – **außer**, dass ich offenbar sehr spät erst zu Bett gehe – ist, dass die Fünfjährige es mühelos schafft, den Wert im Schlaf von ~500ppm auf knapp über 1500ppm herauf zu atmen.
Innerhalb von nur vier bis fünf Stunden – das erschien mir überraschend viel und es gibt auch Graphen, die dramatischer verlaufen.
Und dass sich die Lage bei Öffnen der Tür sofort entspannt.
Und so begann ich mich für das Thema zu interessieren: Welcher Wert wäre denn der anzupeilende?
Ab wann wird es ungemütlich, und was sind eigentlich die Folgen von „kritisch“?

## Was ist gut, was ist kritisch?
> _Kohlendioxid (chemische Kurzbezeichnung: CO<sub>2</sub>) ist ein farb- und geruchloses Gas. Mit einer Konzentration um 400ppm ist es ein natürlicher Bestandteil der Umgebungsluft. \[...\] Danach gelten Konzentrationen unter 1000ppm Kohlendioxid in der Raumluft als unbedenklich, Konzentrationen zwischen 1000 und 2000ppm als auffällig und Konzentrationen über 2000ppm als inakzeptabel[^1]._

Diese Schwellenwerte sind im Graphen entsprechend farblich markiert, und ganz ehrlich: 1000ppm sind schneller erreicht, als man gucken kann.
Mit diesem Wissen erschien es mir jedenfalls nicht mehr vertretbar, Fenster und Türen des gar nicht einmal so kleinen Raums des Nachts geschlossen zu halten.
Zudem hat die Kleine eine Spezialität: Sie liebt es, sich zum Schlafen ihre Kuscheldecke auf den Kopf zu legen.
Also nicht nur so ein bisschen, sondern richtig drauf und das gesamte Gesicht ist bedeckt.
Ich hab dann mal spaßeshalber die Probe gemacht und mich **mit** dem Healthy Home Coach (ja ja, lacht ruhig) **unter** meiner Bettdecke verschanzt: Es dauerte keine halbe Stunde, ihn auf knapp 4000ppm hochzujubeln (er leuchtet dann sehr rot und schickt beleidigte Meldungen aufs Smartphone).
Also, Kuscheldecke auf Kinderkopf: kann ich das verantworten?

Ich will in dem Zusammenhang kein Feuerwerks-Bashing betreiben, bei Durchsicht der Graphen ist mir aber der sehr interessante von Silvester 2019 in die Hände gefallen.
Gegen 20:00 hatte das Kind sich (ohne vorheriges Lüften) zum Schlafen hingelegt, ab 23:45 riss ich nochmal schnell das Doppelfenster im Raum auf und lüftete, solange es noch möglich war – ersichtlich durch den Abfall des Wertes auf ~676ppm.
Dann begann (bei wiederum geschlossenem Fenster) das Feuerwerk.
Und innerhalb kürzester Zeit herrschte draußen dichtester Nebel, sodass an ein Öffnen des Fensters bis zum nächsten Morgen nicht mehr zu denken war ... nur um mal zu zeigen, wie schnell so etwas gehen kann.

![CO2 an Silvester](/assets/img/community/2020/02/CO2_an_Silvester.png){:.lead width="800" height="100" loading="lazy"}
Verlauf der CO<sub>2</sub>-Entwicklung an Silvester 2019
{:.figcaption}

Okay, das findet ihr jetzt vielleicht nicht schnell, immerhin ist das ja ein Verlauf über gute acht Stunden.
Wenn wir uns vor den oben genannten Gesichtspunkten allerdings ein Großraumbüro vorstellen (viele Leute, lange Zeitdauer, regelmäßiger Streit um Luftwechsel – Stichwort „thermische Behaglichkeit“), einen Meeting-Raum (viele Leute auf sehr engem Raum), ein Klassenzimmer (dito) oder gar ein Auto (!), dann wird klar, wie unfassbar schnell hier jegliche Grenzwerte gerissen werden – und das auf regelmäßiger Basis.

## Zuviel CO<sub>2</sub>: die Folgen
In hoher Konzentration führt ein Zuviel zu Bewusstlosigkeit, noch mehr bis hin zum Tode.
Aber dann reden wir von Konzentrationen deutlich jenseits der 100.000ppm, und es muss schon einiges schiefgehen, um die im Wohnraum zu erreichen. 🙃
Da CO<sub>2</sub> schwerer ist als Luft, kann es sich in Bodennähe und in Senken ansammeln – ein Phänomen, das allerdings üblicherweise beispielsweise Höhlen, Schächten, Bunkern vorbehalten ist.
Ein eindrucksvolles Beispiel der Antike hierfür ist das „Tor zur Hölle“: Opfertiere in Bodennähe wurden erst ohnmächtig und starben dann, wohingegen die Priester (deren Köpfe sich mindestens einen Meter weiter oben befanden) verschont blieben.
Inzwischen weiß man, dass das deutlich weniger wunderträchtig war, als die Priester die Menschen glauben machten – vielmehr drang (und dringt bis heute) vulkanisches Kohlendioxid aus Rissen im Boden der Tempelhöhle und sammelte sich, vor allem am frühen Morgen, als konzentrierter, jedoch nicht wahrnehmbarer „See“[^4].

Aber auch die schnell erreichten niedrig vierstelligen Werte in unseren Schlafzimmern, Büro- und Meeting-Räumen zeigen bereits signifikante Auswirkungen.
Allem voran sinkt das generelle Wohlbefinden schon ab 2000ppm, und ab 3000ppm sind Einbußen in Sachen Konzentrationsfähigkeit, vermehrte Kopfschmerzen, Müdigkeit und/oder Schwindel keine Seltenheit mehr[^2].
Individuelle Befindlichkeiten addieren sich hinzu: mein Körper scheint deutlich zu erkennen, dass da etwas nicht gut ist und er etwas anderes brauchen würde – er signalisiert dies und mein Verstand missinterpretiert es als „Hunger“ (na toll!).
Eine ähnliche Fehlinterpretation kann vorliegen, wenn in den letzten 15 Minuten einer Unterrichtsstunde plötzlich vermehrt Schüler zur Toilette wollen.
Zu meiner Schulzeit wurde man da ganz gerne angeblafft, man sei _doch alt genug_ und die Viertelstunde könne man _ja wohl auch noch aushalten_.
Beides stimmte; oft geht es aber nicht darum, zur Toilette gehen oder sich Nahrung beschaffen zu können – unbewusst geht es in erster Linie darum, **raus** zu kommen.

## Zu wenig Sauerstoff?
Vor allem, wenn Personen aus dem Außenbereich in einen dicht besetzten Raum eintreten, ist ihr erster angewiderter Kommentar nicht eben selten „Ist ja kein Stück Sauerstoff in diesem Raum!“, gefolgt von einem entschlossenen Aufreißen des erstbesten Fensters.
Praktisch stimmt es natürlich, dass zumindest letztere Aktion die Luftqualität verbessern wird, faktisch kommt das dann aber eher durch das Entweichen des CO<sub>2</sub> zustande denn durch eine Erhöhung des Sauerstoffgehalts.
Denn die negativen Auswirkungen des CO<sub>2</sub> sind messbar – auch bei völlig konstantem und optimalem Sauerstoffgehalt der Atemluft[^1].

Aber: vor allem bei sehr niedrigen Außentemperaturen ist in solch einer Situation – wenn andere Teilnehmer den Raum nicht ohne weiteres verlassen können – wortloses Aufreißen der Fenster mit finster-vorwurfsvollem Gesichtsausdruck dennoch ätzend.
Denn einige reagieren auf abrupte Temperaturstürze oder Sitzen am offenen Fenster oder im Durchzug einfach heftiger als andere, und auch ein „Ach komm, **die** zehn Minuten!“ sind für sie vielleicht einfach zehn Minuten zu viel.
Sowas muss anders gehen.

## Jetzt habe ich dieses Wissen – was mache ich damit?
Inzwischen steht der Home Coach im Wintergarten, meinem Arbeitszimmer – von einigen Sukkulenten abgesehen pflanzenfrei.
Durch die beiden Aquarien im Raum hatte ich hier mit (zu) hoher Luftfeuchtigkeit gerechnet, aber die zeigt sich erfreulich gesund.
Doch in Sachen CO<sub>2</sub> sieht auch meine tippende Wenigkeit sich damit konfrontiert, mindestens alle zwei Stunden lüften zu müssen.
Und da mache ich direkt die nächste Beobachtung: Die Empfehlung lautet, für etwa 5 bis 10 Minuten das Fenster richtig aufzumachen („Stoßlüftung“).
In meinem Fall ist das Fenster mit knapp 2,50m Breite überdurchschnittlich groß und macht zwei Drittel der Raumbreite wett – die cyanfarbene senkrechte Linie markiert das Öffnen des Fensters.
Lassen wir jetzt mal das ganze Taupunkt- und Schimmelbildungsblabla außer acht:

- Wenn dieses Fenster 10 Minuten offen ist, reicht das gerade, um von 1550ppm auf 1255ppm zu kommen – und die gelten nach wie vor als _hygienisch auffällig_[^1].
- Nach 15 Minuten bin ich auf 837ppm.
- Und um auf 585ppm zu kommen braucht es eine halbe Stunde.
- In dieser Zeit sinkt die Raumtemperatur gerne um vier bis 5 Grad ab; ich aber habe viel mit [dem Raynaud-Syndrom](https://de.wikipedia.org/wiki/Raynaud-Syndrom) zu kämpfen und reagiere auf so einen Temperaturabfall extrem heftig, weshalb ich ihn vermeide, wo immer ich kann.
- Mache ich das Fenster nicht auf, sondern kippe es nur, so habe ich nach der gleichen Lüftungsdauer einen um 200ppm höheren Wert im Vergleich zum vollständig geöffneten Fenster; dafür verzeichne ich keinen Temperatursturz im Raum, womit wir wieder beim Thema „thermische Behaglichkeit“ wären, heize aber vermutlich sehr viel mehr (ich kann es gerade nicht verifizieren, weil die Heizung noch ein blödes Thermostat hat).

![Absinken des CO2-Wertes durch Lüften](/assets/img/community/2020/02/CO2_und_lueften.png){:.lead width="800" height="100" loading="lazy"}
Auch durch Lüften erholt sich der CO<sub>2</sub>-Wert nur langsam
{:.figcaption}

Okay, was hier keine Beachtung findet, ist der CO<sub>2</sub>-Gehalt der Außenluft: Den ermittle ich in Ermangelung eines Sensors nicht, und er ist im Winter generell höher als im Sommer.
Ich komme jedoch zu dem Schluss, dass die Sache mit dem viel gelobten Stoßlüften eben nicht erledigt ist.
Oder anders formuliert: _„Die Kipplüftung ist somit in den Sommermonaten eine durchaus geeignete Lüftungsmaßnahme und kann auch in den Wintermonaten als Ergänzung zur Stoßlüftung die Luftqualität deutlich verbessern.“[^2]_

Im Klartext heißt das für mich: Fenster auf, Fenster schräg, und zwar so oft wie möglich.
Idealerweise schaffe ich mir für das viel genutzte Arbeitszimmer noch ein Thermostat mit Temperatursturzerkennung an – das steht schon länger auf meiner Wunschliste.
Geöffnete Innentüren halte ich für unbrauchbar, denn damit verlagert sich das Problem maximal in andere Räume, bleibt jedoch „im Haus“.
Die Erzeugung von Durchzug kann das Absenken des CO<sub>2</sub>-Gehalts beschleunigen; das Zuschalten des Badlüfters ist zu ineffizient, er braucht etwa 20 Minuten für eine Absenkung um 150ppm.
Und für Meeting-Räume, Großraumbüros und Klassenzimmer braucht es eigentlich generell eine Belüftungsanlage – keine **Klima**anlage, die lediglich auf Einhaltung von Temperaturgrenzen reagiert, sondern eine **Be- und Entlüftung**, die _zusätzlich_ den CO<sub>2</sub>-Gehalt als maßgebliche Größe einbezieht.

Kurzum: dieser Artikel stellt nicht das Ende meiner Überlegungen dar, sondern vielmehr den Anfang.
Aspekte wie Temperatur und Luftfeuchtigkeit habe ich hier maximal gestreift oder anders formuliert: sie bereiten mir in meiner Umgebung kein Kopfzerbrechen, da sie sich jeweils innerhalb eines gesunden Rahmens bewegen.
Da ich so ziemlich alles töte, was grün und nicht unter Wasser ist, besitze ich quasi keine Zimmerpflanzen – würde ich den Wintergarten verwenden _as intended_, sähe die Sache sicher nochmal ganz anders aus.
Und überhaupt nicht erwähnt habe ich [flüchtige organische Verbindungen („volatile organic compounds“, kurz VOC)](https://de.wikipedia.org/wiki/Fl%C3%BCchtige_organische_Verbindungen): so geben zum Beispiel auch Lacke, Baustoffe, Teppiche und Farben (die Liste ist endlos, meine geliebte Duftlampe dürfte da auch eher als der Aggressor in Erscheinung treten) Stoffe an die Umgebungsluft ab und beeinflussen – oder beeinträchtigen – deren Qualität.
Und werden [Grenzwerte](https://www.air-q.com/grenzwerte) überschritten, wirkt sich das früher oder später auf die Gesundheit aus.

## Hardware für den Eigenbedarf
Seid ihr noch wach?
Ich hab euch ja vorgewarnt: Der Artikel ist **riesig**.
Widmen wir uns nun also dem spaßigen Teil der Sache: Hardware zur Überwachung diverser Luftqualitätsparameter.
Wer echt viel Zeit hat, kann mal [in dem Twitter-Thread herumstochern](https://twitter.com/sys_adm_ama/status/1223583663905964032?s=20), den ich vor ein paar Tagen zum Thema losgetreten hab – der ist sehr erhellend.
Und da solche tollen Informationen gerne verschwinden, wenn sie sich in Twitter-Threads verstecken, werde ich mal versuchen, die gesammelten Ideen hier zusammenzutragen.

### Lösungen diverser Hersteller
- [USB-Raumluftsensor von REHAU](https://www.rehau.com/de-de/raumluftsensor) – VOC: praktisch durch den Formfaktor und ideal zur Überwachung nicht sicherheitskritischer Umgebungen. Dabei liefert er jedoch keine Einzelwerte, sondern einen Gesamtwert; für die Steuerung einer Lüftung ist das ein guter Anfang, wer es genauer wissen will, wird damit auf Dauer vermutlich nicht ganz so glücklich.

- Artikel von Jürgen Plate in der [UpTimes](https://guug.de/wp-content/uploads/2019/12/uptimes_2018-01.pdf)

- [Charly Kühnast](https://twitter.com/CharlyKuehnast) hat ebenfalls [einen Artikel](https://kuehnast.com/s9y/index.php?/archives/1295-Luftqualitaet-im-Innenraum-Air-Quality-Sensor-am-Raspberry-Pi.html) dazu geschrieben

- [EVE Room](https://www.evehome.com/de/eve-room) – Temperatur, Luftfeuchtigkeit, VOC: **keine Cloud-Anbindung**, klein, leicht, E-Ink-Display, HomeKit-Kompatibel – der gefiel mir auf Anhieb sehr. Das Aber: er misst Temperatur, Luftfeuchtigkeit sowie flüchtige organische Verbindungen (als Gesamtwert) – jedoch **kein** CO<sub>2</sub>. So schade.

- [Netatmo Healthy Home Coach](https://www.netatmo.com/de-de/aircare/homecoach) – Temperatur, Luftfeuchtigkeit, Lärmpegel, CO<sub>2</sub>: den habe ich, und seine einzige Schwäche ist, dass er über die Netatmo-Cloud läuft – weshalb ich, obgleich ich grundsätzlich zufrieden bin, keinen zweiten anschaffen will. Er ist optisch ansprechend und zuverlässig, läuft derzeit sauber mit openHAB oder Home Assistant ... naja, aber das Ding mit der Cloud halt. Meh.

- [Atmotube](https://atmotube.de/?view=de): Die Atmotube-Geräte sind schick und extrem transportabel – dank ihres Akkus können sie somit auch in U-Bahnen und auf Reisen genutzt werden, im Lieferumfang ist sogar ein Karabiner dabei. Auch hier ist **keine Cloud-Anbindung** erforderlich, die Geräte kommunizieren per Bluetooth mit dem Smartphone. Es gibt zwei Varianten:
    - [Atmotube PLUS](https://atmotube.de/products/atmotube-plus?view=de) – Luftdruck, Temperatur, Luftfeuchtigkeit, VOC: das günstigere Modell, das aber wohl eher zur Angabe einer groben Richtung taugt und weniger dazu, verlässliche Werte zu liefern
    - [Atmotube PRO](https://atmotube.de/products/atmotube-pro?view=de) – Temperatur, Luftdruck, Luftfeuchtigkeit, VOC, Feinstaub 1 μg/m³, Feinstaub 2,5 μg/m³, 10 μg/m³: die teurere Variante unterstützt auch die Feinstaub-Messung

- [Aranet4](https://aranet4.com/) – CO<sub>2</sub>, Temperatur, Luftfeuchtigkeit, Luftdruck: zu dem weiß ich noch so gar nichts Näheres. Hat jemand ihn im Einsatz und kann berichten?

- [air-Q](https://www.air-q.com/technologie): auch ihn gibt es in einer _normalen_ und einer Pro-Variante; er ist derzeit zum Vorzugspreis vorbestellbar – verfügbar dann wohl ab Mitte März 2020 – und ist auch dann so teuer, dass ich ihn nicht ernsthaft in Erwägung ziehe. Optisch finde ich ihn etwas gewöhnungsbedürftig (sieht irgendwie billig aus für mich, wie ein Klo-Einhänger), aber dass er keine Cloud-Anbindung benötigt spricht definitiv für ihn.
    - [air-Q](https://shop.air-q.com/air-Q-Luftanalysator-11-Sensoren): Kohlendioxid, VOC, Kohlenmonoxid, Feinstaub PM10, Feinstaub PM2.5, Feinstaub PM1.0, Temperatur, Luftfeuchte, Luftdruck, Lärm, Stickstoffdioxid
    - [air-Q PRO](https://shop.air-q.com/air-Q-Pro-Luftanalysator-14-Sensoren): er ergänzt die Messpunkte des Basismodells um Sauerstoff, Ozon sowie Schwefeloxid

- [AWAIR](https://www.getawair.com/) – Temperatur, Luftfeuchtigkeit, CO<sub>2</sub>, VOC, Feinstaub: also wenn der mal nicht schick ist, dann weiß ich's auch nicht! Dafür steht er aber auch auf „ausverkauft“ ... Soweit ich es verstehe, kommuniziert auch er über Bluetooth/ Wifi im Haus und benötigt keine Cloud-Anbindung.

- [TFA Dostmann](https://www.tfa-dostmann.de/) hat sowas im Programm:
    - [CO<sub>2</sub>-Monitor AIRCO2NTROL LIFE](https://www.tfa-dostmann.de/produkt/co2-monitor-airco2ntrol-life-31-5003/)
    - [CO<sub>2</sub>-Monitor mit Datenlogger AIRCO2NTROL 5000](https://www.tfa-dostmann.de/produkt/co2-monitor-airco2ntrol-5000-31-5008/)

- Eher in Richtung Industrie gehen dann die Produkte der folgenden:
    - [PCE Instruments](https://www.pce-instruments.com/)
    - [Trotec](https://de.trotec.com/produkte-services/messgeraete/)

### Für Bastler
Ein beliebter Weg ist es, die Messung in Eigenregie mit möglichst minimalem Hardware-Aufwand durchzuführen; an der Front habe ich selbst bislang nicht experimentiert, aber Spaß machen würde das in jedem Fall!
Wenn ihr sowas angehen wollt, sind die folgenden Links vielleicht eine gute erste Anlaufstelle.

- [Arduino Nano mit VOC Sensor und ESP8266](https://forum.arduino.cc/index.php?topic=570275.0) – Forenthread im Arduino-Forum
- [ESP8266 and Air Quality sensor](https://www.hackster.io/fablabeu/esp8266-voc-sensor-eef2ec) – Bauanleitung
- [VOC-Mischgas-Sensor/ Luftqualität (digital) selber bauen](https://www.loxwiki.eu/pages/viewpage.action?pageId=917927) – Bauanleitung im Loxone Wiki zu Hausautomation
- [Building an Army of ESP32 Air Quality Sensors](https://hackaday.com/2019/04/05/building-an-army-of-esp32-air-quality-sensors/) – Projektbericht @Hackaday
- [CO<sub>2</sub>Meter](https://github.com/heinemml/CO2Meter)
- [Bosch Gas Sensor BME680](https://www.bosch-sensortec.com/products/environmental-sensors/gas-sensors-bme680/), [Forenbeitrag @HoneyPi](https://www.honey-pi.de/en/bosch-bme680-luftfeuchtigkeits-druck-temperatur-luftguetesensor/)

## Weiterführende Links
Hinter den nachfolgenden Links verstecken sich weiterführende Inhalte der unterschiedlichsten Art.

- [Selbstbauanleitung CO<sub>2</sub>-Ampel (nicht selbst getestet)](https://www.umwelt-campus.de/forschung/projekte/iot-werkstatt/ideen-zur-corona-krise-1)
- [Why Air Quality Matters](https://www.youtube.com/watch?v=MRqh8oLY7Ik) (Vortrag)
- [VOCs: Definition, Quellen und gesundheitliche Auswirkungen](https://www.air-q.com/blog/vocs-definition-quellen-und-gesundheitliche-auswirkungen) (Zusammenfassung auf air-q.com)
- [Grenzwerte in Sachen Atemluft](https://www.air-q.com/grenzwerte) (übersichtliche Darstellung auf air-q.com)
- [The concentration of carbon dioxide in conference rooms: a simplified model and experimental verification](https://link.springer.com/article/10.1007/s13762-019-02412-5) (Paper)
- [Raumklima im Büro – Auswirkungen, Richtwerte und Einflussmöglichkeiten](https://ergonomie-am-arbeitsplatz.de/raumklima-buero/)
- [Is CO<sub>2</sub> an Indoor Pollutant? Direct Effects of Low-to-Moderate CO<sub>2</sub> Concentrations on Human Decision-Making Performance](https://ehp.niehs.nih.gov/doi/10.1289/ehp.1104789) (Paper)

[^1]: [„Gesundheitliche Bewertung von Kohlendioxid in der Innenraumluft“](https://www.umweltbundesamt.de/sites/default/files/medien/pdfs/kohlendioxid_2008.pdf) (Umweltbundesamt)
[^2]: [„Beurteilung der CO<sub>2</sub>\-Konzentration in Klassenräumen“](https://www.unfallkasse-nrw.de/fileadmin/server/download/Praeventionsmaterialien/CO2_in_Schulen.pdf) (Unfallkasse Nordrhein-Westfalen)
[^3]: [„Richtig lüften: Alles, was Sie wissen müssen“](https://www.co2online.de/energie-sparen/heizenergie-sparen/lueften-lueftungsanlagen-fenster/richtig-lueften/) (co2online.de)
[^4]: [„Römer: 'Tor zur Hölle' tötete mit CO<sub>2</sub>“](https://www.wissenschaft.de/geschichte-archaeologie/roemer-tor-zur-hoelle-toetete-mit-co2/) (wissenschaft.de)