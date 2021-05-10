# OctoPi

OctoPrint om op een **Raspberry Pi** te laten werken.
![Image of Yaktocat](/Images/Screenshot%202021-02-08%20at%2018.14.56.png)

## Benodigde onderdelen
* 3D Printer (deuh)
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
Om de camera te configureren kan het bestand octopi.txt aangepast worden. Gebruik ook hiervoor Notepad++.
Pas het bestand aan door de vetgedrukte lijnen bij te voegen. Daardoor wordt de Raspi Camera gebruikt met een resolutie van 1280 x 720 pixels
````
### Configure which camera to use
#
# Available options are:
# - auto: tries first usb webcam, if that's not available tries raspi cam
# - usb: only tries usb webcam
# - raspi: only tries raspi cam
#
# Defaults to auto
#
````
Voeg nu de volgende regel toe om een camera aangeloten op de CSI poort te gebruiken:
````
camera="raspi"
````

````
### Additional options to supply to MJPG Streamer for the USB camera
#
# See https://github.com/foosel/OctoPrint/wiki/MJPG-Streamer-configuration
# for available options
#
# Defaults to a resolution of 640x480 px and a framerate of 10 fps
#
#camera_usb_options="-r 640x480 -f 10"

### additional options to supply to MJPG Streamer for the RasPi Cam
#
# See https://github.com/foosel/OctoPrint/wiki/MJPG-Streamer-configuration
# for available options
#
# Defaults to 10fps
#
````

Voeg hier ook nog deze regel toe om de resolutie te bepalen:
````
camera_raspi_options="-x 1280 -y 720 -fps 20 -br 100 -ex night"
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
Open de browser, en navigeer naar http://octopi.local
Doorloop de stappen van de wizzard
### Installeren van de TouchUI user interface
De opbouw van het scherm is beter geschikt voor kleinere schermen
Om een plug-in te installeren klik op het sleutel-icoon bovenaan het hoofdvenster
Selecteer in de linker kolom **Plugin Manager**
Klik daarna op **Get more...**
Tik een zoekwoord (TouchUI) in om de lijst in te korten
Klik dan op **Install** van de plugin om deze te installeren
Herstart daarna de Raspberry Pi
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


[link to Mardown!]https://guides.github.com/features/mastering-markdown/
