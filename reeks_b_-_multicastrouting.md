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
* Forwarding state wordt bijgehouden in **(S,G)** en **(*,G)** (S = unicast IP, G = specifiek multicast group IP adress)

## 2. Omschrijf de 2 fundamenteel verschillende manieren om deze basisprincipes te realiseren, inclusief hun relatieve voor- en nadelen.
* 


## 3. Bespreek in detail de diverse facetten van het momenteel meest gebruikte multicastroutingprotocol.
PIM-SM
## 4. Omschrijf de optionele technieken om de werking van dit protocol nog meer te optimaliseren.
## 5. Hoe kan men een Linux toestel als multicastrouter laten werken? Geef 2 concrete voorbeelden. Geef aan hoe men de diverse multicastverkeerstromen kan opvolgen.
Kernel flags aanzetten:
* IPv4: 
    * CONFIG_IP_MULTICAST=u
    * CONFIG_IP_MROUTE=y
    * CONFIG_IP_PIMSM_V2=y
* IPv6:
    * CONFIG_IPV6_MROUTE=y
    * CONFIG_IPV6_PIMSM_V2=y


## 6. Sommige routers verzorgen in het multicastproces bijzondere rollen, die ze pas na specifieke verkiezingsprocessen toebedeeld krijgen. Geef een overzicht van deze bijzondere functies, en de overeenkomstige verkiezingsprocessen.
