---
title: "Ein mount.cephfs-Issue"
vgwort_public: "c5df028f856a4fc3bba90e499f3bd52c"
date: 2023-05-26 15:23 +0200
categories:
  - "online"
tags:
  - "cluster"
  - "community"
  - "storage"
description: "Warum mein CephFS sich nicht mounten ließ"
image: "/assets/img/community/2023/05/Mohnblumen.jpg"
hide_description: true
hide_image: false
accent_image:
  background: url('/assets/img/community/2023/05/Mohnblumen.jpg') center/cover
  overlay: false
---

Alles war doch eigentlich cool...

* Replaced by the toc
{:toc}

# Vorgeschichte
Innerhalb der Testumgebung hatte ich ein Mini-[Ceph](https://ceph.io/)-Cluster gebaut.
Unter Verwendung des derzeit aktuellen Release namens [„Quincy“](https://docs.ceph.com/en/latest/releases/quincy/), unter Debian 11, rein IPv6-basiert und – um die Zusammenhänge zu verstehen und möglichst viel zu lernen – komplett von Hand.

Mein Cluster war „up & running“; doch jegliche Versuche, mein CephFS zu mounten, fuhren mehr oder minder kommentarlos an die Wand.
Ich war der Lösung sogar ziemlich nah – am Ende war ich dann aber extrem dankbar für Hilfe von außen 😇

Google war mir keine große Hilfe, deshalb notiere ich es mal hier für die Nachwelt.
Dabei kürze ich die Geschichte extrem ab und konzentriere mich auf die Lösung – damit ihr hoffentlich nicht so lange den Kopf auf die Tischplatte schlagen müsst wie ich 😂

# mount.cephs schlägt fehl – reproduzierbar
Ich versuchte mich an der neuen Notation.

~~~bash
mount -t ceph testuser@61cca62e-0a90-4df0-8c11-ef2411c70b17.test-cephfs=/ /data \
-o secret=AbcDeFgHiJkLmnoPqRsTuVwXyZ+0123456789+==
mount error: no mds server is up or the cluster is laggy
~~~

Seltsam.
**Selbstredend** hatte ich (mehr als einen) MDS, und „laggy“ war überhaupt nichts.
Ich prüfte das zur Sicherheit nochmal...

~~~bash
ceph -s
  cluster:
    id:     61cca62e-0a90-4df0-8c11-ef2411c70b17
    health: HEALTH_OK
    
    services:
      mon: 3 daemons, quorum ceph-alpha, ceph-beta, ceph-gamma (age 11h)
      mgr: ceph-alpha(active, since 6d), standbys: ceph-beta, ceph-gamma
      mds: 1/1 daemons up, 2 standby
      osd: 6 osds: 6 up (since 3d), 6 in (since 6d)

    data:
      ...
~~~

Also alles perfekt.
Ich suchte weiter.
Was in `dmesg` protokolliert wurde, machte mich leider auch nicht klüger – eher im Gegenteil.
Denn offenbar kam ja grundsätzlich eine Verbindung zustande – die dann aber nicht funktionierte?
~~~text
[  330.764994] libceph: mon2 (1)[2001:db8:1:2:3::eb]:6789 session established
[  330.765662] libceph: mon2 (1)[2001:db8:1:2:3::eb]:6789 socket closed (con state OPEN)
[  330.765681] libceph: mon2 (1)[2001:db8:1:2:3::eb]:6789 session lost, hunting for new mon
[  330.767904] libceph: mon0 (1)[2001:db8:1:2:3::e9]:6789 session established
[  330.768325] libceph: client14459 fsid 61cca62e-0a90-4df0-8c11-ef2411c70b17
[  330.771856] libceph: mds0 (unknown sockaddr family 0) connect error
[  331.874915] libceph: mds0 (unknown sockaddr family 0) connect error
[  332.866795] libceph: mds0 (unknown sockaddr family 0) connect error
[  334.885136] libceph: mds0 (unknown sockaddr family 0) connect error
~~~

Ein Netzwerk-Ding?
Aber das Cluster funktionierte ja tadellos.
Und der Client, der mounten sollte, stand im gleichen Netz, *direkt daneben* sozusagen – ein Netzwerk-Problem schloss ich aus.
Zur Sicherheit überprüfte ich auch die MTU – einheitlich 1500, auf allen Systemen.

Ich beschloss, mein `mount` mal `verbose` zu machen – in der Hoffnung auf Erkenntnisgewinn.

~~~bash
mount -v -t ceph testuser@61cca62e-0a90-4df0-8c11-ef2411c70b17.test-cephfs=/ /data \
-o secret=AbcDeFgHiJkLmnoPqRsTuVwXyZ+0123456789+==
parsing options: rw,secret=AbcDeFgHiJkLmnoPqRsTuVwXyZ+0123456789+==
mount.ceph: resolved to: "[2001:db8:1:2:3::eb]:3300,[2001:db8:1:2:3::ea]:3300,[2001:db8:1:2:3::e9]:3300"
mount.ceph: trying mount with new device syntax: testuser@61cca62e-0a90-4df0-8c11-ef2411c70b17.test-cephfs=/
mount.ceph: options "name=test-cephfs,ms_mode=prefer-crc,key=testuser,mon_addr=[2001:db8:1:2:3::eb]:3300/[2001:db8:1:2:3::ea]:3300/[2001:db8:1:2:3::e9]:3300" will pass to kernel
mount.ceph: switching to using v1 address with old syntax
mount.ceph: resolved to: "[2001:db8:1:2:3::eb]:6789,[2001:db8:1:2:3::ea]:6789,[2001:db8:1:2:3::e9]:6789"
mount.ceph: trying mount with old device syntax: [2001:db8:1:2:3::eb]:6789,[2001:db8:1:2:3::ea]:6789,[2001:db8:1:2:3::e9]:6789:/
mount.ceph: options "name=test-cephfs,key=testuser,mds_namespace=test-cephfs,fsid=61cca62e-0a90-4df0-8c11-ef2411c70b17" will pass to kernel
mount error: no mds server is up or the cluster is laggy
~~~

Da war ich der Lösung näher, als mir bewusst war: er *versucht* es mit der neuen Syntax („trying“ zu Port 3300), fällt dann kommentarlos zurück auf die alte Syntax („switching“ zu Port 6789) und fällt dann wieder um.
Beide Ports indes waren grundsätzlich erreichbar.
Der MDS lief auf den Ports 6800 sowie 6801.
Und weil im `dmesg` ja was bezüglich MDS gemeckert wurde (`libceph: mds0 (unknown sockaddr family 0) connect error`) vergewisserte ich mich, dass auch diese erreichbar sind.
~~~bash
netcat ceph-alpha 6800
ceph v2
^C
netcat ceph-alpha 6801
ceph v2
^C
~~~

# Die Lösung
Das brachte dann die Lösung: mein Cluster sprach kein v1.
Der Linux-Kernel `5.10.178-3` meines Debian 11 war zwar der aktuelle für die Distribution – aber er kann **ausschließlich** v1.
Die Unterstützung für v2 kam wohl mit 5.11 – was den Switch zu v1 dann auch erklärt.

Ich konnte den Knoten lösen, indem ich `ms_bind_msgr1 = true` in `/etc/ceph/ceph.conf` definierte und mein Cluster einmal durchstartete.

# Besonderer Dank
Das Gute daran: ich hab auch die Ecken und Winkel von Ceph durch diese Geschichte ziemlich gut kennengelernt.
Denn was hier auf zwei Minuten Lesezeit komprimiert ist, war für mich eine Reise durch Manpages, Release Notes und Issues aller Art.

Mein besonderer Dank geht an [Robert](https://mastodon.gurubert.de/@gurubert), der sich am Brückentag die Zeit für ein (im Endeffekt gar nicht so langes) Telefonat nahm, mit mir *brainstormte* und mich in die richtige Richtung schubste.
Danke! 🥰
