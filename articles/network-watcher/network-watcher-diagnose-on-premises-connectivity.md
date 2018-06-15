---
title: Az Azure hálózati figyelőt VPN-átjárón keresztül kapcsolatot nyújthassanak derítse |} Microsoft Docs
description: A cikkből megtudhatja, hogyan diagnosztizálhatja a helyi kapcsolat Azure hálózati figyelőt erőforrás hibaelhárítás VPN-átjárón keresztül.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e51d31035a8b05238ef0f8d13dd6b6c3f9ad02e8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
ms.locfileid: "26374202"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>A helyi kapcsolat keresztül VPN-átjárók diagnosztizálása

Az Azure VPN Gateway lehetővé teszi a hibrid megoldás, amely a tárhelyfelhasználás a helyszíni hálózat és az Azure virtuális hálózat között a biztonságos kapcsolat létrehozásához. Mivel a követelmények egyedi, ezért nem a választott helyszíni VPN-eszköz. Jelenleg csak az Azure támogatja [több VPN-eszközök](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) az eszközforgalmazók együttműködve folyamatosan érvényesíti. A helyszíni VPN-eszköz konfigurálása előtt tekintse át az eszközre vonatkozó konfigurációs beállításokat. Hasonló módon van konfigurálva az Azure VPN Gateway vannak beállítva [IPsec paraméterek támogatott](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) kapcsolatok létrehozásához használt. Jelenleg nincs mód, hogy adja meg vagy válassza ki az Azure VPN Gateway IPsec-paraméterek megadott kombinációját. A sikeres kapcsolat a helyszíni és az Azure közötti létrehozásához, a helyszíni VPN-eszköz beállításait meg kell felelnie az Azure VPN-átjáró által előírt IPSec-paramétereket. Ha a beállítások megfelelő, ott van egy, a kapcsolat megszakadása és eddig a problémák hibaelhárítása nem trivial és általában tartott óra azonosíthatja és megoldhatja a problémát.

Az Azure hálózati figyelőt szolgáltatás elhárításával kapcsolatos tudnivalókat lehet bármely, az átjáró és a kapcsolatok problémák elemzéséhez és percen belül rendelkezik elég információval a probléma kijavításához tájékozott döntést.

## <a name="scenario"></a>Forgatókönyv

A pont-pont kapcsolatot az Azure és a helyszíni konfigurálni szeretné a helyszíni VPN-átjáróként FortiGate használatával. Ebben a forgatókönyvben eléréséhez a következő telepítő igényelnének:

