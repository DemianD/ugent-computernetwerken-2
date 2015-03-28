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
### 1.4. Hoe kun je, via de uitvoer van een enkele opdracht, op een als bridge geconfigureerd Linux toestel op diverse manieren controleren dat de juiste poorten geblokkeerd zijn, en om welke reden de andere poorten geactiveerd moeten blijven?
### 1.5. Hoe weet een multicast bron of router dat hij verantwoordelijk is om multicast berichten af te leveren aan clients (niet-routers) op de diverse subnetwerken waarop hijzelf is aangesloten. Bespreek de addressering en het protocol dat hierbij gehanteerd wordt, inclusief de bijkomende faciliteiten van meer recente versies ervan. Vermeld eveneens de Linux opdracht die hieromtrent informatie kan verschaffen.