# Reek C - DHCP leaseprocessen en relay-agents

## Geef een overzicht van de verschillende types DHCP berichten. Hoe wordt in het bericht het type aangeduid ?

We duiden het type aan met de optie 53

* DHCP-Discovery bericht
  * Broacast om servers te ontdekken
  * Optie 51 voor specifieke lease-tijd aan te vragen
  * Optie 55 bevat een geordende lijst van gewenste opties
* DHCP-Offer bericht
  * Reactie op DHCP discovery bericht
  * Biedt een client een IP-adreslease aan
  * Biedt ook de aangevraagde opties aan
  * Optie 54 wordt ingesteld met de serveridentificatie (IP-adres)
* DHCP-Request bericht
  * Bericht om de DHCP Offer te finaliseren
    * Dient dus om adreslease te vernieuwe/verlengen
  * Optie 54 wordt ingesteld met de serveridentificatie (IP-adres)
* DHCP-Decline bericht
  * Bericht van client naar server die initialisatieproces herstart indient foutieve configuratieinformatie ontvangen is.
* Positief DHCP-Acknowledgment bericht
  * Bevestigd de lease
  * Stuurt de vereiste DHCP-opties mee
  * DHCP servers wiens voorstel niet geaccepteerd werd stoppen de reservering van het aangeboden IP.
* Negatief DHCP-Acknowledgment bericht
  * Kan bij ongeldig of reeds toegekend IP-adres, of bij aanvraag subnet waarvoor hij geen DHCP scope ter bschikking heeft
  * Herbegin de volledige DHCP initialisatie procedure
* DHCP-Release bericht
  * Bericht van client om huidige lease vrij te geven
* DHCP-Inform bericht
  * Vraagt aanvullende opties aan de server
    * Zoals DHCP Discovery optie 55

<p style="page-break-after:always;"></p>
## Bespreek in detail de opeenvolgende stappen van beide DHCP leaseprocessen.
* **Initialisatie**
    * Client broadcast een DHCP discovery bericht
        * Bronadres: 0.0.0.0
        * Dieladres: 255.255.255.255
        * Opties 51 en 55 ingesteld
    * Servers antwoorden met DHCP Offer
        * Server ID in optie 54
    * Client selecteert lease en vraagt deze formeel aan met een DHCP request
        * Gekozen server ID in optie 54
    * Server bevestigt lease met Positief DHCP Acknowledgment bericht
        * Broadcast bericht zodat andere servers tijdelijk gereserveerde leases kunnen vrijgeven.
        * Weigeren van lease met een negatieve DHCP Acknowledgment
    * Client vraagt eventuele extra informatie op via DHCP Inform bericht
    * Client neemt de lease in gebruik
        * kan deze ook weigeren met DHCP Decline
    * Client geeft lease opnieuw vrij met behulp van een DHCP Release bericht.
* **Vernieuwing** (Gebeurt wanneer lease een tijdlang gebruikt is geweest (ongeveer na helf lease tijd expire time))
    * Client stuurt een DHCP request naar de server die de lease gestuurd heeft
    * Server antwoordt met een DHCP Acknowledgment bericht
        * Met opnieuw de opties ingesteld zodat de client zijn configuratie kan bijwerken
    * Indien geen DHCP Acknowledgment:
        * Client herhaald de stappen met wachttijd die oploopt in machten van 2: 2, 4, 8, 16, ...
        * Indien nog steeds geen Acknowledgment na ongeveer 7/8e van releasetijd, dan wordt de rebinding status geactiveer (tenzij anders gespecifieerd in optie 59).
            * Client probeert lease te vernieuwen bij willekeurige DHCP Server door DHCP Request berichten te broadcasten.
            * Server antwoordt met een DHCP Acknowledgment.
        * Indien na 3 keer nog steeds geen Acknowledgment:
            * Client stopt met lease te gebruiken.
            * Herstart initialisatieproces.

<p style="page-break-after:always;"></p>
## Bespreek het doel en (in detail) de werking van DHCP relay-agents. Welke velden in DHCP berichten helpen deze functie realiseren ?
### Doel
* DHCP maakt gebruik van broadcast en zal dus enkel werken op een enkel subnet
    * Routers laten broadcastberichten niet door
* Een relay agent maakt het mogelijk om DHCP broadcastberichten door te sturen naar andere subnetten.

### Werking

* Ander leaseproces
* Client stuurt DHCP Discovery bericht met het gateway veld op 0.0.0.0
* Relay agent stuurt het bericht door en vult het gateway veld op met het IP adres van de router
  * Doorgeven op 2 manieren:
    * Ofwel berichten sturen gericht naar de actieve server
    * Ofwel bericht broadcasten op alle externe subnetten waarop ze aangesloten is
  * Server antwoordt met een DHCP Offer
    * Adres is eventueel uit een andere scope gehaald, afhankelijk van het gateway veld.
    * Dit bericht wordt rechtstreeks naar de relayagent gestuurd.
  * Relay agent geeft het bericht de adreslease door te broadcasten op het relevante netwerk

### Vereiste velden

* Hop veld
  * Wordt met 1 verhoogd telken een relay agent het bericht opnieuw broadcast.
* Gateway veld
  * hierin wordt de IP van de doorsturende router opgeslagen
  * Dit wordt ook gebruikt om de scope te bepalen van adressen bij keuze van een lease.

## Beschrijf wat er gebeurt op DHCP niveau bij het heropstarten van een Windows Server of een Linux toestel, nadat men een shutdown heeft uitgevoerd.

* Zowel Windows als Linux loggen hun DHCP leases
  * Linux: file `/var/lib/dhcp/dhclient.leases`
  * Windows: `hive register`

<p style="page-break-after:always;"></p>
## Beschrijf wat er gebeurt indien een Windows Server of Linux DHCP-cliënt in eerste instantie geen DHCP-server kan bereiken.
* Linux en Windows DHCP clients kunnen zichzelf configureren om met een tijdelijke IP-configuratie te werken
* = Automatic Private IP Addressing (APIPA) (of automatische IP Configuratie)
* Toegewezen uit gereserveerde klasse B bereik 169.254/16
    * Dit bereik wordt niet op het internet gebruikt
* De DHCP client voert een conflictdetectie uit met een Gratuitous ARP aanvraag
    * Indien duplicaat IP adres wordt een nieuw random IP adres gekozen in dit bereik
* Na toewijzing van het tijdelijke adres blijven de clients in de achtergrond op periodieke intervallen contact zoeken met een DHCP Server
    * Indien toch DHCP Server gevonden, wordt een lease aangevraagd en vervalt het tijdelijke adres.
