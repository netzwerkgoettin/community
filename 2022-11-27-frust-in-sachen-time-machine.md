---
title: "Frust in Sachen Time Machine"
vgwort_public: "37cf7da10f9e476294f050dd55f01039"
date: 2022-11-27 12:31 +0200
categories:
  - "online"
tags:
  - "backuprestore"
  - "community"
description: "Time Machine must erase your existing backup history and start a new backup to correct this."
image: "/assets/img/community/2022/11/TimeMachineBroken.jpg"
hide_description: false
hide_image: false
accent_image:
  background: url('/assets/img/community/2022/11/TimeMachineBroken.jpg') center/cover
  overlay: false
---

Ich **liebe** Time Machine!
Es hat mir wirklich schon mehr als einmal den Hintern gerettet.

* Replaced by the toc
{:toc}

In meinem 2010er Mac Pro befanden sich 4 Platten: eine SSD mit dem System, zwei Datenplatten im RAID und eine Backup-Platte.
Das funktionierte über Jahre wirklich tadellos!
Doch dann musste ich den MacPro abschaffen – und mir mit Einzug des MacBook Pro ein gänzlich neues Setup überlegen.

## Die Technik dahinter
Seit mehreren Jahren läuft für meine internen Belange eine Synology DS216+II.
Die beiden internen Platten, im RAID zusammengeschaltet, nutze ich für meine Foto- und Dokumentensammlung und tausche sie regelmäßig – im Moment sind es zwei WD Red Plus mit 4TB (WD40EFRX).
Glücklicherweise bin ich kein Daten-Messie und halte da seit Jahren sehr rigoros Ordnung, so dass ich mit den 4TB auch locker auskomme.

Die Synology hängt per LAN am internen 1000er Netz.
Wie denn eben auch das MacBook Pro – welches ich über WLAN übrigens nur sehr selten nutze.
Eine über USB extern an die Synology angeschlossene Platte beherbergte bislang das Time Machine-Backup.
Und irgendwie erwies sich das nicht als dauerhaft praxistauglich.

## Was ging schief?
Bei mir lief es **immer** darauf hinaus, dass das Dateisystem des Sparsebundle-Images kaputtgegangen ist.
Und ich weiß ärgerlicherweise nicht, **wodurch** dieses Problem letztendlich ausgelöst wird.
Tatsächlich läuft das Backup mehrere Wochen, Monate regelmäßig und fehlerfrei durch.
Um dann, aus heiterem Himmel, mit einer Fehlermeldung wie dieser umzufallen:

> Time Machine detected that your backups on "synology" cannot be reliably restored. Time Machine must erase your existing backup history and start a new backup to correct this. 

Der Wortlaut der Fehlermeldung variiert über die verschiedenen Betriebssystem-Versionen, die Kernaussage hingegen ist immer identisch: alles kaputt, alles wegschmeißen und wieder von Null anfangen.
Und das, wo ich doch gerade auf die History großen Wert lege. Grrr.

Übers Interface sind mir keine Reparaturmöglichkeiten bekannt.
Die nachfolgenden Schritte habe ich in iTerm2 vorgenommen.
Damit das funktioniert, musste ich `iTerm.app` in *Preferences &rarr; Security & Privacy &rarr; Privacy &rarr; Full Disc Access* hinzufügen.
Manche Anleitungen besagen auch, man müsse `fsck_hfs` an dieser Stelle hinzufügen; das blieb bei mir wirkungslos.

## Theorie: der erfolgreiche Reparaturversuch
Mehrere Male konnte ich mein Backup in Handarbeit retten.
Dazu bin ich folgendermaßen vorgegangen:

* **automatische Backups deaktivieren**, das ist tatsächlich ein wichtiger erster Schritt: System Preferences &rarr; Time Machine &rarr; Häkchen entfernen bei *Back Up Automatically*

* **das Volume ins System einhängen**, so dass es anschließend unter `/Volumes` zu finden ist; dazu bin ich schlicht durch den Finder marschiert und habe es eingebunden

* **Schreibzugriff auf das Sparsebundle-Image gewähren**, damit eventuell gefundene Fehler auch behoben werden können
  * `-R` – *Change the file flags of the file hierarchies rooted in the files, instead of just the files themselves.*
  * `nouchg` – *clear the user immutable flag (owner or super-user only)*
