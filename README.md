# Go1_Research
[GER] Student project about establishing connectivity and adding new ports to a unitree go1 robotdog

Inhaltsverzeichnis
1. Schnittstellen erweitern
  1.1. Mechanische Schnittstellen
    1.1.1. 3d-Druck Aufbau
  1.2. Elektrische Schnittstellen
    1.2.1. Struktur analysieren
    1.2.2. Anschlüsse und Kommunikationen verstehen
2. Go1 mit einem PC verbinden (LAN)
3. Befehle via LAN an den Go1 schicken
  3.1. walk_example.cpp ausführen lassen
4. Go1 mit einem PC verbinden (WLAN)
  4.1. WLAN-Stick am Labor-PC (hat nicht funktioniert)
  4.2. Statische IP auf Hund konfiguriert (für Labornetzwerk)
5. Befehle via WLAN an den Go1 schicken
  5.1. ROS2 zu ROS1 Bridge, Funktion testen
  5.2. Talker & Listener
6. SD-Karte Klonen
7. Ausblick in die Zukunft

_________________________________________________________________

Projektstudie:
1. Schnittstellen erweitern
In diesem Projekt sollen die mechanischen und elektrischen Schnittstellen eines Unitree Go1 Roboterhundes analysiert und erweitert werden.

1.1. Mechanische Schnittstellen
1.1.1. 3d-Druck Aufbau
Um den Roboterhund mechanisch zu erweitern wurde ein spezieller klappbarer Aufbau entworfen, der einfach auf den Hund geschraubt werden kann. Er dient als Geräteträger und kann durch einfache Konstruktion von hexagonalen Steckverbindungen als Grundplatte für säntliche Aufbauten dienen. Beispielsweise können auf dem Go1 Kameras, Lampen, Lautsprecher, Elektronik / Sensorik sicher befestigt werden. Die zugehörigen .stp, .stl. und .ipt (Autodesk Inventor 2021) - Dateien sind im Git hinterlegt.

1.2. Elektrische Schnittstellen
1.2.1. Struktur analysieren
Aus dem Handbuch und Recherche konnte die genaue interne Struktur des Go1 analysiert werden.
Unter der folgenden Website https://www.docs.quadruped.de/projects/go1/html/quick_start.html ist ein QuickStart-Guide, sowie die internen IP-Adressen des Go1 zu finden.
Die interne Systemstruktur ist im Handbuch auf Seite 10 graphisch dargestellt. (Manual V1.4_202112)

1.2.2. Anschlüsse und Kommunikationen verstehen
Mittels Smartphone-App und BrowserURL / Webinterface kann man sich mit dem Wifi-Modul des Go1 verbinden. Hier können Kamerabilder ausgegeben, in einer Simulation die Steuerung geübt, jedoch leider nicht der Hund real ferngesteuert werden. Ein Steuerungsinface ist vorhanden, auf dem Smartphone mit Touchbuttons, auf der Website am PC mit WASD. Jedoch reagierte der Roboterhund auf keinerlei Eingaben über diese Schnittstellen.
Die Kommunikation mit dem MasterController des Hundes findet über einen verbauten RaspberryPi4 statt. Dieser ist auch mit dem Ethernet-Port und den USB und HDMI Anschlüssen an der Oberseite des Go1 verbunden. Hierrüber kann im späteren auch mithilfe einer externen Tastatur und einem Monitor auf den Pi zugegriffen werden und beispielsweise die Netzwerkkonfiguration geändert werden.

2. Go1 mit einem PC verbinden (LAN)
Zum ersten Aufbau einer Kommunikationsschnittstelle wird der Go1, bzw. der verbaute RaspberryPi4, mit dem LaborPC via LAN verbunden.
Um eine erfolgreiche Verbindung aufbauen zu können wurde ein neues LAN-Netzwerk am LaborPC konfiguriert.
Das genaue Vorgehen entspricht der Beschreibung des Repos https://github.com/unitreerobotics/unitree_ros2_to_real
Wie im Repo beschrieben ist für unitree_ros2_to_real ein weiteres Repo notwendig: https://github.com/unitreerobotics/unitree_legged_sdk (Version 3.5.1)
Dieses muss vor sämtlichen Installationen und Programmausführungen geklont sein.
Via SFTP konnten Daten verschickt werden und eine Verbindung zur Kommunikation via SSH aufgebaut werden.

3. Befehle via LAN verschicken
Weiterhin im Repo https://github.com/unitreerobotics/unitree_ros2_to_real
Nach Konfiguration des Netzwerks wird das Package ausgeführt, siehe "Run the Package" im verlinkten Repo.
Bei Ausführung von
ros2 run unitree_legged_real ros2_udp highlevel
und
ros2 run unitree_legged_real ros2_walk_example
wurde der Coden der example_walk.cpp - Datei ausgeführt. Die Kommunikation mit dem Hund via LAN ist somit abgeschlossen und funktionsfähig.

4. Go1 mit einem PC verbinden (WLAN)
Um die Bedienung zu vereinfachen und den Hund ohne ein LAN-Kabel verwenden zu können, wird im nachfolgenden Abschnitt eine WLAN verbindung hergestellt.

