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

<p style="page-break-after:always;"></p>

### Soorten

* **Hop-by-Hop options**
  * Verzamelt alle opties die ook door tussenliggende routers verwerkt dienen te worden
  * Komt onmiddelijk na de IPv6 header en is TLV geencodeerd.
  * Jumbo payload optie (type 194), maakt afhandelen datagrammen > 65535 bytes mogelijk
  * Router Alert optie (type 5), maakt een router duidelijk dat de informatie in het datagebied dient te worden verwerkt door de router.
  * Opvuloptie (type 1), gebruikt om extensieheader af te lijnen op veelvoud van 8 bytes.
* **Routing Header (RH)**
  * Specifieer de route die het pakket moet afleggen
  * Bevat een lijst van IP adressen die het datagram moet bezoeken
  * Het doeladres moet niet perse het laatste adres in de lijst zijn.
  * Het routing Type ved beschrijft hoe de route moet lopen:
    * Type 0:
      * Adres begint bij eerste adres in de lijst, waarna het doorgestuurd wordt naar de volgende adressen om zo zijn doel te bereiken.
      * Gebruikt loose source routing waarbij tussenliggende routes zijn toegestaan.
  * Het segments Left veld geeft aan hoeveel adressen er nog bezocht moeten worden vooraleer de eindbestemming bereikt wordt.
* **Fragment Header (FH)**
  * Fragmentatie kan enkel toegepast worden door de afzender
  * Dezelfde informatie als IPv4 fragmentatie wordt in de FH gestoken
    * Fragment Identification veld is 2x zo groot als bij IPv4
    * Fragment Offset veld is te klein om jumnogrammen te fragmenteren.
  * Path MTY Discovery proces is nodig om de maximale pakketgrootte te bepalen.
* **Authentication Header (AH)**
  * Implementeert het IPsec mechanisme
  * Laat authenticatie van de inhoud toe
  * De AH bevat een MD5 checksum van het datagram, ontvanger kan controleren of pakket gewijzigd is.
  * AH Bevat volgnummer dat zorgt voor anti-replay bescherming (onderschepte atagrammen kunnen niet opnieuw het netwerk ingestuurd worden.)
* **Encapsulating Security Payload (ESP)**
  * laat toe om geencrypteerde datagrammen te verzenden.
  * Geeft aan dat alle data vanaf dit punt geencrypteerd is.
  * Bevat ook genoeg gegevens om het datagram opnieuw te decrypteren.
  * Op 2 manieren te gebruiken:
    * **Transparante of Transport mode:**
      * Enkel de data wordt geencodeerd, de header en extensieheaders niet.
    * **Tunnel mode:**
      * Volledige datagram wordt versleuteld en in een andere datagram verpakt door een security gateway.
      * De security gateway aan de kant van de ontvanger zorgt voor het uitpakken van het datagram.
  * Aangeraden om steeds de ESP in combinatie met de AH e gebruiken.
* **Desintation Options Header**
  * Bevat alle opties, die enkel door eindbestemming verwerkt dienen te worden
  * Kan verschillende keren voorkomen in het pakket (andere headers niet)
