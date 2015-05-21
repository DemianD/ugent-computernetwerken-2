# Reek C - IPv6 berichtstructuur
## Bespreek in detail de structuur van IPv6 berichten.
* IPv6 heeft een vaste headergrootte (IPv4 niet)
* Opties worden geencodeerd als een gelinkte lijst van aparte extensieheaders.
    * Routing wordt efficienter, omdat de opties niet steeds onderzocht dienen te worden
* Zowel header als extensieheaders bevatten een 'Next header' veld
    * Geeft aan welk type data er volgt
        * Een nieuwe header (type gespecifieerd) 
        * Data van een ander protocol
* Het geheel van extensieheaders + ingekapselde gegevens wordt de payload genoemd
* IPv6 Header:
    * Wel in IPv4, niet in IPv6
        * **Header Length:** IPv6 heeft vaste grootte (40 bytes)
        * **Checksum:** Dit wordt verzorgd door de transportlaag
        * **Fragmentatievelden:** niet meer mogelijk
    * Wel in IPv4, ook in IPv6
        * **Version:** Ingevuld met waarde 6
        * **Traffic Class:** Analoog aan het TOS veld, geeft mogelijkheid tot gedifferentieerde dienst is default 0
        * **Payload length:** Aantal bytes in datagram
        * **Hop Limit:** Bovengrens voor de levensduur van een datagram.
        * **Bron- en doeladres:** 16 bytes lang
    * Niet in IPv4, Wel in IPv6
        * **Flow label:** Detecteerd samenhangende pakketten en routers die dit in cache hebben kunnen het pakket direct forwarden zonder header te interpreteren.
        * **Next Header:** Welk type data volgt er

##Geef en bespreek de verschillende soorten extensieheaders die momenteel voor IPv6 gedefinieerd zijn.
### Algemeen
Alle extensieheaders (behalve encapsulating security payload) volgen dezelfde structuur.

* Next Header
* Extension Header Length
* Extension Header Data

Deze headers komen voor in een vastgelegde volgorde.

### Soorten
* Hop-by-Hop options
    * Verzamelt alle opties die ook door tussenliggende routers verwerkt dienen te worden
    * Komt onmiddelijk na de IPv6 header en is TLV geencodeerd.
    * Jumbo payload optie (type 194), maakt afhandelen datagrammen > 65535 bytes mogelijk
    * Router Alert optie (type 5), maakt een router duidelijk dat de informatie in het datagebied dient te worden verwerkt door de router.
* Routing Header (RH)
* Fragment Header (FH)
* Authentication Header (AH)
* Encapsulating Security Payload (ESP)
* Desintation Options hEader
Soorten: