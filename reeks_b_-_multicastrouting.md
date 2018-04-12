# Reeks B - Multicastrouting

## 1. Geef de basisprincipes van multicastrouting

* Gebruikt klasse D range (224.0.0.0 - 239.255.255.255)
* Heeft geen mask lengte voor forwarding doeleinden (mask is standaard /32).
* multicast address is group address.
* group member toont interesse voor pakketten te ontvangen verstuurd aan specifieke adressen. (receiver of listener)
* host verstuurt deze pakketten.
* Meerdere ontvangers mogelijk, dit is dus een tak van een boom = distribution tree.
* De data die vloeit van host naar receiver = downstream (via outbound of outgoing interface), van receiver naar host = upstream (via incoming of inbound interface).
* Incoming interface wordt ook IIF genoemd, outgoing interface de OIL.
* Forwarding state wordt bijgehouden in **(S,G)** en **(\*,G)** (S = unicast IP, G = specifiek multicast group IP adress)
* Reverse path forwarding om lus vrije forwarding van multicast pakketten te garanderen, dit door hulp van Reverse Path Forwarding en Shortest Path trees of Rendezvous Point Trees.
* Gebeurt op 2 manieren:
  * Shortest Path Tree (SPT) (Wortel distribution tree is zender)
  * Rendezvous Point Tree (RPT) (Wortel 1 van de routers is kern netwerk)

## 2. Omschrijf de 2 fundamenteel verschillende manieren om deze basisprincipes te realiseren, inclusief hun relatieve voor- en nadelen.

### 2.1. Dense Protocols

* Gaan uit van dichte verdeling en elk subnet minstens 1 geinteresseerde listener heeft.
* Volgt flood-and-prune model.
  * Broadcast traffic door domein
  * Bij ontvangen traffic op interface dichtst bij bron gaat router dit forwarden op alle andere interfaces.
  * Als traffic op niet RPF interface dan discard en zend prune bericht naar de upstream.

**Voordelen**

* Bij weinig of geen prunes is dit efficienter dan sparse protocols.
* Eenvoud, laat toe om makkelijk multicast verdeel boom rooted aan de bron te maken.
* Een bron gebaseerde boom garandeerd het kortste en efficientste pad van bron naar ontvanger.

**Nadelen**

* Niet geschikt bij netwerken met veel prunes
* Schaalbaarheid (door het flooden)

**Voorbeelden**

* DVMRP
* PIM-DM

<p style="page-break-after:always;"></p>

### 2.2. Sparse Protocols

* Gebruikt veronderstelling dat een sparse verdeling van subnetten met minstens 1 geinteresserde ontvanger bestaat. (meer consistent met wat er op het internet bestaat).
* Gaat een core node aanduiden die alle actieve sources in een domein gaat tracken.
* Sparse protocols volgen een explicit join model. Dit forward data enkel naar routers die het opvragen.
* Deze core node (of rendezvous point (RP)) kan traffic ontvangen van de bron via SPT.
* Als een host een groep wilt joinen, dan gaan de rechstreekse verbonden routers de distributie boom joinen richting de RP. Traffic wordt ontvangen door de RP over de SPT en geforward naar geinteresseerde ontvangers via het Rendezvous punt.

**Voordelen**

* Vermindert aantal statussen vereist.
* Vereist geen flooding
* Het RP is de enige router dat op de hoogte moet zijn van alle actieve bronnen in het domein.

**Nadelen**

* Introduceert potentieel voor suboptimal routing.
  * Multicast data gaat eerst door het RP en dan de ontvanger. Ook al is de ontvanger dichter bij de bron.
  * Om dit tegen te gaan gaat PIM-SM toestaan dat de ontvanger zijn DR gaat joinen richting de bron volgens het SPT als het verkeer een bepaalde waarde wordt.

**Voorbeelden**

* PIM-SM

<p style="page-break-after:always;"></p>

## 3. Bespreek in detail de diverse facetten van het momenteel meest gebruikte multicastroutingprotocol.

* 2 Versies
  * Versie 1: Gebruikt IGMP berichten. IGMP versie = 1, IGMP type = 4 (router PIM berichten)
  * Versie 2: Gebruikt IP Protocol 103
    * 4 bits = versie nummer
    * 4 bits = bericht type
  * Versie 1 berichten zijn een subset van versie 2 berichten met bootstrap en kandidaat RP-Advertisements als toegevoegde berichten.
* Opbouwen in 3 fasen:
  * RPT opbouwen
    * Client stuurt IGMP report naar dichtste upstream router
    * Receive DR stuurt:
      * PIM join bericht naar ALL-PIM-ROUTERS groep tot RP
      * Elke router maakt nieuw join bericht
      * Elke router gebruikt eigen RPF tabel om buur te bepalen
  * SPT bron naar RP
    * Bron start met multicast data
    * PIM-SM designated router (DR) stuurt PIM Register berichten unicast naar RP.
    * Als DR bericht van bron ontvangt stuurt hij register bericht.
    * RP ontvangt register bericht.
      * Pakket wordt doorgestuurd naar ontvangers
      * (S,G) PIM join bericht sturen naar bron om SPT op te bouwen
    * RP ontvangt dubbele pakketten
      * RP stuurt register-stop naar DR
    * DR stopt het sturen van register berichten.
  * SPT Bron aan client opbouwen
    * client stuurt (S,G) join naar RPF buur op weg naar bron om SPT te vormen.
    * client ontvangt dubbele berichten (1 van bron en 1 van RP)
    * Client stuurt prune bericht naar traagste
    * Client ontvangt 1 exemplaar en dan is het optimale pad gerealiseerd.

<p style="page-break-after:always;"></p>

