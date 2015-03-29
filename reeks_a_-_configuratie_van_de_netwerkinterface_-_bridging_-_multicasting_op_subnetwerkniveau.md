## 1. Configuratie van de netwerkinterface - Bridging - Multicasting op subnetwerkniveau
### 1.1. Bespreek alle (ook meer recente) opdrachten, inclusief hun opties en output, die onder Linux voor de configuratie van de netwerkinterface kunnen gebruikt worden, zowel op de subnetwerklaag als op de internetlaag. Behandel eveneens eventuele configuratiebestanden (inclusief locatie en inhoud). Vergeet in het bijzonder de opstartbestanden niet.

#### Commando's en hun werking
|Commando|Werking|
|--------|-------|
|`system-config-network`|Fedora netwerk configurator GUI|
|`dmesg` of `/var/log/dmesg`|Toont naam, type, mac adres, interrupt, I/O poort, ... van de gedetecteerde netwerkkaarten bij boot. Auto probing gaat ervoor zorgen dat deze automatisch gedetecteerd en geconfigureerd worden.|
|`lsmod`|Toont de geladen drivers|
|`ifconfig` of `iwconfig`|- Laat ons toe om de ip addressen te configureren en na te kijken.<br />- `ifconfig <interface>` toont info over deze interface, de belangrijkste info zijnde het broadcast adres, het ip adres, subnet, mac adres, status.<br />- `ifconfig <interface> (inet) <ip> netmask <netmask>`: Stel IP configuratie in voor deze interface.<br />- `ifconfig <interface> <up of down>`: activeer of sluit interface (down verwijdert ip alias).<br />- `ifconfig <interface> multicast`: Enabled multicast voor deze interface.<br />- `ifconfig ppp0 <ip> pointtopoint <ip2>`: Stelt pointtopoint  gevinding in. (weinig gebruikt, vaak speciale software).
|`ip addr`|- Recenter alternatief van ifconfig<br />- `ip addr add <ip>/prefix dev <interface>`: Stel IP configuratie in voor specifieke netwerkkaart.
|`ifdown <interface>`|Sluit interface af.|
|`ifup <interface>`|Activeer interface.|
|`ifrename`|Verandert default interface naam.|
|`netstat`|- `netstat -i`: Toont info over alle running geconfigureerde netwerkinterfaces.<br />- `netstat -ia`: Toont info over alle aanwezige geconfigureerde netwerkinterfaces.|

#### Opstartbestanden
|Bestand of map|Werking|
|-------|-------|
|`/etc/conf.modules`, `/etc/modules.conf`, `/etc/modprobe.conf`, `/etc/modprobe.d`|File of map waarin we zetten welke driver voor welke interface gebruikt wordt, met hun opties. deze drivers staan in de `/lib/modules/'uname -r'/kernel/drivers/net` folder. We kunnen hier ook de standaard eth naam veranderen.|
|`/proc/modules`|Bevat de geladen drivers|
|`/etc/sysconfig/network-scripts`|Bevat files die worden gebruikt voor configuratie interfaces on boot, vb: `ifcfg-etho0` keys in deze file zijn: DEVICE, IPADDR, NETMASK en ONBOOT|

<br />
### 1.2. Bespreek het equivalent onder Windows Server, zowel via de Command Prompt, als via de grafische interface.
#### Command Prompt
|Commando|Werking|
|--------|-------|
|`netstat -esp <IP>`|Roept netstat op met Ethernet statistieken (-e), statistieken van TCP, UDP, ICMP (-s) en IP en selecteer IP (-p IP)|
|`ipconfig`|- Dit commando is gelijkwaardig aan `ifconfig`.<br />`ipconfig /all`: Toont alle info|
|`netsh`|- Staat voor netshell, sub shell in command prompt.<br />- `netsh interface ip set address "<Interface Name>" static ^ <IP> <subnetmask>`: zet de details voor de verbinding van deze interface, ^ laat ons toe om dit over meerdere lijnen te schrijven in command prompt.<br />- `netsh interface ip show ipaddress`: Toont een overzicht van alle interfaces en hun ip adressen.

