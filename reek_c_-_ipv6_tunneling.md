# Reek C - IPv6 Tunneling

## Wat is IPv6 over IPv4 tunneling, en waarvoor zal men deze techniek aanwenden ?

* Bij IPv6 over IPv4 tunneling worden IPv6 datagrammen in een IPv4 datagram geplaatst, dit m.b.h. het IPv4 Protocol veld optie 41
* Datagrammen worden ingekapseld en uitgepakt door de dual-stack eindpunten van de tunnel.
* Deze techniek is essentieel bij de eerste fasen van de overschakeling, daar het een end-to-end IPv6 connectiviteit verzekert zonder dat daar de nodige IPv6 infrastructuur aanwezig voor moet zijn.
* In latere fasen garandeert het connectiviteit langs een IPv4-only backbone.

## Bespreek in detail de diverse, ook de meeste recente, tunnel mechanismen waarop men een beroep kan doen. Bespreek telkens onder andere hun toepassingsgebied, vereisten, beperkingen, de relatieve voor- en nadelen, en de gebruikte addresseringsschema's.

### Automatische IPv6 over IPv4 tunneling

* Hierbij zal men indien nodig tunnels creeren doorheen de bestaande IPv4 infrastructuur.
* Tunnels niet nodig indien:
  * Doeladres is IPv4 adres (IPv4 wordt gebruikt)
  * Eindbestemming bevindt zich op hetzelfde netwerk (IPv6 wordt gebruikt)
  * Eerste hop van een route naar de eindbestemming is een IPv6 router (IPv6 wordt gebruikt).
* Indien criteria niet voldaan en het eindadres is IPv4 compatibel (32-bits voorafgegaan door 96 0-bits), dan automatisch tunnelen:
  * Router-to-host tunneling
    * Indien een router op het pad merkt dat de volgende hop geen IPv6 aankan, zal de router automatische tunneling toepassen tot de eindbestemming.
  * Host-to-host tunneling
    * Indien de eerst mogelijke hop na het afzenden geen UPv6 ondersteund, zal de tunnel van afzender tot bestemming lopen.
* Aangezien het aanmaken van een tunnel zo lang mogelijk wordt uigesteld, zal de tunnel:
  * Steeds zo kort mogelijk zijn (aan de kant van de afzender)
  * Langs de kant van de ontvanger is de tunnel steeds suboptimaal (tunnel aangelegd tot eindbestemming).
* het eindpunt moet steeds IPv4 compatibel zijn, waardoor er geen gebruik kan gemaakt worden van de volle 128-bits adresruimte. (Dus enkel toepasbaar op kleine netwerkomgevingen)

<p style="page-break-after:always;"></p>

### Geconfigureerde IPv6 over IPv4 tunneling

* Hierbij worden routers zo geconfigureerd om IPv6 pakketten die over een IPv4 netwerk moeten, via een expliciet geconfigureerde tunnel te sturen.
* Deze tunnel manifesteert zich dikwijls als een aparte interface, waarbij het eindpunt steeds vastligt.
* Het grote voordeel is dat de eindpunten niet IPv4 compatibel dienen te zijn en dus de hele IPv6 adresruimte beschikbaar is.
* Tunneling:
  * Vooral router-to-router tunneling
  * Indien beginknooppunt een standalon IPv6 knooppunt is, kan ook host-to-router tunneling bekomen worden.

### 6to4 tunneling

* Dit is een special vorm van geconfigureerde tunneling, waarbij het IPv4 eindpunt eenduidig kan afgeleid worden uit het IPv6 adres (Subnet van IPv6 gereserveer op: `2002:w.x.y.z::/48`)
* Een 6to4-router is een dual-stack border router, met een publiek IPv4 adres voor de tunnelinterface die de toegewezen 6tp4 adresruimte in het internet injecteert.
* Dezelfde router adverteert de globale 6to4 adresruimte `2002::/16` naar het intranet van de organisatie toe.
* Het geheel van 6to4 routers verbonden met het internet verzekert communicatiemogelijkheden tussen alle 6to4 knooppunten onderling:
  * **6to4 knooppunten om eenzelfde site:** Deze kunnen rechtstreeks met elkaar communiceren via de private IPv6 infrastructuur.
  * **6to4 knooppunten op verschillende sites:** Bij afgifte van het IPv6 pakket aan de 6to4 router, detecteert die aan de TLA van 2002 dat een 6to4 tunnel moet opgezet worden.
