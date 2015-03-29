## 2. Routing
### 2.1 bespreek het doel van routing, de werking en de belangrijkste componenten ervan. Behandel de terminologie en problematiek die het routing proces kenmerkt.
#### Doel
Routing gaat ervoor zorgen dat een bericht wordt afgeleverd aan de gevraagde bestemming. Dit gebeurt door het door te geven aan andere routers als men niet rechtstreeks met de eindbestemming verbonden is, of door het af te geven aan de bestemming als men hier rechtstreeks mee verbonden is.

#### Belangrijkste component
Zijn de router (hardware speciaal gemaakt voor het verhandelen van berichten) en de gateway (computers die tegelijk deel uitmaken van meerdere LAN en/of wan netwerken. Zorgt voor zware belasting)

#### Werking
1. Zoek regels in routingtabel waar IP van de bestemming deel uitmaakt van netwerkadres.
2. Kies meest specifieke regel met de kleinste metriek
3. Indien niet gevonden, stuur IMCP-Destination Unreachable (ICMP brengt afzender op de hoogte van problemen)

#### Termen
Bij routing hebben we verschillende termen:

|Term|Betekenis|
|----|---------|
|Hops|De keten van routers tot de bestemming|
|hop afstand|minimum aantal routers vereist om tot bestemming te geraken|
|diameter van internetwerk|maximale hopafstand die in het internetwerk optreedt|
|Time-to-live|Mechanisme dat ervoor zorgt dat berichten niet eeuwig bijgehouden worden tussen de routers|
|Routingtabel|Houdt bij aan welke volgende router de huidige router het bericht best afgeeft|
|Forwarding address|Volgende router|
|Netwerkadres|Unieke identificatie|
|metriek|Kost om eindbestemming te bereiken (meestal hop afstand), als men via het Type Of Service veld een berekeningswijze selecteert dan spreekt men van TOS Routing|
|lifetime|Zorgt ervoor dat routes expiren waardoor de routes up to date blijven (voral bij automatische routingtabellen)|

<p style="page-break-after:always;"></p>
#### Problemen
|Probleem|Uitleg|
|--------|------|
|Routing Loops|Treedt op als routingtabel voor eindbestemming pad construeert dat terugverwijst naar 1 van de intermediaire routers van het pad. Hierdoor worden berichten rondgestuurd tot TTL verstreken is.|
|Black holes|Treedt op als het pad naar de eindbestemming niet meer functioneert en dit falen nog niet is opgenomen in de routingtabel van de vorige router op het pad. De router gaat dus IP-datagrammen blijven sturen naar de black hole router en die zullen allemaal verloren gaan.|

### 2.2 Geef de diverse (inclusief de meest eenvoudige) alternatieven om de routingtabel van niet-routers (o.a. toestellen die slechts op 1 subnetwerk zijn aangesloten) te configureren. Indien er hiertoe op Linux of Windows bijzondere componenten moeten geinstalleerd of geconfigureerd worden, bespreek hoet dit moet gebeuren.
|Manier|Uitleg|
|------|------|
|Statisch: Routetabel handmatig invullen|We kunnen de routingtabellen zelf invullen.<br /><br />- **Op Linux: **Eerst voeren we `sysctl -w net.upv4.ip_forward=1` uit wat ons de rol van router gaat geven en daarna gebruiken we ifconfig, zo kunnen we bijvoorbeeld routes toevoegen door: `ip r r 192.168.18.0/24 via 192.168.16.10` te gebruiken wat een route toevoegt die via de router 16.10 gaat gaan.<br /><br />- **Op Windows: **Hier gaan we router spelen door *IPEnableRouter* op 1 te zetten in het register (in `My Computer\\HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\Tcipip\Parameters`) en daarna routes toe te voegen via `route add 192.168.17.0 mask 255.255.255.0 192.168.16.1`.|
|Dynamisch: Router discovery met ICMP|Gebruik van ICMP berichten om de gegevens van een standaardgateway op een subnetwerksegment op te sporen. 2 soorten: <br />- ICMP-Router sollicitation: Computers verzenden bericht naar 224.0.0.2 om de routers te ontdekken<br />- ICMP-Router Advertisement: Antwoorden op sollicitation (en periodiek om de 7 minuten als keep alive).<br />- We kunnen ICMP redirection gebruiken als optimalisatie.|
|Eavesdropping of wiretapping|Wissel berichten uit via broadcast, hierdoor kunnen werkposten passief meeluisteren en hun routingtabellen aanpassen.<br />- **Op Linux:** Activeren met `routed -q`<br />- **Op Windows:** Installeer RIP Listener via `Control Panel -> Add or remove windows component -> Network Services -> RIP Listener`. (Er wordt enkel naar RIPv1 geluisterd).|

### 2.3 Vergelijk de voor- en nadelen van statische en dynamische routing, zonder in detail in te gaan op specifieke routingprocotollen.
#### Statisch: 
|Voordelen|Nadelen|
|---------|-------|
|Gemakkelijk te implementeren in een klein netwerk|Enkel goed voor makkelijke topologieen|
|Veilig, geen advertisements verzonden|Configuratie moeilijkheid verhoogt snel|
|Voorspelbaar|Als link faalt, dan kan men geen verkeer rerouten, dus manuele interventie nodig|
|Geen algoritme nodig, dus CPU en RAM besparing||

#### Dynamisch:
|Voordelen|Nadelen|
|---------|-------|
|Goed voor alle topologieen|Moeilijker in het begin|
|Niet afhankelijk van netwerkgrootte|Minder veilig door broadcast en multicast routing updates. (heeft passieve interfaces en authenticatie nodig voor betere beveiliging)|
|Automatische aanpassingen om verkeer om te leiden|Route hangt af van huidige topologie|
||Meer CPU, RAM en badnwith nodig|
### 2.4 Maak een classificatie van routingprotocollen, volgens 2 criteria. Omschrijf de terminologie die je hierbij invoert. Geef ondermeer aan op welk niveau hetzelfde routingprotocol actief kan zijn, en hoe aan schaalbeperking kan worden gedaan. Geef van elke klasse de meest courante vertegenwoordigers. Het is niet de bedoeling in te gaan op een gedetailleerde vergelijking tussen de verschillende klassen en hun specifieke vertegenwoordigers.
#### Volgens Routingprotocollen
##### Interior Gateway Protocollen (IGP) / Intra-domein protocollen
Wisselt routing informatie uit tussen gateways binnen eenzelfde autonoom systeem. Berekenen de beste routes naar om het even welke eindbestemming en verspreiden deze info over alle routers.

* Routing Information Protocol (RIP)
* Enhanced Interior Gateway Routing Protocol (E-IGRP)
* Open Shortest Path First Protocol (OSPF)
* Intermediate System to System Protocol (IS-IS)

##### Exterior Gateway Protocollen / Inter-domein protocollen
Wisselt routing informatie uit tussen verschillende autonome systemen, waardoor er ook verkeer tussen die systemen mogelijk is. Dit is echter enkel voor de Border Routers van dit Autonoom Systeem.

* Exterior Gateway Protocol (EGP)
* Gateway to Gateway Protocol (GGP)
* Border Gateway Protocol (BGPv4) (Bijna altijd gebruikt, geeft elk AS een unieke nummer)

#### Volgens Technologie (Welke informatie wordt uitgewisseld)
##### Distance Vector Protocollen
Vereist dat router buren informeert bij veranderingen

* BGPv4
* RIP
* EIGRP

##### Link State Protocollen
Elke router maakt mapping van de connecties in het netwerk en berekent de beste weg naar elke mogelijke bestemming (routing tabel).

* OSPF
* IS-IS