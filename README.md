# OctoPi

OctoPrint om op een **Raspberry Pi** te laten werken.
![Image of Yaktocat](/Images/Screenshot%202021-02-08%20at%2018.14.56.png)

## Benodigde onderdelen
* 3D Printer
* Raspberry Pi 3 of 4
* Een microSD kaart van 8 GB, of meer indien timelapse beelden moeten opgenomen worden
* Een USB kabel om de Raspberry Pi te verbinden met de 3D printer
* Een voeding voor de Raspberry Pi. Die moet 5,1V bij minimaal 2A kunnen leveren
* Een 3.5" touch screen
* Rapsberry Pi Camera (5 Megapixel voldoet ruimschoots)
* Een behuizing voor de camera: https://www.thingiverse.com/thing:2864095
* Een behuizing voor de Raspberry Pi met display

## Info
Informatie kan op de volgende locaties gevonden worden:
* OctoPi image download: https://github.com/guysoft/OctoPi
* Hoe OctoPi installeren
  * https://www.electromaker.io/tutorial/blog/setup-octoprint-for-the-anet-a8-with-a-raspberry-pi
  * https://all3dp.com/2/octoprint-on-anet-a8-how-to-get-started/
  * https://www.youtube.com/watch?v=thQ3PdLxqe8
  * https://community.octoprint.org/t/octoscreen-a-new-software-to-use-octoprint-with-lcd/10629
* Schermdriver XTP2046 installeren
  * https://medium.com/@tengfone/setting-up-raspberry-pi-4-3-5-touch-screen-xpt2046-349e484a7813
  * http://www.lcdwiki.com/3.5inch_RPi_Display
  * https://github.com/goodtft/LCD-show

## Werkwijze
Downloaden van de OctoPi image.
Gezipt bestand unzippen.
De OctoPi image op een SD Card schrijven m.b.v. Win32 Disk Imager of Raspberry Pi Imager.

### WiFi configureren
Plaats de microSD kaart in de computer, en gebruik een teksteditor om het bestand **octopi-wpa-supplicant.txt** aan te passen (Notepad ++, of TextEdit)
````
## WPA/WPA secured
#network={
#  ssid=”put SSID here”
#  psk=”put password here”
#}
````
In bovenstaande moet de # verwijderd worden vanaf network, en dient het SSID en paswoord voor het draadloos netwerk ingevuld te worden.
Daarna moet ook nog het land opgegeven worden om WiFi te kunnen gebruiken:
````
Country=GB # United Kingdom
````
Moet als volgt gewijzigd worden:
````
#Country=GB # United Kingdom
````
En moet er nog een lijn toegevoegd worden (net voor of net na de vorige lijn)
````
Country=BE # Belgium
````
### Camera configureren
Om de camera te configureren kan het bestand **octopi.txt** aangepast worden. Gebruik ook hiervoor Notepad++.
Pas het bestand aan door de onderstaande lijnen bij te voegen.
````
camera="raspi"
camera_usb_options="-r 640x480 -f 10"
camera_raspi_options="-fps 10"
   -o "output_http.so -w $camera_http_webroot $camera_http_options"
camera_http_webroot="./www-octopi"
camera_http_options="-n"
camera_streamer=mjpeg
````

### Paswoord aanpassen
Plaats de microSD kaart in de Raspberry Pi.
Verbind de netwerkkabel met de Raspberry Pi, of maak gebruik van de WiFi verbinding.
Koppel de voeding aan de Raspberri Pi.
SSH naar de Raspberry Pi:
````
SSH pi@octopi.local
````
Geef het paswoord op: 
````
raspberry
````
Wijzig het paswoord
````
pi@octopi:~ $ passwd
Changing password for pi.
Current password: raspberry
New password: new_password
Retype password: new_password
passwd: password updated succesfully
````
### Camera activeren
````
sudo raspi-config
````
Gebruik `3 Interface Options`.
Daarna `P1 Camera`, en bevestig met `YES`.

