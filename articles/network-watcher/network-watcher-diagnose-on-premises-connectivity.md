---
title: A helyszíni kapcsolat diagnosztizálása VPN-átjárón keresztül
titleSuffix: Azure Network Watcher
description: Ez a cikk ismerteti, hogyan diagnosztizálhatja a helyszíni kapcsolatot VPN-átjárón keresztül az Azure Network Watcher erőforrás-hibaelhárítás.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845052"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>A helyszíni kapcsolat diagnosztizálása VPN-átjárókon keresztül

Az Azure VPN Gateway lehetővé teszi, hogy hibrid megoldást hozzon létre, amely a helyszíni hálózat és az Azure virtuális hálózat a biztonságos kapcsolat szükségességét elégítheti ki. Mivel a követelmények egyediek, így a helyszíni VPN-eszköz kiválasztása is. Az Azure jelenleg [számos VPN-eszközt](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) támogat, amelyeket folyamatosan érvényesítaz eszközgyártókkal együttműködve. A helyszíni VPN-eszköz konfigurálása előtt tekintse át az eszközspecifikus konfigurációs beállításokat. Hasonlóképpen az Azure VPN-átjáró van konfigurálva egy [sor támogatott IPsec-paraméterek,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) amelyek kapcsolatok létrehozásához használt. Jelenleg nincs mód az IPsec-paraméterek adott kombinációjának megadására vagy kiválasztására az Azure VPN-átjáróból. A helyszíni és az Azure közötti sikeres kapcsolat létrehozásához a helyszíni VPN-eszköz beállításainak összhangban kell lenniük az Azure VPN-átjáró által előírt IPsec-paraméterekkel. Ha a beállítások helytelenek, a kapcsolat megszakadt, és a problémák elhárítása eddig nem volt triviális, és általában órákba telt a probléma azonosítása és megoldása.

Az Azure Network Watcher hibaelhárítási funkciójával diagnosztizálhatja az átjáróval és a kapcsolatokkal kapcsolatos problémákat, és perceken belül elegendő információval rendelkezik ahhoz, hogy megalapozott döntést hozzon a probléma orvoslására.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Forgatókönyv

Az Azure és a helyszíni kapcsolatok között a FortiGate-et helyi VPN-átjáróként használva szeretne konfigurálni. A forgatókönyv eléréséhez a következő beállításra van szükség:

1. Virtuális hálózati átjáró – VPN-átjáró az Azure-ban
1. Helyi hálózati átjáró – A [helyszíni (FortiGate) VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) ábrázolása az Azure-felhőben
1. Helyek közötti kapcsolat (útvonalalapú) – [Kapcsolat a VPN-átjáró és a helyszíni útválasztó között](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [A FortiGate konfigurálása](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Részletes, részletes útmutatást talál a helyek közötti konfiguráció konfigurálásához a következő címen: [Virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure Portal használatával.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Az egyik kritikus konfigurációs lépés az IPsec kommunikációs paraméterek konfigurálása, a helytelen konfiguráció a helyszíni hálózat és az Azure közötti kapcsolat elvesztéséhez vezet. Jelenleg az Azure VPN-átjárók úgy vannak konfigurálva, hogy az 1. Amint az alábbi táblázatban látható, az Azure VPN Gateway által támogatott titkosítási algoritmusok az AES256, az AES128 és a 3DES.

### <a name="ike-phase-1-setup"></a>IkE 1.

| **Tulajdonság** | **Házirendalapú** | **RouteAlapú és szabványos vagy nagy teljesítményű VPN-átjáró** |
| --- | --- | --- |
| IKE verziószám |IKEv1 |IKEv2 |
| Diffie-Hellman Group |2. csoport (1024 bites) |2. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs |Előre megosztott kulcs |
| Titkosítási algoritmusok |AES256 AES128 3DES |AES256 3DES |
| Kivonatoló algoritmus |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| 1. fázisú biztonsági társítás (SA) Élettartam (idő) |28 800 másodperc |28 800 másodperc |

Felhasználóként konfigurálnia kell a FortiGate-et, a [GitHubon](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt)mintakonfiguráció található. Tudtán kívül úgy állította be a FortiGate-et, hogy az SHA-512-t használja kivonatoló algoritmusként. Mivel ez az algoritmus nem támogatott algoritmus a házirend-alapú kapcsolatokhoz, a VPN-kapcsolat működik.

Ezeket a problémákat nehéz elhárítani, és a kiváltó okok gyakran nem intuitívak. Ebben az esetben megnyithat egy támogatási jegyet, hogy segítséget kapjon a probléma megoldásához. Az Azure Network Watcher hibaelhárítási API-jával azonban saját maga azonosíthatja ezeket a problémákat.

## <a name="troubleshooting-using-azure-network-watcher"></a>Hibaelhárítás az Azure Network Watcher használatával

A kapcsolat diagnosztizálásához csatlakozzon az Azure `Start-AzNetworkWatcherResourceTroubleshooting` PowerShellhez, és indítsa el a parancsmast. A parancsmag használatával kapcsolatos részleteket a [Virtuális hálózati átjáró és kapcsolatok – PowerShell hibaelhárítása](network-watcher-troubleshoot-manage-powershell.md)című témakörben találja. Ez a parancsmag akár néhány percet is igénybe vehet.

Miután a parancsmag befejeződött, a parancsmagban megadott tárolási helyre navigálhat, hogy részletes információkat kapjon a problémáról és a naplókról. Az Azure Network Watcher létrehoz egy zip mappát, amely a következő naplófájlokat tartalmazza:

![1][1]

Nyissa meg az IKEErrors.txt nevű fájlt, és a következő hibaüzenetjelenik meg, jelezve a helyszíni ike beállítás helytelen beállításának hibáját.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Részletes információkat kaphat a Scrubbed-wfpdiag.txt-től a hibáról, mivel ebben `ERROR_IPSEC_IKE_POLICY_MATCH` az esetben megemlíti, hogy a kapcsolat nem működik megfelelően.

Egy másik gyakori helytelen konfiguráció a helytelen megosztott kulcsok megadása. Ha az előző példában különböző megosztott kulcsokat adott meg, az IKEErrors.txt fájl a következő hibát jeleníti meg: `Error: Authentication failed. Check shared key`.

Az Azure Network Watcher hibaelhárítási funkció lehetővé teszi a VPN-átjáró és a kapcsolat egyszerű diagnosztizálását és hibaelhárítását egy egyszerű PowerShell-parancsmag egyszerűségével. Jelenleg támogatjuk diagnosztizálása a következő feltételeket, és azon dolgozik, hogy adjunk több feltételt.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba. |Igen|
| GatewayNotFound | Nem található az átjáró, vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance |  Az átjárópéldány karbantartása van folyamatban.  |Nem|
| UserDrivenUpdate | Amikor egy felhasználói frissítés folyamatban van. Ez lehet egy átméretezési művelet. | Nem |
| VipUnResponsive | Nem érhető el az átjáró elsődleges példánya. Ez akkor fordul elő, ha az állapotminta meghibásodik. | Nem |
| PlatformInActive | A platformmal kapcsolatos probléma áll fenn. | Nem|
| ServiceNotRunning szolgáltatás | Az alapul szolgáló szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Nincs kapcsolat az átjárón. Ez csak egy figyelmeztetés.| Nem|
| KapcsolatokNincs csatlakoztatva | Egyik kapcsolat sincs csatlakoztatva. Ez csak egy figyelmeztetés.| Igen|
| GatewayCPUUsageExceedt túllépve | A jelenlegi átjáró használat CPU-használat > 95%. | Igen |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba. |Igen|
| GatewayNotFound | Nem található az átjáró, vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance | Az átjárópéldány karbantartása van folyamatban.  |Nem|
| UserDrivenUpdate | Amikor egy felhasználói frissítés folyamatban van. Ez lehet egy átméretezési művelet.  | Nem |
| VipUnResponsive | Nem érhető el az átjáró elsődleges példánya. Ez akkor történik, ha az egészségügyi szonda meghibásodik. | Nem |
| A connectionentitynem található | Hiányzik a kapcsolat konfigurációja. | Nem |
| ConnectionIsMarkedLeed | A kapcsolat "leválasztva" jelöléssel van ellátva. |Nem|
| ConnectionNotConfiguredOnGateway | Az alapul szolgáló szolgáltatás nem rendelkezik a kapcsolat konfigurálva. | Igen |
| ConnectionMarkedStandby | Az alapul szolgáló szolgáltatás készenléti állapotként van megjelölve.| Igen|
| Hitelesítés | Az előmegosztott kulcs nem egyezik. | Igen|
| Társelérés | A társátjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társátjáró olyan IKE-szabályzatokkal rendelkezik, amelyeket az Azure nem támogat. | Igen|
| WfpParse-hiba | Hiba történt a WfP-napló elemzésével. |Igen|

## <a name="next-steps"></a>További lépések

Ismerje meg a VPN-átjárók és a PowerShell és az Azure Automation közötti kapcsolat ellenőrzését az [Azure Network Watcher hibaelhárításával kapcsolatos VPN-átjárók figyelése ellátogatva](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
