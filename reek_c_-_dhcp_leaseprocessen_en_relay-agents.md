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

## Bespreek in detail de opeenvolgende stappen van beide DHCP leaseprocessen.

## Bespreek het doel en (in detail) de werking van DHCP relay-agents. Welke velden in DHCP berichten helpen deze functie realiseren ?
## Beschrijf wat er gebeurt op DHCP niveau bij het heropstarten van een Windows Server of een Linux toestel, nadat men een shutdown heeft uitgevoerd.
## Beschrijf wat er gebeurt indien een Windows Server of Linux DHCP-cliënt in eerste instantie geen DHCP-server kan bereiken.

