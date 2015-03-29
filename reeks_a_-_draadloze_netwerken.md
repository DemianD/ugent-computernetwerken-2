## 5. Draadloze Netwerken
### 5.1. Met welke opdrachten kan men op Windows toestellen nagaan welke de karakteristieken zijn van de eigen Wifi interface en van de Wifi zenders in de buurt. Geef aan welke karakteristieken vermeld worden.

|Opdracht|Uitleg|
|--------|------|
|`netsh wlan sh networks`|Toont samenvatting gegevens omgeving.<br /><br />**Karakteristieken:**<br />- Naam, beschrijving, SSID<br />- Netwerktype<br />- Authenticatie<br />- Encryptie|
|`netsh wlan sh networks mode=Bssid`|Toont gedetailleerde gegevens omgeving.<br /><br />**Karakteristieken:**<br />- Netwerktype, Authenticatie, Encryptie<br />- BSSID met: Signaal, Kanaal, Radiotype, Basis Snelheden en Andere Snelheden|
|`netsh wlan sh int`|Toont de eigen gegevens<br /><br />**Karakteristieken:**<br />- Naam, Beschrijving, GUID, Fysiek adres, Status, SSID, BSID<br />- Netwerktype, Radiotype, Authenticatie, Encryptie, Connectie<br />- Kanaal, Ontvangstsnelheid, Verzendsnelheid, Signaal<br />- Profiel, Hosted netwerk status|

#### Output netsh wlan sh networks
    SSID 1 : Geerinck Beneden
        Network type            : Infrastructure
        Authentication          : Open
        Encryption              : WEP

#### Output netsh wlan sh networks mode=Bssid
    SSID 1 : Geerinck Beneden
        Network type            : Infrastructure
        Authentication          : Open
        Encryption              : WEP
        BSSID 1                 : 5c:33:8e:59:75:1a
            Signal              : 58%
            Radio type          : 802.11g
            Channel             : 11
            Basic rates (Mbps)  : 1 2 5.5 11
            Other rates (Mbps)  : 6 9 12 18 24 36 48 54
         
#### Output netsh wlan sh int
    There is 1 interface on the system:
     
        Name                    : Wi-Fi
        Description             : Qualcomm Atheros AR938x Wireless Network Adapter
        GUID                    : 3e423c56-0869-436c-b2ce-c7618f572880
        Physical address        : 90:f6:52:0a:0d:04
        State                   : connected
        SSID                    : Geerinck Beneden
        BSSID                   : 5c:33:8e:59:75:1a
        Network type            : Infrastructure
        Radio type              : 802.11g
        Authentication          : Open
        Cipher                  : WEP
        Connection mode         : Profile
        Channel                 : 11
        Receive rate (Mbps)     : 54
        Transmit rate (Mbps)    : 54
        Signal                  : 58%
        Profile                 : Geerinck Beneden
    
        Hosted network status   : Not available
    
### 5.2. In welk opzicht zijn Ad Hoc routingprotocollen (in wireless meshes) anders dan de meer traditionele routingprotocollen (bedoeld voor internetwerken die uit bekabelde subnetwerken bestaan)?
* Eventuele mobiliteit van de routers
* Uitvallen en herstel van verbindingen meer frequent, met variabele kwaliteit van de link
* Bijkomende criteria voor performantie en metriek, zoals stabiliteit onder mobiele omstandigheden, energie verbruik, signaal/ruis verhouding, ...
* Strikt minimale processing (energieverbruik) en netwerkbelasting gewenst bij topologiewijzigingen
* Alleen gedistribueerde algoritmen aanvaardbaar.

### 5.3.Geef de 2 fundamenteel verschillende manieren om Ad Hoc routingprotocollen te realiseren, inclusief hun relatieve voor- en nadelen en hun optimaal toepassingsgebied.
|Routing Protocol|Beschrijving|Voorbeeld|Voordelen|Nadelen|
|----------------|------------|---------|---------|-------|
|Proactieve of table driven protocollen|Werken met routing tabel zoals RIP of OSPF|Optimized Link State (OLSR)|Routes onmiddelijk bruikbaar|Grote netwerkbelasting|
|Reactieve of on-demand protocollen|
1. Proactieve of table driven protocollen
    * Werken met een routing tabel zoals RIP of OSPF,
    * OLSR: Optimized Link State.
    * Berekend op basis van een metriek
    * Routes worden aangepast bij wijzigen linkkarakteristieken
    * Relatief grote netwerkbelasting
    * Routes onmiddelijk bruikbaar
2. Reactieve of on-demand protocollen
    * Pas als we iets versturen gaan we het pad zoeken
    * Minder netwerkverkeer
    * Eerste verbinding duurt lang.
    * AODV: Ad Hoc On Demand Distance Vector Routing

### 5.4. Bespreek een concreet voorbeeld van een implementatie die tot 1 van deze categorieen behoort, met vooral aandacht voor de verschillen met het traditionele routingprotocol (voor bekapelde internetwerken), waarvan het is afgeleid.

### 5.5. Bespreek een concreet voorbeeld van een implementatie die tot de andere categorie behoort, nu met een gedetailleerde beschrijving van hoe de routingtabellen door specifieke berichtuitwisselingen ingevuld worden.