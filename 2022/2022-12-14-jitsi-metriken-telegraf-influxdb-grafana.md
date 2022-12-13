---
title: "Jitsi-Metriken – mit Telegraf, InfluxDB und Grafana"
vgwort_public: ""
date: 2022-12-14 08:07 +0200
categories:
  - "online"
tags:
  - "community"
  - "tabsvongesternnacht"
  - "communications"
description: "Eigentlich genügt es zu wissen, dass es die JVB-Metriken gibt und wie man an herankommt."
image: "/assets/img/community/2022/12/Jitsi-Telegraf-InfluxDB-Grafana.png"
hide_description: false
hide_image: true
accent_image:
  background: url('/assets/img/community/2022/12/Jitsi-Telegraf-InfluxDB-Grafana.png') center/cover
  overlay: false
---

Für den Betrieb von `#tabsvongesternnacht` ist es relevant, auf regelmäßiger Basis Zahlen zu sammeln.
Doch: wie?

* Replaced by the toc
{:toc}

In meinem ursprünglichen Jitsi-Setup, [hier ausführlich dokumentiert](/yaja-yet-another-jitsi-article/#jitsi-exporter-für-prometheus), nutzte ich einen Prometheus-Exporter.
Der funktionierte gut, wurde jedoch nicht weiterentwickelt.
Und irgendwann liefen Exporter und Jitsi so weit auseinander, dass ich diese Kombination nicht mehr zum Funktionieren bewegen konnte.

## JVB stellt Metriken bereit
`jitsi/jvb` stellt so einiges an Information zur Verfügung. 
Nach wie vor betreibe ich die Maschinerie innerhalb von Docker-Containern.
Im `.env` meines Docker-Setups habe ich `COLIBRI_REST_ENABLED=True` definiert.

Mein JVB-Container heißt `wa-jitsi-jvb`.
Mit einem Bein steht er im Netzwerk `workadventure`, mit dem anderen im Netzwerk `meetup`.
Deshalb würde die übliche Abfrage beide IP-Adressen zutage fördern.
~~~bash
{% raw %}
$ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' wa-jitsi-jvb
172.19.0.6172.18.0.6
{% endraw %}
~~~

Um meine Statistiken zu erhalten, muss ich allerdings nur genau **eine** IP-Adresse des Containers wissen.
(Welche hat sich in meinem Fall als zweitrangig erwiesen – die Metriken lassen sich unter beiden abrufen.)
Ich entscheide mich einfach mal für die Adresse des Netzwerks `workadventure`.
~~~bash
{% raw %}
$ docker inspect -f '{{.NetworkSettings.Networks.workadventure.IPAddress}}' wa-jitsi-jvb
172.18.0.6
{% endraw %}
~~~

Mit `curl` lässt sich das gut prüfen.
~~~bash
$ curl http://172.18.0.6:8080/colibri/stats | jq
{
  "inactive_endpoints": 0,
  "inactive_conferences": 0,
  "total_ice_succeeded_relayed": 0,
  "colibri2": true,
  "total_loss_degraded_participant_seconds": 0,
  "bit_rate_download": 0,
  "local_active_endpoints": 0,
  "muc_clients_connected": 1,
  "total_participants": 0,
  "total_packets_received": 0,
  "rtt_aggregate": 0,
  "packet_rate_upload": 0,
  "p2p_conferences": 0,
  ...
}
~~~

## Konfig für Telegraf
~~~yaml
## File: "/etc/telegraf/telegraf.d/jitsi.conf"
[[inputs.http]]
  name_override = "jitsi_stats"
  urls = ["http://172.18.0.6::8080/colibri/stats"]
  data_format = "json"
~~~

Damit die Daten in InfluxDB landen ist es erforderlich, in Telegraf entsprechend `outputs.influxdb` zu definieren.
~~~yaml
## File: "/etc/telegraf/telegraf.conf"
...
[[outputs.influxdb]]
  urls = ["http://127.0.0.1:8086"]
  database = "telegraf"
  username = "telegraf"
  password = "9gpKcETrQN4MUg7LWl0P0VOF"
...
~~~

Nach einem Restart von `telegraf.service` greift sich dieses die Metriken auf konfigurierter regelmäßiger Basis ab und steckt sie in InfluxDB.
(Ich setze hierbei eine betriebsbereite InfluxDB auf `127.0.0.1:8086` voraus; diese soll nicht Bestandteil dieses kurzen Artikels sein.)

## Abfragen für Grafana
`server` habe ich als Dashboard-Variable in Grafana erfasst:
~~~sql
SHOW TAG VALUES FROM system WITH KEY=host
~~~

Darauf aufbauend lassen sich nun diverse Abfragen formulieren und, beispielsweise als Stats-Panel, in Grafana einbauen.
~~~sql
SELECT last("conferences")
FROM "jitsi_stats"
WHERE ("host" =~ /^$server$/) AND $timeFilter
GROUP BY time($interval)
~~~
Zuletzt erfasste Anzahl laufender Konferenzen
{:.figcaption}

~~~sql
SELECT last("participants")
FROM "jitsi_stats"
WHERE ("host" =~ /^$server$/) AND $timeFilter
GROUP BY time($interval)
~~~
Zuletzt erfasste Anzahl an Teilnehmern in **allen** laufenden Konferenzen
{:.figcaption}

![Exemplarische Darstellung im Grafana-Dashboard](/assets/img/community/2022/12/Jitsi-Telegraf-InfluxDB-Grafana.png)
{:.lead width="100%" loading="lazy"}

Exemplarische Darstellung im Grafana-Dashboard
{:.figcaption}


## Wenn die IP des Containers sich ständig ändert
Nun läuft mein Jitsi nicht permanent, sondern nur bei Bedarf.
Läuft es nicht, müllt Telegraf mir das `syslog` voll, weil es den Endpoint nicht erreichen kann.
Und läuft es dann wieder an ist nicht gesagt, dass die Container mit den gleichen IP-Adressen wie zuvor hochkommen.

Deshalb hat es sich bewährt, innerhalb des Cron-Jobs zum Herunterfahren von Jitsi auch die Telegraf-Konfig zu verwerfen.
~~~bash
sudo rm /etc/telegraf/telegraf.d/jitsi.conf
sudo systemctl restart telegraf.service
~~~

Analog dazu erstellt der Cron-Job, der das Ganze anstartet, eine passende Telegraf-Konfig und gibt auch dem Dienst einen Tritt.
~~~bash
{% raw %}
sudo bash -c 'JVB_IP_ADDRESS=$( docker inspect -f '{{.NetworkSettings.Networks.workadventure.IPAddr
ess}}' wa-jitsi-jvb ) && cat <<EOF > /etc/telegraf/telegraf.d/jitsi.conf
[[inputs.http]]
  name_override = "jitsi_stats"
  urls = ["http://$JVB_IP_ADDRESS:8080/colibri/stats"]
  data_format = "json"
EOF'

sudo systemctl restart telegraf.service
{% endraw %}
~~~

## Fazit
Der Ansatz ist nicht *fancy*; dafür tut er aber, was er soll – ist recht niederschwellig und solide.
Eigentlich genügt es zu wissen, dass es die JVB-Metriken **gibt** und wie man an **herankommt**; was man dann damit anstellt, ist dann schon wieder eine ganz andere Sache.