1. Virtuális hálózati átjáró – az Azure VPN-átjáró
1. Helyi hálózati átjáró - a [helyszíni VPN-átjáró (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) Azure felhőben megjelenítésre
1. Webhelyek közötti kapcsolattal (útvonal alapján) - [a VPN-átjáró és a helyszíni útválasztót közötti kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [FortiGate konfigurálása](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Részletes útmutatást a pont-pont konfiguráció konfigurálásához található ellátogatva: [hozhat létre egy Vnetet az Azure portál használatával webhelyek kapcsolattal rendelkező](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Az egyik fontos konfigurációs lépés konfigurálását végzi az IPsec-kommunikáció paraméterek, a kapcsolat megszakadása a helyszíni hálózat és az Azure közötti vezet a helytelen konfiguráció. A következő IPSec-paramétereket támogatja az 1. szakasza jelenleg Azure VPN Gatewayek vannak konfigurálva. Megjegyzés: a korábban említett, ezek a beállítások nem módosíthatók.  Az alábbi táblázatban látható, az Azure VPN-átjáró által támogatott titkosítási algoritmusok rendszer AES256 AES128 és a 3DES.

### <a name="ike-phase-1-setup"></a>IKE-1. fázis beállítása

| **Tulajdonság** | **Házirendalapú** | **RouteBased és Standard vagy nagy teljesítményű VPN gateway** |
| --- | --- | --- |
| IKE verziószám |IKEv1 |IKEv2 |
| Diffie-Hellman Group |2. csoport (1024 bites) |2. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs |Előre megosztott kulcs |
| Titkosítási algoritmusok |AES256 AES128 3DES |AES256 3DES |
| Kivonatoló algoritmus |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| 1. fázisú biztonsági társítás (SA) Élettartam (idő) |28 800 másodperc |10 800 másodperc |

Egy felhasználó nevében, lehetővé válik a FortiGate konfigurálásához szükséges, itt talál egy minta konfigurációs [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Tudtukon konfigurálta a FortiGate a kivonatoló algoritmus az SHA-512 használandó. Ez az algoritmus nincs a támogatott algoritmus a csoportházirend-alapú kapcsolatokhoz, a VPN-kapcsolat működjön.

Ezek a problémák hibaelhárítása nehéz és alapvető okok gyakran nem intuitív. Ebben az esetben nyithatja meg a probléma megoldását súgó támogatási jegy. De Azure hálózati figyelőt az API elhárításával kapcsolatos tudnivalókat azonosíthatja ezeket a problémákat, saját maga.

## <a name="troubleshooting-using-azure-network-watcher"></a>Hibaelhárítás segítségével Azure hálózati figyelőt

Diagnosztizálhatja a kapcsolatot, csatlakozzon az Azure PowerShell és kezdeményezni a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmag. Ez a következő parancsmag használatával részletei [hibáinak elhárítása a virtuális hálózati átjáró és a kapcsolatok - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Ez a parancsmag is tarthat néhány perc múlva befejezéséhez.

A parancsmag után léphet a parancsmag a problémát, és a naplók kapcsolatos részletes információkat szerezni a megadott tárolási helyét. Az Azure hálózati figyelőt a következő naplófájlokat tartalmazó zip mappát hoz létre:

![1][1]

Nyissa meg a IKEErrors.txt nevű fájlt, és a hiba, amely jelzi, a helyszíni kapcsolatos problémát jelez IKE beállítás helytelen konfiguráció.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Letölthető részletes információk a Scrubbed-wfpdiag.txt a hibával kapcsolatban, ebben az esetben az akkor említi, hogy nincs `ERROR_IPSEC_IKE_POLICY_MATCH` , előfordulhat, hogy a kapcsolat nem működik megfelelően.

Egy másik gyakori hiba a konfiguráció a megadásával helytelen megosztott kulcsok. Ha az előző példában különböző megosztott kulccsal volt megadva, a IKEErrors.txt jeleníti meg a következő hibával: `Error: Authentication failed. Check shared key`.

Az Azure hálózati figyelőt hibaelhárítása funkciója lehetővé teszi diagnosztizálása és megoldása a VPN-átjáró és a kapcsolat a egyszerű PowerShell parancsmag a könnyű. Jelenleg a Microsoft támogatja a következő feltételek diagnosztizálni, és kifizetni a további feltétel hozzáadása dolgozik.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető. |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nem lett beállítva. |Nem|
| PlannedMaintenance |  Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Amikor a felhasználó frissítése folyamatban van. Ennek oka lehet egy átméretezés. | Nem |
| VipUnResponsive | Az elsődleges példány az átjáró nem érhető el. Ez akkor fordul elő, amikor a állapotmintáihoz sikertelen. | Nem |
| PlatformInActive | A platformon probléma van. | Nem|
| ServiceNotRunning | Az alapul szolgáló szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Kapcsolat nem létezik az átjáróhoz. Ez a figyelmeztetés csak.| Nem|
| ConnectionsNotConnected | A kapcsolatok egyike csatlakoznak. Ez a figyelmeztetés csak.| Igen|
| GatewayCPUUsageExceeded | Az aktuális átjáró használati CPU-használat > 95 %. | Igen |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető. |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nem lett beállítva. |Nem|
| PlannedMaintenance | Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Amikor a felhasználó frissítése folyamatban van. Ennek oka lehet egy átméretezés.  | Nem |
| VipUnResponsive | Az elsődleges példány az átjáró nem érhető el. Akkor történik, ha a állapotmintáihoz sikertelen lesz. | Nem |
| ConnectionEntityNotFound | Kapcsolat konfigurációja hiányzik. | Nem |
| ConnectionIsMarkedDisconnected | A kapcsolat "leválasztott" van megjelölve. |Nem|
| ConnectionNotConfiguredOnGateway | Az alapul szolgáló szolgáltatás nincs konfigurálva a kapcsolat. | Igen |
| ConnectionMarkedStandy | Az alapul szolgáló szolgáltatás készenléti jelölésű.| Igen|
| Authentication | Előmegosztott kulcs nem megfelelő. | Igen|
| PeerReachability | A társ-átjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társ átjáró rendelkezik IKE szabályzatok Azure által nem támogatott. | Igen|
| WfpParse hiba | Hiba történt a Windows Fájlvédelem napló elemzésekor. |Igen|

## <a name="next-steps"></a>Következő lépések

Ismerje meg, a PowerShell és az Azure Automation VPN Gateway-kapcsolat ellenőrzése a ellátogatva [figyelő VPN-átjárók Azure hálózati figyelőt hibaelhárítás](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