```
sudo chflags -R nouchg /Volumes/$NAME_DES_LAUFWERKS/$( hostname ).sparsebundle
```
* **Prüfung des Dateisystems**
```
sudo hdiutil attach \
-nomount \
-noverify \
-noautofsck /Volumes/$NAME_DES_LAUFWERKS/$( hostname ).sparsebundle/
```

Es erfolgt sofort eine Ausgabe ähnlich der nachfolgenden:
```
/dev/disk2          	GUID_partition_scheme
/dev/disk2s1        	EFI
/dev/disk2s2        	Apple_HFS
```

Die detaillierten Ergebnisse hingegen finden sich in `/var/log/fsck_hfs.log`. Leider wurde in meinem Fall `FILESYSTEM DIRTY` protokolliert, und somit war klar, dass ich einen Reparaturversuch würde starten müssen.

* der erfolgt über `fsck_hfs` auf die als `Apple_HFS` gekennzeichnete Partition, und dabei haben sich die folgenden Optionen bewährt:
  * `d` – *output debugging info*
  * `r` – *rebuild catalog btree*
  * `f` – *force fsck even if clean*
  * `y` – *assume a yes response*
```
sudo fsck_hfs -drfy /dev/disk2s2
```

**Das wird dauern.**
Im Zweifel auch mehrere Stunden.
Ich empfehle dringend, den Rechner hierzu
* per LAN anzubinden,
* ihn ans Netzteil zu hängen und
* dafür zu sorgen, dass er **keinesfalls** in den Sleep-Mode wechselt.

Läuft es ungünstig und der Aufruf bricht mit einem Fehler ab, so kann `fsck_hfs` mit der *fixe-die-ganz-normalen-Unstimmigkeiten*-Option (`sudo fsck_hfs -p /dev/disk2s2`) doch noch wahre Wunder wirken.
In besonders hartnäckigen Fällen soll es Gerüchten zufolge auch helfen, dem ein erneutes `sudo fsck_hfs -drfy /dev/disk2s2` folgen zu lassen.
Oder es mehrfach auszuführen – wobei man für jeden Lauf entsprechend viel Zeit einplanen muss.

Verläuft die manuelle Reparatur erfolgreich, muss man zu guter Letzt den Backup-Status ebenfalls manuell korrigieren.
Dieser findet sich in der Datei `/Volumes/$NAME_DES_LAUFWERKS/$( hostname ).sparsebundle/com.apple.TimeMachine.MachineID.plist`.
Die beiden Zeilen für die fehlgeschlagene Prüfung müssen ganz raus, und der Wert von `VerificationState` (der vermutlich auf `1` oder `2` steht) muss `0` sein.

~~~diff
File: "com.apple.TimeMachine.MachineID.plist"
 <?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
 <plist version="1.0">
 <dict>
-       <key>RecoveryBackupDeclinedDate</key>
-       <date>2022-10-22T16:13:26Z</date>
        <key>VerificationDate</key>
        <date>2022-11-08T08:38:53Z</date>
        <key>VerificationExtendedSkip</key>
        <false/>
        <key>VerificationState</key>
-       <integer>2</integer>
+       <integer>0</integer>
        <key>com.apple.backupd.HostUUID</key>
        <string>EB3EA526-064B-58FA-807A-5F5144738118</string>
        <key>com.apple.backupd.ModelID</key>
        <string>MacBookPro16,2</string>
 </dict>
 </plist>
~~~


Abschließend ist das manuell eingebundene Sparsebundle-Image wieder aus dem System zu werfen.
```
sudo hdiutil detach /dev/disk2s2
```

Theoretisch **sollte** nun alles wieder einsatzbereit sein.
Zur Sicherheit habe ich dann allemal zuerst ein manuelles Backup ausgeführt.
Je nachdem, wie lange die Reparatur im Endeffekt gedauert hat, wird dieses erste Backup dann relativ lange dauern und auch deutlich umfangreicher sein.

Nachdem das erfolgreich durchgelaufen war, aktivierte ich die Einstellung *Back Up Automatically* wieder und die Welt war schön.

## Praxis: der erfolglose Reparaturversuch
Oder halt auch nicht: nachdem ich das Ganze beim letzten Crash mehrere Tage lang mit `fsck_hfs` malträtiert und immer das gleiche Ergebnis erhalten hatte musste ich es einsehen: das Backup ist final hinüber.