* Dit is eveneens toe te passen voor inditividuele hosts:
  * Om connectiviteit te bekomen tussen 6to4 knooppunten en reguliere IPv6 knooppunten, moeten 6to4 relay routers geconfigureerd worden.
  * Dergelijke dual-stack routers bevatten zowel een regulier IPv6 als een 6to4 adres, waardoor 6to4 routers as default route een 6to4 tunne naar een dergelijke relay router kunnen opzetten.
  * De relay router zal vervolgens de 6to4 IPv4 header strippen, om tenslitte het IPv6 datagram correct door te sturen.
* Het gorte nadeel aan deze tunnelingtechniek is dat de routingtabellen hun huidige grootte behouden, en een global hernummering zal nodig zijn om een reguliere IPv6 adresseringsschema te bekomen.

<p style="page-break-after:always;"></p>

### Intra-Site Automatic Tunnel Addressing Protocol (ISATAP) tunneling

* ISATAP gaat IPv6 pakketten versturen tussen dual-stack knooppunten bovenop een IPv4 netwerk
* Is een methode voor het aanmaken van linklokale IPv6 adressen van een IPv4 adres.
* Is een mechanisme om Buren te ontdekken bovenop IPv4

Linklokale adressen aanmaken:

* Maak linklokaal adres met:

  * `fe80::0200:5efe:...` voor globale adressen
  * `fe80::0000:5efe:...` voor private adressen

* Dit mechanisme is gelijkaardig aan 6to4, maar biedt een oplossning voor tunneling tussen 2 subnetten van eenzelfde private netwerkinfrastructuur (verschillende IPv6 eilanden binnen eenzelfde site)
  * 6to4 faalt hierbij, aangezien beide subnetten dezelfde 6to4 prefix kennen en dus rechtstreeks met elkaar zouden moeten kunnen communiceren, zonder gebruik van een tunnel.
* Om dit te verhelpen wordt het IPv4 eindpunt van de tunnel niet afgeleid van de prefix, maar van de laagste 32 bits van een ISATAP adres
* ISATAP adressen zijn samengesteld uit een willekeurige globale unicast prefix (64 bit) en een interface-id van de vorm 0:5EFE:w.x.y.z (met w.x.y.z zijnde een willekeurig IPv4 adres dat aan de interface is toegekend)
  * Het voordeel hiervan is dat ISATAP eenvoudig kan gecombineerd worden met 6to4:
    * De eerste 64 bits van het adres worden bepaald via 6to4
    * Het interface id wordt gegenereerd met behulp van ISATAP
* ISATAP knooppunten zijn steeds dual-stack
* Communicatie tussen knooppunten verloopt als volgt:
  * Tussen 2 knooppunten van dezelfde site (die niet op hetzelfde IPv6 subnet zitten):
    * hier wordt host-to-host tunneling toegepast, indien ze gebruik maken van ISATAP adressen op basis van de linklokale prefix `FE80::/64`
  * tussen 2 knooppunten van verschillende sites (extra configuratie nodig):
    * Knooppunt moet een globale unicast prefix ontvangen
      * Dit kan via uitwisseling van Router sollicication en Router Advertisement, berichten met de borderrouter, eventueel getunneld indien die zich op gescheiden IPv6 eilanden bevinden.
    * Default route binnen de routetabel van het knooppunt:
      * Deze route moet zorgen dat berichten bestemd voor buiten de site afgeleverd worden aan de border router (hetzij via IPv6 routing, hetzij via ISATAP tunneling).
