Az alábbi tábla a házirendalapú és az útválasztó-alapú VPN Gatewayek követelményeit. Ez a tábla a Resource Managerrel végzett és a klasszikus üzembe helyezési modellre is érvényes. A klasszikus modell esetében a házirendalapú VPN Gatewayek ugyanazok, mint a statikus átjárók, az útválasztó-alapú átjárók pedig ugyanazok, mint a dinamikus átjárók.


|   | **Házirendalapú alapszintű VPN Gateway** | **Útválasztó-alapú alapszintű VPN Gateway** | **Útválasztó-alapú standard VPN Gateway**   | **Útválasztó-alapú nagy teljesítményű VPN Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Helyek közötti kapcsolat   (S2S)**  | Házirendalapú VPN-konfiguráció        | Útválasztó-alapú VPN-konfiguráció  | Útválasztó-alapú VPN-konfiguráció     | Útválasztó-alapú VPN-konfiguráció    |
| **Pont–hely típusú kapcsolat (P2S**)      | Nem támogatott   | Támogatott (párhuzamosan használható az S2S mellett)  | Támogatott (párhuzamosan használható az S2S mellett)  | Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer**                 |    Előre megosztott kulcs  | Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz | Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz | Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma**       | 1                              | 10                                                                    | 10                                | 30                               |
| **P2S-kapcsolatok maximális száma**       | Nem támogatott                  | 128                                                                   | 128                               | 128                              |
|**Aktív útválasztás-támogatás (BGP)**           | Nem támogatott                  | Nem támogatott                                                         | Nem támogatott                     | Nem támogatott                    |
 


<!--HONumber=Jun16_HO2-->