~~~
   [...]
   Executing fsck_hfs (version hfs-583.100.10).
** Checking Journaled HFS Plus volume.
** Detected a case-sensitive volume.
   The volume name is Time Machine Backups
** Checking extents overflow file.
** Checking catalog file.
** Checking catalog B-tree.
hfs_UNswap_BTNode: invalid node height (1)

hfs_swap_HFSPlusBTInternalNode: catalog key #10 invalid length (0)
   Invalid key length
(4, 1511093)
** The volume Time Machine Backups could not be repaired.
        volume type is pure HFS+
        primary MDB is at block 0 0x00
        alternate MDB is at block 0 0x00
        primary VHB is at block 2 0x02
        alternate VHB is at block 5478960789 0x146924e9e
        sector size = 512 0x200
        VolumeObject flags = 0x07
        total sectors for volume = 5478960800 0x146924ea0
        total sectors for embedded volume = 0 0x00
        CheckHFS returned 8, fsmodified = 0
~~~

Ich hatte sogar mal ein paar Sätze dazu getwittert, es aber schnell wieder sein lassen.
Da gab es dann die „Wie kann man für sowas auch bloß eine Syno nehmen, selbst schuld“-Fraktion.
Daneben stand die „Du machst irgendwas konkret falsch, **ich** hatte noch nie Probleme!“-Fraktion.
Und natürlich die „Kein Backup, kein Mitleid“-Fraktion, die sich für besonders witzig hält.

Auf Umwegen erreichten mich dann aber doch Berichte von Usern, denen es, teils mehrfach, genauso erging wie mir.
Leider konnten wir auch gemeinsam nicht herausfinden, was das Ganze verursacht – und wie man sich effektiv davor schützen könnte.

## Als wäre es nicht schon schlimm genug...
Der besonders arschige Move des Schicksals an dieser Stelle ist, dass mir mein zweites Time Machine-Backup – hinterlegt man zwei Ziele, so werden sie abwechselnd beschrieben – quasi zeitgleich verreckt ist.
Hierbei handelte es sich um eine WD Essential 5TB, die per USB über den USB-Hub des Monitors direkt am Rechner hängt.
Die ist noch nicht alt, aber sie klickt...

Ich hatte so die Idee, dass es die extern an die Syno angeschlossene Platte ist, die Probleme machen könnte.
Also packte ich das neue Backup direkt auf Syno, und seit ein paar Wochen läuft das auch problemlos.
Nur – leider hat das halt nichts zu sagen...

## Einschub: das Backup bricht ab
Manchmal trollt mich das Backup auf andere Art: es stoppt nach etwa 30%.
Und versuche ich über die oben genannten Schritte, mich der Sache manuell zu nähern, so erhalte ich von `hdiutil` nur die Meldung
~~~
hdiutil: attach failed - Resource temporarily unavailable
~~~

**Aber:** zum Glück zeigte sich „nur“, dass ich sowohl im LAN als auch im WLAN war und das Backup über die „falsche“ Strecke lief.
Nach Deaktivieren des WLAN und manuellem Anschubsen des Backups lief es dann – puh! – fehlerfrei durch.
Warum es aus dem WLAN heraus abbricht weiß der Himmel – der AP hängt über meinem Kopf, die Syno steht rechts von mir, die Leitung ist frei...

## Fazit
Ich hätte gerne die Daten von der klickenden USB-Platte auf eine nicht-klickende übertragen.
So hätte ich zumindest jene History behalten können.
Das hat reproduzierbar nicht funktioniert, und „AFPS, Case-sensitive, Encrypted" ist auch so eine Sache für sich...
Ich hätte das an der Stelle sicherlich weiter verfolgen können, aber es gibt Wichtigeres 😢

Im Endeffekt hab ich die klickende Platte beschriftet und im Giftschrank verstaut.
Auf der nicht-klickenden ein neues Backup anfahren lassen.
Und auch das Backup in Richtung Syno lasse ich weiterhin laufen.
Ich verlasse mich aber nicht mehr darauf, dass es auch funktioniert, und ich habe es auf 500GB begrenzt.

Als nächstes brauche ich dann neue Platten für die Synology.
Leider hat sich der Black Friday diesbezüglich als nicht hilfreich erwiesen, schade.