## 4. Omschrijf de optionele technieken om de werking van dit protocol nog meer te optimaliseren.

### 4.1. Group-to-RP Mapping

* PIM-SM vereist dat alle routers het actieve RP (rendezvous point) kennen.
* Groep van PIM-SM zenders connecteren fysisch met/of tunnels die dezelfde RP-to-group mapping matrix overeenkomen.
* Group-to-RP Mapping manieren:
  * Statisch: configureer elke router manueel met het adres van de RP voor elke multicast groep. (simpel maar vereist aanpassing als het RP adres veranderd ook backup RP nodig).
  * Dynamisch: PIM bootstrap router (BSR)
* PIM bootstrap = v2, om als RP-set mechanisme te gebruiken moet elke router v2 pakket formaten gebruiken. 1 router moet geselecteerd worden om te dienen als BSR.

### 4.2. Anycast RP

* PIM-SM laat 1 RP toe als actief voor elke multicast groep. (Geeft problemen voor load balancing en redundantie).
* Anycast RP overkomt deze limitatie. Anycast betekend dat meerdere hosts hetzelfde unicast IP adres delen. Dit adres word dan geadverteerd door een routing protocol (OSPF, ISIS, BGP, ...). Als onbereikbare host, dan lever aan volgende dichtste host met anycast adres.
* Geeft meerdere actieve RPs in een PIM-SM domein.
* Anycast RP vormt meerdere PIM-SM domeinen met elk subdomein bestaande uit een RP.
* Anycast adres is meestal secundair adres om problemen met dubbele router ids te voorkomen.

### 4.3. PIM Register Message Processing

* Als PIM-SM DR een multicast pakket ontvangt, dan DR encapsuleert dit pakket in een Register bericht en zend het als een unicast pakket naar het RP voor de groep.
* Dit bericht heeft bron (s) en groep adres (G). antwoord is gebaseerd op 2 factoren:
  * is de RPT al opgezet voor de groep?
  * Is het data aan het ontvangen voor dit (S,G) paar.
* RP negeert RPT en zend Register-Stop bericht als:
  * Geen RPT geconfigureerd is
  * RPT bestaat maar RP is al data aan het ontvangen.
* Nu is RPT geconfigureerd als deze condities zijn voldaan
* RP joined SPT door zenden (S,G) Join aan zijn RPF buur.
* Bij ontvangst Register-Stop bericht stopt DR met Register berichten te sturen en start een Register-Stop timer.
* RP handeld ontvangst van een Null-Register op dezelfde manier als een normaal register bericht.
* Als Register-Stop niet verzonden is en de timer afgaat, dan gaat de DR terug normale Register berichten sturen.

### 4.4. Designate Routers en Hello berichten

* PIM verstuurt periodiek Hello Berichten om elke router zijn bestaan te weten.
* Word verzonden bij opstart of eerste configuratie voor PIM.
* Dit bericht houd geconfigureerde DR prioriteit bij.

<p style="page-break-after:always;"></p>

## 5. Hoe kan men een Linux toestel als multicastrouter laten werken? Geef 2 concrete voorbeelden. Geef aan hoe men de diverse multicastverkeerstromen kan opvolgen.

Kernel flags aanzetten:

* IPv4:
  * CONFIG_IP_MULTICAST=u
  * CONFIG_IP_MROUTE=y
  * CONFIG_IP_PIMSM_V2=y
* IPv6:
  * CONFIG_IPV6_MROUTE=y
  * CONFIG_IPV6_PIMSM_V2=y

Stel router in als unicast forwarding:

    echo 1 > /proc/sys/net/ipv4/ip_forward

als unicast RPF informatie != multicast RPF, disable return path filtering

    echo 0 > /proc/sys/net/ipv4/conf/all/rp_filter

Start daemon

    pimd -d

Monitoren PIM-SM:

    show pim bootstrap
    show pim bootstrap rps
    show pim interface
    show pim interface adres
    show pim join

<p style="page-break-after:always;"></p>

## 6. Sommige routers verzorgen in het multicastproces bijzondere rollen, die ze pas na specifieke verkiezingsprocessen toebedeeld krijgen. Geef een overzicht van deze bijzondere functies, en de overeenkomstige verkiezingsprocessen.

* **Active Querier:**
  * Verstuur Active querier Internet Group Management Protocol Query berichten met IP TTL van 1
  * \> 1 router? ==> Selecteer router met laagst genummerde IP als active querier
  * Andere versturen geen IGMP queries maar luisteren wel nog dei informatie die alle IGMP berichten bevatten. (sla op in cache).
* **Designated Router:**
  * Designated Router Hello berichten met DR prioriteit (standaard 0)
  * De router met de hoogste DR prioriteit word verkozen als DR voor het subnet.
  * Als geen 1 DR prioriteit gebruikt, dan DR is router met hoogste IP.
* **Rendezvous Point:**
  * Dient als ontmoetingsplaats voor multicast listeners en bronnen.
  * Alle routers moeten RP kennen, dit op 2 manieren:
    * Manueel statisch invullen van het RP bij elke router.
    * Dynamisch door PIM Bootstrap (Bootstrap Router BSR)
* **BSR:**
  * Standaard is BSR prioriteit 0, dan is router niet geschikt als BSR
  * Minstens 1 router moet BSR Prioriteit > 0 hebben
  * Elke kandidaat stuurt bootstrap berichten uit op al zijn interfaces
  * Elke buur verwerkt dit en stuurt kopie naar al zijn buren behalve op interface ontvangen.
  * Als kandidaat BSR boostrap ontvangt met BSR > zijn eigen, dan stopt deze met adverteren.
  * Uiteindelijk 1 router over, deze is nu het RP.
