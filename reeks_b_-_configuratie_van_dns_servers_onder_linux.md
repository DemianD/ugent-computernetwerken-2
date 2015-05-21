# Reeks B - Configuratie van DNS servers onder Linux
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux computers voor, met corresponderend IP-adres, van de vorm 192.168.16.z . Het getal z lees je af links van de naam van de computer. De getallen rechts van de naam van de computer moet je negeren. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. De rechthoeken die domeinen groeperen stellen dan weer een zone voor. Stippellijnen duiden op een domein/sub­domein relatie. De pijlen laten toe om de primaire name­server van elke zone te achterhalen. Je hoeft geen reverse DNS te configureren.

## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers, waarbij je er rekening moet mee houden dat elk van deze servers ook secundaire nameserver is voor alle zones van de andere server: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
Figuur gegeven op examen

### Voorbeeld
Maak zonebestanden aan per DNS server:

**XX.us.zone**

    ;
    ;	    Zone File for "XX.us" - Internal Use ONLY
    ;                                      
    ; Append XX.us after every relative domain
    $TTL 60
    @    IN    SOA    corelli.sonatas.XVII.it. xavier.geerinck.ugent.be (
                      1            ; serial
                      1            ; refresh
                      1            ; retry
                      1            ; expire
                      1 )          ; minimum
    ;
         IN    NS    corelli.sonatas.XVII.it.                       ; Authoritative Name Server
    ; Delegations
    vocal				          IN  NS	gershwin.vocal         ; vocal.xx.us. is controlled by gershwin.vocal.xx.us.
    ;
    corelli.sonatas.XVII.it	    IN  A     192.168.16.126         ; IP of name server
    gershwin.vocal                 IN  A     192.168.16.132         ; A-record for gershwin.vocal.xx.us (IP name server vocal.xx.us subdomain)
    barber				         IN  A 	192.168.16.4           ; A-record barber.xx.us
    bernstein 			         IN  A 	192.168.16.17          ; A-record bernstein.xx.us
    cage				           IN  A 	192.168.16.58          ; A-record cage.xx.us
    copland				        IN  A 	192.168.16.73          ; A-record copland.xx.us
    glass				          IN  A 	192.168.16.99          ; A-record glass.xx.us
    carter.orchestral 		     IN  A 	192.168.16.64          ; A-record carter.orchestral.xx.us


**XIX.it.zone**

    ;
    ;       Zone File for "XIX.it" - Internal Use ONLY
    ;                                    
    ; Append XIX.it after every relative domain
    $TTL 60
    @    IN    SOA    corelli.sonatas.XVII.it. xavier.geerinck.ugent.be (
                      1            ; serial
                      1            ; refresh
                      1            ; retry
                      1            ; expire
                      1 )          ; minimum
    ;
         IN    NS    corelli.sonatas.XVII.it.                  ; Authoritative Name Server
    ; delegations
    opera		                  IN  NS   frescobaldi.XVII.it.   ; opera.XIX.it is controlled by frescobaldi.XVII.it.
    ;
    corelli.sonatas.XVII.it	    IN  A    192.168.16.126         ; IP of name server
    frescobaldi.XVII.it.           IN  A    192.168.16.131         ; IP of name server that controls opera.XIX.it.
    ponchielli	                 IN  A	192.168.16.197         ; A-record ponchielli.XIX.it

**oratoria.XVII.it.zone**

    ;
    ;       Zone File for "oratoria.XVII.it" - Internal Use ONLY
    ;                   
    ; Append oratoria.XVII.it after every relative domain
    $TTL 60
    @    IN    SOA    corelli.sonatas.XVII.it. xavier.geerinck.ugent.be (
                      1            ; serial
                      1            ; refresh
                      1            ; retry
                      1            ; expire
                      1 )          ; minimum
    ;
         IN    NS    corelli.sonatas.XVII.it.           ; Authoritative Name Server
    ;
    corelli.sonatas.XVII.it     IN  A   192.168.16.126  ; IP Of name server
    carissimi                   IN  A   192.168.16.60   ; A-record carissimi.oratoria.XVII.it



## Bespreek in detail het begrip secundaire nameserver, inclusief voordelen, beperkingen en problemen. (§3.1 & §3.3.3) 
Secundaire nameservers houden dezelfde informatie bij als primaire

* Moet als type slave gebruikt worden.
* allow-transfer { ip-adressen }; moet gebruikt worden bij options.

Voordelen:
* Worden gebruikt om taak primaire nameserver te verlichten
* Ook om te zorgen voor fouttolerantie in geval dat primaire nameserver uitvalt.

Beperkingen:
* Niet flexibel in gebruik: Wijzigingen enkel op primaire nameserver, secundaire moet dus checken of primaire gewijzigd.