4.1. WLAN-Stick am Labor-PC
Um die Netzwerkkonfiguration des internen RaspberryPi's zu ändern wurde ein externer USB-Hub mit einer Tastatur und einer Maus an eine der USB-Schnittstellen auf der Oberseite des Go1's verbunden. Zusätzlich wird auch ein Monitor an den HDMI-Port angeschlossen.
Es wurde versucht mithilfe eines WLan-Sticks am LaborPC eine Verbindung mit dem RaspberryPi4 des Go1 herzustellen.
Nach zahlreichen Versuchen hat es nicht funktioniert, da der USB-Stick kein 5ghz-Netz unterstützt und das vom USB-Stick aufgespannte Netzwerk im Wifi-Menü des RaspberryPi's nicht angezeigt wird. Außerdem ist das Netzwerk nicht im Terminal unter der Netzwerkansicht zu finden.

4.2. Statische IP auf Hund konfiguriert (für Labornetzwerk)​
Im nächsten Schritt wird für die erfolgreiche Kommunikation dem Go1 eine statische IP im Labornetzwerk vergeben und diese für das WLAN0 des Go1 hinterlegt.
Der RaspberryPi hat zwei interne WLAN-Netzwerke:
- WLAN0
- WLAN1
WLAN1 ist hierbei für die innere Kommunikation reserviert und WLAN0 ist eine Kommunikationsschnittstelle für externe Geräte / Verbindungen.

Nach jedem Bootvorgang muss der Go1 nochmal mit der Tastatur und dem Monitor verbunden werden und das WLAN0 neugestartet werden:
sudo ifconfig wlan0 down
sudo ifconfig wlan0 up

Ansonsten kann keine Verbindung mit wlan0 hergestellt werden.
Um diesen Prozess zu automatisieren wurde in das Bootscript am Ende der oben stehende WLAN0-Neustart eingefügt, jedoch unterbrach der RaspberryPi kurz nach dem Bootvorgang die Verbindung und stellte sie nicht wieder her. Im Anschluss wurde versucht mithilfe eines Shellscripts diesen Neustart / Reconnect zu erzwingen, da eine fehlende Berichtung für sudo-Befehle im Bootscript vermutet wurde, bzw. dass nach dem Bootvorgang durch ein externes Script das WLAN0 getrennt wird. Auch mithilfe eines shellscripts trennte sich die Verbindung und konnte nicht automatisch wiederhergestellt werden.

Nach dem manuellen Reconnect des WLAN0 konnten Daten via SFTP verschickt werden und auf den Go1 über SSH zugegriffen werden.

5. SD-Karte klonen
Um Datenverlusten oder sonstigen Problemen bei der Programmierung vorzugbeugen bzw. ein BackUp zu erstellen, wurde die interne SD-Karte geklont.
Dies geschah mittels des Linux-Befehls dd.

sudo dd if=/dev/mmcblk0 bs=1M | gzip -" | dd of=/Desktop/UnitreeBackup.gz status=progress

Zuerst steht das zu klonende Verzeichnis hinter if
bs=1M legt die Blocksize auf 1Mb fest
gzip - komprimiert im Anschluss ans klonen die Datei sodass bei einer 32gb SD-Karte nur ein Verzeichnis mit der größe der tatsächlich belegten 6gb entsteht. Wird dieser Befehl weggelassen, ist das erzeugte Abbild immer gleich dem maximalen Speicherplatz auf der SD-Karte; unabhängig davon wie viel tatsächlich belegt ist.
Unter of wird das Zielverzeichnis angegeben
mit status=progress kann während dem klonen der Fortschritt verfolgt werden.

6. Befehle via WLAN verschicken
6.1. Bridge ROS2 zu ROS1
Da der LaborPC mit ROS2-Foxy arbeitet und der Go1 mit ROS1-Melodic sollte eine Bridge eingefügt werden, um die direkte Steuerung des Hundes über den LaborPC zu ermöglichen.
Die bridge wurde aus diesem Repo übernommen: https://github.com/ros2/ros1_bridge
Die Installation erfolgte gemäß dem Repo.
Um die Funktionalität der Bridge zu testen wurde das Example #1 - Talker Listener auf dem LaborPC zwischen ROS1 und ROS2 durchgeführt; mit positivem Ergebnis.

6.2. Bridge zur Kommunikation mit dem Go1
Im Anschluss wurde die Kommunikation mit dem Go1 über die Bridge getestet, jedoch ohne Erfolg.
Beim Versuch Befehle zu versenden, stieß man nur auf Fehlermeldungen und der Go1 versuchte selbstständig wirre Kombinationen aus IP-Adressen und Ports zu finden.
Eine Vermutung für die IP's und Ports ist, dass die IP's interne sind (einige IPs entsprechen denen von internen Bauteilen, wie dem Kopf oder Motorsteuerungen) und der Go1 nach fehlerhafter Verbindung mit dem LaborPC eine Liste interner IPs zum Reconnect abarbeitet.


7. Ausblick in die Zukunft - Ausstehende Arbeiten
7.1. Inititalisieren einer neuen Node um die Bridge zu testen und die Kommunikation mit dem Go1 über ein WLan-Netzwerk zu etablieren.
7.2. WLan Verbindung mit WLAN0 automatisieren, sodass kein manueller Reconnect nach dem Bootvorgang nötig ist.
7.3. ROS2_Bridge in die Schnittstelle zum Hund integrieren
7.4. Mechanischen Aufbau erweitern, konstruierten Geräteträger um bspw. Servoarm für Kameras / Lampen erweitern
7.5. Eventuelle Kommunikation über einen zusätzlichen RaspberryPi und nicht über den im Go1 integrierten (da bei Problemen mit dem RaspberryPi der gesamte Go1 zerlegt werden muss, um an die SD-Karte zu gelangen)
   Man kann beide via USB an der Oberseite des Go1 miteinander verbinden.