### Camera controleren
Controleer of de camera herkent wordt:
````
pi@octopi:~ $ vcgencmd get_camera
````
Dit moet het volgende tonen bij succesvolle detectie
````
supported=1 detected=1
````
### Update installeren
Controleer de huidige Linux versie:
````
pi@octopi:~ $ uname -sr
Linux 5.4.79-v7l+
````
Controleer welke updates er beschikbaar zijn:
````
pi@octopi:~ $ sudo apt-get update
````
Installeer de updates:
````
pi@octopi:~ $ sudo apt-get upgrade
````
Herstart de Raspberry Pi:
````
pi@octopi:~ $ sudo shutdown -r now
````
of
````
pi@octopi:~ $ sudo reboot
````
### Schermdriver installeren
Om het aanraakscherm te kunnen gebruiken moet er eerst een driver geïnstalleerd worden.
Tik volgende commando’s in:
````
pi@octopi:~ $ sudo rm -rf LCD-show
pi@octopi:~ $ git clone https://github.com/goodtft/LCD-show.git
pi@octopi:~ $ chmod -R 755 LCD-show
pi@octopi:~ $ cd LCD-show/
pi@octopi:~/LCD-show $ sudo ./LCD35-show
````
Bovenstaande commando’s doen het volgende:
* Indien er reeds een driver geïnstalleerd was, dan wordt die verwijderd.
* De nieuwe schermdriver wordt gedownload en geïnstalleerd.
* De schermdriver wordt uitvoerbaar gemaakt, en kan ook beschreven worden door de eigenaar (pi).
* Wijzig de actieve directory.
* Start de schermdriver.
## OctoPi configuratie
Open de browser, en navigeer naar http://octopi.local.
Doorloop de stappen van de wizzard.
### Installeren van de TouchUI user interface
De opbouw van het scherm is beter geschikt voor kleinere schermen.
Om een plug-in te installeren klik op het sleutel-icoon bovenaan het hoofdvenster.
Selecteer in de linker kolom **Plugin Manager**
Klik daarna op **Get more...**
Tik een zoekwoord (TouchUI) in om de lijst in te korten.
Klik dan op **Install** van de plugin om deze te installeren.
Herstart daarna de Raspberry Pi.
````
pi@octopi:~ $ sudo reboot
````
### Installeren van OctoRelay
Bij de configuratie moeten we rekening houden met de werking van touch-interface van het schermpje, die ook enkele GPIO pinnen gebruikt
* Relay 1 (Light) wordt aangestuurd door GPIO 4. Dit wordt GPIO 5.
* Relay 2 (Printer) wordt aangestuurd door GPIO 17. Dit wordt GPIO 6.
* Relay 3 (Fan) wordt aangestuurd door GPIO 18. Dit wordt GPIO 13.
* Relay 4 wordt niet gebruikt.
Klik daartoe op het sleutel-icoon.
In de configuratielijst, klik op “OctoRelay”.
Pas de configuratie aan.
## Inloggen op de webserver van de OctoPi.
Gebruik de webbrowser om naar de Octopi te browsen:
````
http://octopi.local
````
Geef de gebruikersnaam en paswoord in die bepaald werd bij de installatiewizzard.
## Installatie van de grafische omgeving
### Installatie Desktop Environment
Om het TouchUI scherm weer te geven op het klein aanraakschermpje moet op de Raspberry Pi de “Desktop environment” geïnstalleerd worden.
Daartoe moet er ingelogd worden op de Raspberry Pi. Dit kan via het aanraakschermpje, of via een terminal (ssh). Gebruik volgende inloggegevens:
````
Login: pi
Password: new_password
````
Voer volgende commando in:
````
pi@octopi:~ $ sudo /home/pi/scripts/install-desktop
[sudo] password for pi: new_password

This will install the desktop environment on your Pi
Please keep in mind that the desktop environment needs
system resources that then might not be available for
printing, possible leading to print artifacts.
It is not recommended to run the desktop environment
alongside OctoPrint if you do not have a Pi with
multiple cores (e.g. Pi1 or PiZero). Even then, use
at your own risk.

If you do not want to install the desktop environment
after all, please hit Ctrl+C now.

Press any key to continue or Ctrl+C to exit...
````
Klik op `Enter` om door te gaan.
Daarna nog `yes` ingeven om te bevestigen:
````
type ‘yes’ now. Type ‘no’ if not.
Finish with ENTER:yes
````
De desktop environment wordt geïnstalleerd.
Waarna een herstart noodzakelijk is:
````
pi@octopi:~ $ sudo reboot
````
Daarna terug ssh naar de raspberry pi:
````
ssh pi@octopi.local
pi@octopi.local's password: new_password
````
### Schermdriver installeren
Na installatie van de desktp zal de schermdriver opnieuw geïnstalleerd moeten worden.
````
pi@octopi:~ $ sudo rm -rf LCD-show
pi@octopi:~ $ git clone https://github.com/goodtft/LCD-show.git
pi@octopi:~ $ chmod -R 755 LCD-show
pi@octopi:~ $ cd LCD-show/
pi@octopi:~/LCD-show $ sudo ./LCD35-show
````
Na installatie van de schermdriver wordt er automatisch gereboot.

### Chromium installeren
Log in (ssh) op de Raspberry Pi en voer de onderstaande commando's uit:
Bewerk het bestand sources.list
````
pi@octopi:~ $ sudo nano /etc/apt/sources.list
````
Voeg de onderstaande twee regels toe aan het bestand:
````
deb http://ppa.launchpad.net/canonical-chromium-builds/stage/ubuntu vivid main
#deb-src http://ppa.launchpad.net/canonical-chromium-builds/stage/ubuntu vivid main
````
Bewaar de wijzigingen met ctrl+X, Y, en enter
Voeg de APT key toe:
````
pi@octopi:~ $ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys DB69B232436DAC4B50BDC59E4E1B983C5B393194
````
Installeer het chromium browser package:
````
pi@octopi:~ $ sudo apt update
pi@octopi:~ $ sudo apt install chromium-browser
````
Nadat de installatie is voltooid en de Raspberry Pi is herstart zal de browser verschijnen in de map: Menu > Internet en kun je deze gebruiken om te browsen over het internet.
### OctoPi in Chromium in kiosk mode starten
Maak een map om er een script in te plaatsen
````
pi@octopi:~ $ mkdir .local/bin
````
Maak het script
````
pi@octopi:~ $ nano .local/bin/octostart.sh
````
Plaats daarin het volgende:
````
#!/bin/bash
/usr/bin/chromium-browser --kiosk http://octopi.local
````
Sla dit op: ctrl-O, bevestig, en sluit af met ctrl-X

Het bestand moet dan nog uitvoerbaar gemaakt worden:
````
pi@octopi:~ $ chmod +x .local/bin/octostart.sh
````
Het script moet nu uitgevoerd worden bij het opstarten van de Raspberry Pi.
Dit wordt gedaan door een .desktop bestand aan te maken, waarin verwezen wordt naar het script. Dit bestand moet dan in de autostart map komen:
````
pi@octopi:~ $ mkdir .config/autostart
pi@octopi:~ $ nano .config/autostart/octostart.desktop
````
Plaats daarin de volgende inhoud:
````
[Desktop Entry]
Type=Application
Exec=/home/pi/.local/bin/octostart.sh
````
Herstart de Raspberry Pi:
````
pi@octopi:~ $ sudo shutdown -r now
````
