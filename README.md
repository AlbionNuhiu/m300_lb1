# m300_lb CHECKMK Vagrant

By Albion Nuhiu ST18E

# Einleitung
Dies ist die Dokumenation für das Vagrant File für einen CheckMK Server samt einem CheckMK Test Client. Der CheckMK Server ist sobald das Vagrantfile durchgelaufen ist über http://localhost:8080/TBZSide/check_mk/login.py ereichbar. Mit den Default einstellungen von dem Vagrantfile wird dem Benutzer cmkadmin das Passwort Admin1234 gesetzt. Es werden zwei Boxen gestartet mit je 512MB Ram. Die beiden Boxen sind über ein Boxen internes Netzwerk verbunden. Der Server hat die IP 192.168.55.100 und der Client hat 192.168.55.101. Der Client ist vom Host aus nur via SSH ereichbar, sonst ist er hinter der NAT Firewall von VirtualBox geschützt. Der CheckMK Server ist nur via Port 8080 und dem SSH Port ereichbar.

Die Idee für dieses Projekt habe ich gefunden weil bei uns in der Firma auch CheckMK benutzt wird und dadurch mein Interesse geweckt wurde. Vagrant ist nicht Optimal für das aber so kann ich schon einmal testen wie CheckMK funktioniert um es danach als richtige VM zu Instalieren.
# Sicherheit
Diese VM ist nur Normal gesichert, sie hat keine speziele Firewall oder auch kein Reverse Proxy. Der Service Selbst ist durch Benutzername und Passwort gesichert. Es können einfach weitere Accounts über das Webinterface erstellt werden. Thoretisch kann man die VM natürlich mit den vorhin erwänten Metoden sichern, aber leider hat mir für dies die Zeit gefehlt.


# Setup
Das Setup ist ganz einfach:

1. In das geklonte Verzeichnis wechseln.
2. cmd.exe als Admin ausführen.
3. "vagrant up" eingeben.
4. Kurz Warten :-) bis Reading state information... Done in der Konsole steht.
5. CheckMk Starten.
6. Mit dem User cmkadmin und dem Passwort Admin1234 anmelden.
Fertig!

