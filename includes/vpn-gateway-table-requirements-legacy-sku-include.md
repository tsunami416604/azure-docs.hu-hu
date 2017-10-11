A következő táblázat PolicyBased és RouteBased VPN-átjáró rendszerkövetelményeit sorolja fel. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A Klasszikus modell PolicyBased VPN gatewayek ugyanazok, mint statikus átjárók, és útválasztó-alapú gatewayek ugyanazok, mint a dinamikus átjárók.

|  | **PolicyBased alapszintű VPN Gateway** | **RouteBased alapszintű VPN Gateway** | **RouteBased Standard VPN Gateway** | **RouteBased nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Pont-pont kapcsolat (S2S)** |PolicyBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |RouteBased VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** |Nem támogatott |Nem támogatott |Támogatott |Támogatott |

