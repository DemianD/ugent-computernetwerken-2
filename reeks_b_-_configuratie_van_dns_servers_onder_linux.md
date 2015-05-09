# Reeks B - Configuratie van DNS servers onder Linux
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux computers voor, met corresponderend IP-adres, van de vorm 192.168.16.z . Het getal z lees je af links van de naam van de computer. De getallen rechts van de naam van de computer moet je negeren. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. De rechthoeken die domeinen groeperen stellen dan weer een zone voor. Stippellijnen duiden op een domein/sub­domein relatie. De pijlen laten toe om de primaire name­server van elke zone te achterhalen. Je hoeft geen reverse DNS te configureren.

## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers, waarbij je er rekening moet mee houden dat elk van deze servers ook secundaire nameserver is voor alle zones van de andere server: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
TODO, moet figuur vinden

## Bespreek in detail het begrip secundaire nameserver, inclusief voordelen, beperkingen en problemen. (§3.1 & §3.3.3) 
Secundaire nameservers houden dezelfde informatie bij als primaire

* Moet als type slave gebruikt worden.
* allow-transfer { ip-adressen }; moet gebruikt worden bij options.

Voordelen:
* Worden gebruikt om taak primaire nameserver te verlichten
* Ook om te zorgen voor fouttolerantie in geval dat primaire nameserver uitvalt.

Beperkingen:
* Niet flexibel in gebruik: Wijzigingen enkel op primaire nameserver, secundaire moet dus checken of primaire gewijzigd.