#### GUI
* Plug-and-Play zoekt tijdens boot netwerkkaarten en maakt een interface aan.
* Interfaces staan in `Control Panel -> Network Connections`
* Hernoemen gaat door rechterklik -> rename.
* Info: `<Interface> Right Click -> Properties -> Dubbel klik Internet Protocol Version 4` (hier staat ip, dns, ... onder properties zelf staat andere info zoals hardware info, ...
* Hardware instellingen: `<Interface> Right Click -> Properties -> Configure`.
* Monitoren netwerk verkeer: `<Interface> Right Click -> Status`
* Disable Interface: `<Interface> Right Click -> Disable` of via status menu.

### 1.3. In welke opeenvolgende stappen bepalen een verzameling bridges, die diverse netwerksegmenten tot een enkel subnetwerkgroeperen, welke van hun poorten moeten geblokeerd worden?
We hebben in het labo het Spanning Tree algoritme gebruikt, dit voorkomt lussen in de topologie. Het algoritme werkt zo:

1. Selecteer root bridge (Dit is de bridge met de kleinste bridge ID, bridge priority (2 bytes) + MAC (6 bytes), we kunnen nieuwe bridge laten kiezen door prioriteit te veranderen, dit moet wel in meervouden van 4096!).
2. Vind laagste kost pad voor de root bridge (We kennen kostprijs toe en laten elke bridge de som maken van de andere bridges tot die root bridge, de goedkoopste poort noemt men de designated root. Ook wordt er een designated port geselecteerd, deze wordt gebruikt om de root bridge te bereiken.) 
3. Deactiveer alle andere poorten die geen designated port zijn of root port.
4. Breek gelijkheden op.

<br /><br /><br /><br /><br /><br /><br /><br /><br /><br />
### 1.4. Hoe kun je, via de uitvoer van een enkele opdracht, op een als bridge geconfigureerd Linux toestel op diverse manieren controleren dat de juiste poorten geblokkeerd zijn, en om welke reden de andere poorten geactiveerd moeten blijven?
Ze kunnen de topologie bekijken door het commando `brctl showstp <bridgename>` uit te voeren.

> stp moet wel aan staan, dit doen we via `brctl stp <brname> on`.

    br
        bridge id               8000.56b283b000c3
        designated root         8000.02b2c474422e
        root port               3                   path cost               100
        max age                 20.00               bridge max age          20.00
        hello time              2.00                bridge hello time       2.00
        forward delay           15.00               bridge forward delay    15.00
        ageing time             300.00
        hello timer             0.00 tcn timer 0.00
        topology change timer   0.00 gc timer 183.29
        flags
    
    eth0 (1)
        port id                 8001                state forwarding
        designated root         8000.02b2c474422e   path cost               100
        designated bridge       8000.56b283b000c3   message age timer       0.00
        designated port         8001                forward delay timer     0.00
        designated cost         100                 hold timer              0.00
        flags
    
    eth1 (2)
        port id                 8002                state blocking
        designated root         8000.02b2c474422e   path cost               100
        designated bridge       8000.2ac74dd8ebbc   message age timer       19.04
        designated port         8002                forward delay timer     0.00
        designated cost         100                 hold timer              0.00
        flags
    
    eth2 (3)
        port id                 8003                state forwarding
        designated root         8000.02b2c474422e   path cost               100
        designated bridge       8000.02b2c474422e   message age timer       19.05
        designated port         8003                forward delay timer     0.00
        designated cost         0                   hold timer              0.00
        flags
      
Hier zien we onder het subdeel br wie onze root bridge is en onder de interfaces zien we de kostprijs van het pad (deze kostprijs is de pathcost + designated cost) waarbij de laagste dan de designated port is.

De state blocking betekent dat deze bridge geen paketten gaat forwarden. De geblokkeerde interfaces zijn de interfaces die loops veroorzaken in het netwerk.
### 1.5. Hoe weet een multicast bron of router dat hij verantwoordelijk is om multicast berichten af te leveren aan clients (niet-routers) op de diverse subnetwerken waarop hijzelf is aangesloten. Bespreek de addressering en het protocol dat hierbij gehanteerd wordt, inclusief de bijkomende faciliteiten van meer recente versies ervan. Vermeld eveneens de Linux opdracht die hieromtrent informatie kan verschaffen.
#### Hoe weet men dat men verantwoordelijk is?
Volgens RFC1112 is vastgelegd dat als het IP Multicast Datagram zijn TTL groter is dan 1 dat deze bron verantwoordelijk is voor het afleveren aan clients. 

#### Addressering
elk multicast adres valt in de D range van IPv4 (224.0.0.0 -> 239.255.255.255), deze hebben geen subnetmask. Dit omdat men elk adress apart behandelt.

#### Gebruikte protocol
Het gebruikte protocol is IGMP wat staat voor Internet Group Management Protocol, er zijn 3 verschillende versies van IGMP.

* IGMPv1
    * Stuurt report berichten naar groepadres die bereikt moet worden. (adres is dat van het doel adres)
    * Kan stoppen met zenden op gelijk welk ogenblik.
* IGMPv2
    * Stuurt report berichten naar groepadres die bereikt moet worden. (adres is dat van het doel adres)
    * Stuurt een Leave-Group bericht als deze stopt.
* IGMPv3
    * Zelfde als ICMPv1 en ICMPv2 enkel kan men nu aangeven of men pakketten wil ontvangen of filteren van specifieke adressen. (Adres is hier 224.0.0.22)

Een host gaat IGMP gebruiken om hun interesse te tonen in specifieke multicast groepen. Met als 2 taken om een groep te joinen:
1. Luister op het laag 2 adres die het IP multicast group mapt.
2. Toon interesse om de groep te joinen.

Om de status te verversen gaat men regelmatig IGMP Host Mempership Query Messages sturen. Deze sturen Report messages terug voor elke groep waar ze in geinteresseerd zijn.

