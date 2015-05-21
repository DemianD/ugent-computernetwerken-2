# Reeks B - Configuratie van DNS servers onder Linux Deel 2
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.

## Bespreek in detail het formaat van een zonebestand en zijn records. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve records en formaten beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.1 & §3.3.4)

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

## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