[Setup.txt](https://github.com/AlbionNuhiu/m300_lb/files/6208214/Setup.txt)

# Vagrant File
Hier beschreibe ich das VagrantFile näher.

# VM Config 1
[VMConfig1.txt](https://github.com/AlbionNuhiu/m300_lb/files/6208225/VMConfig1.txt)
______
In diesem Teil wird die ester VM definiert, in diesem Fall der Server. Die Box für diesen Service ist die ubuntu/xenial64 Box. Der Hostname wird auf Server gesetzt. Die VM bekommt zwei Adapter einen NAT und den anderen in einem intern Netzwerk. Der Nat-Netzwerkadapter leitet den Internen Port 80 auf den Port 8080 um, zusätzlich wird von Vagrant der SSH, dieser Port ist Dynamisch. Das interne Netzwerk ist für die komunikation zwischen Server und dem Client. Der Server hat die die interne IP 192.168.55.100. Der Vm wird 512 MB Ram zugewiesen, was für ein kleines Liunux reicht.

# VM Befehl 1
[VMBefehl1.txt](https://github.com/AlbionNuhiu/m300_lb/files/6208226/VMBefehl1.txt)
______
In diesem Teil sind die einzelnen Befehle die nach der Installation ausgeführt werden beschrieben:

sudo apt-get update führt ein Update aus.

sudo wget https://mathias-kettner.de/support/Check_MK-pubkey.gpg lädt den Public Key herunter.

sudo wget https://mathias-kettner.de/support/1.4.0p38/check-mk-raw-1.4.0p38_0.xenial_amd64.deb lädt die CheckMK Instalationsdatei herunter von der Offiziellen Seite.

sudo apt-key add Check_MK-pubkey.gpg fügt den Public Key hinzu damit dan es auch installiert werden kann.

sudo apt-get -y install gdebi-core installiert gdebi welches für die Instalation benötigt wird.

sudo gdebi -n check-mk-raw-1.4.0p38_0.xenial_amd64.deb instaliert mit gdebi CheckMK, der Parameter -n instaliert es ohne nachzufragen.

sudo omd create TBZSide mit diesem Befehl wird in CheckMK eine neue Seite erstellt, für hier wird sie TBZSide genannt.

sudo omd start TBZSide mit disem Befehl wird die vorhin erstellte seite gestartet.

wget http://192.168.55.100/TBZSide/check_mk/agents/check-mk-agent_1.4.0p38-1_all.deb Mit diesem Befehl wird der CheckMK Agent auf dem Server heruntergeladen.

sudo gdebi -n check-mk-agent_1.4.0p38-1_all.deb Instaliert den heruntergeladen Agent.

cd /omd/sites/TBZSide/etc Wechseln in das Web Root Verzeichnis der Webseite.

sudo rm htpasswd Löschen der alten htpasswd Datei.

sudo su in den Root User wechseln.

htpasswd -nb cmkadmin Admin1234 > htpasswd Neue htpasswd erstellen mit dem User cmkadmin und dem Passwort Admin1234.

exit Beenden der Konsole.
# VM Config 2
[VMConfig2.txt](https://github.com/AlbionNuhiu/m300_lb/files/6208249/VMConfig2.txt)
______
In diesem Teil wird die zweite VM definiert, in diesem Fall der Client. Die Box für diesen Service ist die ubuntu/xenial64 Box. Der Hostname wird auf Client gesetzt. Die VM bekommt nur einen Adapter der von dem internen Netzwerk. Das interne Netzwerk ist für die Kommunikation zwischen Server und dem Client zu benutzen. Der Client hat die interne IP 192.168.55.101. Der Vm wird 512 MB Ram zugewiesen, was für ein kleines Linux reicht.

# Vm Befehl 2
[VMBefehl2.txt](https://github.com/AlbionNuhiu/m300_lb/files/6208251/VMBefehl2.txt)
______
sudo apt-get -y install gdebi-core installiert gdebi welches für die Installation benötigt wird.

sudo apt-get -y install xinetd instaliert xinetd welches für die Installation benötigt wird.

wget http://192.168.55.100/TBZSide/check_mk/agents/check-mk-agent_1.4.0p38-1_all.deb Mit diesem Befel wird der CheckMK Agent auf dem Client heruntergeladen.

sudo gdebi -n check-mk-agent_1.4.0p38-1_all.deb Instaliert den herunter geladen Agent.

Somit ist der Client bereit in CheckMK aufgenommen zu werden.
______

# Client in CheckMK aufnehmen
Sobald  die Installation abgeschlossen ist kann man CheckMK per http://localhost:8080/TBZSide/check_mk/login.py öffnen.
Danach meldet man sich mit cmkadmin und Admin1234 an. 
____
![Login](https://user-images.githubusercontent.com/80855358/112553404-be417780-8dc4-11eb-89ee-b22912cc174c.PNG)
____
Danach geht man auf das Main-Dashboard. Hier geht man auf "ADD" und addet einen neuen Host den man kontrollieren kann.
___
![MainDashboard](https://user-images.githubusercontent.com/80855358/112553713-4d4e8f80-8dc5-11eb-98a4-af5ac538f9bc.PNG)
___
Bei ServicesOfHostClient, sieht man alle eingetragenen Hosts und Services.
___
![ServicesOfHostClient](https://user-images.githubusercontent.com/80855358/112553902-a4ecfb00-8dc5-11eb-95b2-a7157a0a8f63.PNG)
___
# Vagrant Destroy
Mit Vagrant Destroy, löscht man alles was dieses Vagrant-File zuvor kreiert hat, also auch die 2 VM's in Virtualbox.
___
![Vagrant_Destroy](https://user-images.githubusercontent.com/80855358/112554056-e8e00000-8dc5-11eb-8159-14b245a9fc52.PNG)
___
# VirtualBox

Wie der VirtualBox-Manager aussehen wird:
___
![VirtualBox](https://user-images.githubusercontent.com/80855358/112554310-5be97680-8dc6-11eb-81ba-70fd9772b68e.PNG)
___
