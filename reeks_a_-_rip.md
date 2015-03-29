## 3. RIP
### 3.1. Geef een gedetailleerde beschrijving van de werking van RIP. Bespreek de mogelijkheden, beperkingen en problemen. Bespreek in het bijzonder de gehanteerde metriek, en hoe RIP berichten verpakt worden.
#### Werking
RIP gaat periodiek alle route vectoren van zijn routingtabel adverteren naar alle routers die hij op subnetwerkniveau rechtstreeks kan bereiken. Op basis van advertisements die een router ontvangt vanwege andere routers gaat deze router de routingtabel systematisch aanvullen. Dit loopt niet synchroon omdat elke router dit op een ander tijdstip uitstuurt.

#### Gehanteerde metriek
RIP gaat de hop afstand gebruiken als metriek, bij het ontvangen van een RIP advertisement verhoogt de router de metriek van de ontvangen routes met 1 vooraleer zijn routing tabel aan te passen. Men kan echter aanpassen dat specifieke verbindingen een grotere bijdrage leveren (vb kleinere bandbreedte, vertraging, ...).

RIP beschouwt routes met metriek 16 of hoger als onbereikbaar.

#### Hoe worden RIP berichten verpakt
RIP berichten worden ingekapseld in UDP segmenten met poortnummers 520. Deze pakketten worden dus niet bevestigd door de ontvanger.

#### Mogelijkheden / Beperkingen
* Maximum diameter = 15
* Veel bandbreedte verbruikt door de broadcasts
* Alle mogelijke paden dienen worden opgeslagen in de routetabbellen.
* Trage convergentie:
    * De meeste implementaties van RIP houden enkel de route met de kleinste metriek bij, de metriek wordt wel verhoogt als een melding van de eerste intermediaire hop ontvangen wordt.
    * De liftetime parameter van via RIP aangeleerde routes wordt standaard ingesteld op 3 minuten, wat 6 keer de default periode is. Dit om pakketten verlies tegen te gaan.
    * Er is een willekeur in het uitwisselingsproces omdat de routers hun berichten slechts periodiek advertern. Hierdoor is er een zeer trage update van de topologie.

#### Problemen
|Probleem|Uitleg|
|--------|------|
|count-to-infinity|vb: subnet 3 is onbereikbaar. Router 2 stelt metriek in op oneindig voor dit subnetwerk. Als router 1 nu advertisement stuurt voor dat router 2 dit doet, dan gaat router 1 subnet 3 wel nog kunnen bereiken, waardoor router 2 deze zet op de waarde van router 1 en 1 verhoogt. Dit blijft voortgaan tot men een metriekwaarde van 16 heeft.|
|routing loop|Berichten worden heen en weer gestuurd zolang de TTL niet 0 is, dit kan leiden tot een overbelasting van het netwerk en dus verlies van pakketten.|

<p style="page-break-after:always;"></p>
### 3.2. Wat wordt bedoelt met reductie van de convergentieperiode (inclusief oorzaken)? Bespreek de verschillende technieken om dit te verwezenlijken.
#### Reductie Convergentieperiode
De reductie van de convergentieperiode gaat de tijd verminderen die nodig is om bij een wijziging terug tot een stabiele toestand te komen.
#### Oorzaken
* Indien enkel de kleinste metrieken bijgehouden worden, worden meldingen van alternatieve paden met hogere metriek genegeerd.
* Lifetime parameter van RIP routes is standaard 3 minuten.
* Routers synchroniseren periodiek waardoor er willekeur is in het uitwisselingsproces.

#### Gebruikte Technieken 
|Tecniek|Betekenis|
|-------|---------|
|Split Horizon|Routers gaan niet langer op het subnetwerk langs waar ze de routes vernomen hebben adverteren. Dit voorkomt count-to-infinity en routing loop maar niet lussen.|
|Poison Reverse|Vermeldt de routes op het subnetwerk langs waar ze deze routes vernomen hebben met een metriek van 16. Dit voorkomt routing loops en count-to-infinity nog meer dan split horizon.|
|Triggered Updates|Routers gaan nu ook bij elke wijziging updates sturen (dit enkel van de wijziging en niet de hele tabel). Als lifetime veld van een route 0 is, dan wordt deze bijna onmiddelijk met metriek 16 gebroadcast, wat de kans op count-to-infinity verkleint. Het reduceert de convergentieperiode aanzienlijk maar er is nog altijd kans op lussen. Ook worden er meer broadcasts gestuurd.|
|General RIP Request|Bij opstart router wordt dit gebroadcast, de ondersteunde routers gaan nu hun volledige routing tabel doorsturen. Dit wordt gericht gestuurd en voorkomt de 30 secondenwachttijd voor hij de actuele info heeft|

### 3.3. Bespreek de verschillende verbeteringen van RIPv2 ten opzichte van RIPv1
#### Multicasting
RIPv1 Broadcast berichten (maakt silent RIP mogelijk) waardoor er meer netwerkverkeer is. Bij RIPv2 wordt er optioneel naar het multicast adres 224.0.0.9 gestuurd wat het verkeer ten goede komt.

#### Subnetmasker binnen route vectoren
Omdat RIPv1 uitsluitend zelfidentificerende adressen gebruikte (A, B, C) en de prefixlengte (subnetmask) niet mee werdt opgenomen kon men supernetten op basis van opeenvolgende klasse-C niet bereiken. RIPv2 geeft het subnetmask meer waardoor dit wel kan.

#### Next Hop veld
RIPv1 geeft hier altijd 0 aan, RIPv2 gaat dit echter gebruiken om het adres van de eerste intermediaire hop aan te duiden. Zo kunnen routers kijken of de hop niet rechtstreeks te bereiken is en worden er dubbele hops vermeden.

#### Eenvoudige authenticatie
RIPv1 heeft geen authenticatie, RIPv2 heeft dit met wachtwoorden zoals MD5. Als dit gebruikt wordt zijn de eerste 20 bytes na de header de authenticatiegegevens.