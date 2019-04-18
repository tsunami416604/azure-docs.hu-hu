---
title: Diagnosztizálás használó helyszíni kapcsolatok VPN-átjárót, az Azure Network Watcher |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Network Watcher erőforrás hibaelhárítás VPN-átjárót használó helyszíni kapcsolatok diagnosztizálása érdekében.
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
ms.openlocfilehash: 323e5d63b5f8566d570dfd47323fcf12f7c6b28b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051580"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>VPN-átjárók helyszíni kapcsolatok diagnosztizálása

Az Azure VPN Gateway lehetővé teszi, hogy hozzon létre hibrid megoldás, a címet a biztonságos kapcsolat a helyszíni hálózat és az Azure virtuális hálózat között van szükség. Egyediek-e a követelményeknek, mivel így a választás a helyszíni VPN-eszköz van. Az Azure jelenleg támogatja [több VPN-eszközök](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) , amely folyamatosan ellenőrzi a ellenőriztünk. A helyszíni VPN-eszköz konfigurálása előtt tekintse át az eszközre vonatkozó konfigurációs beállításokat. Hasonló módon van konfigurálva az Azure VPN Gateway vannak beállítva [IPsec paraméterek támogatott](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) használt kapcsolatokat lehessen létesíteni. Jelenleg nincs lehetőség, hogy adja meg vagy válassza ki az IPsec paraméterek adott kombinációinak az Azure VPN Gateway átjárón. Sikeres kapcsolatlétesítés a helyszíni és az Azure között, a helyszíni VPN-eszközbeállításokat megadni az Azure VPN Gateway átjárók által előírt IPsec paraméterek összhangban kell lennie. Ha a beállítások helyesek, ott a megszakad a kapcsolat és az eddig ezen kapcsolatos hibák elhárítása nem triviális és óra azonosíthatja és megoldhatja a problémát általában tartott.

Az Azure Network Watcher szolgáltatással a szolgáltatás hibaelhárítása, az átjáró és kapcsolatok problémák diagnosztizálása és percen belül van, hogy érdemes-e hibanaplóit a probléma megoldásához elég információ áll.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Forgatókönyv

Azure és helyszíni között helyek közötti kapcsolat konfigurálása szeretné FortiGate használja, mint a helyszíni VPN-átjárót. Ebben a forgatókönyvben eléréséhez a következő telepítési kellene:

1. Virtuális hálózati átjáró – a VPN-átjárót, az Azure-ban
1. Helyi hálózati átjáró - a [a helyszíni VPN-átjáró (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) ábrázolása az Azure-felhőben
1. Helyek közötti kapcsolat (útvonalalapú) – [a VPN-átjáró és a helyszíni útválasztót közötti kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [FortiGate konfigurálása](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Részletes részletes útmutató egy helyek közötti konfiguráció funkcionáló találhatók: [Virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure Portalon](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Az egyik fontos konfigurációs lépés konfigurálja az IPSec-kommunikáció paramétereket, a helyszíni hálózat és az Azure közötti kapcsolat megszakadása vezet bármely hibás. 1. fázis a következő IPsec paraméterek támogatása jelenleg az Azure VPN-átjárók vannak konfigurálva. Vegye figyelembe, ahogy korábban említettük, ezek a beállítások nem módosíthatók.  Ahogy az alábbi táblázatban látható, az Azure VPN Gateway átjárók által támogatott titkosítási algoritmusokat AES256, az AES128 és a 3DES.

### <a name="ike-phase-1-setup"></a>Az IKE 1. fázis beállítása

| **Tulajdonság** | **Házirendalapú** | **Útvonalalapú és Standard vagy nagy teljesítményű VPN gateway** |
| --- | --- | --- |
| IKE verziószám |IKEv1 |IKEv2 |
| Diffie-Hellman Group |2. csoport (1024 bites) |2. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs |Előre megosztott kulcs |
| Titkosítási algoritmusok |AES256 AES128 3DES |AES256 3DES |
| Kivonatoló algoritmus |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| 1. fázisú biztonsági társítás (SA) Élettartam (idő) |28 800 másodperc |10 800 másodperc |

Felhasználóként, a FortiGate konfigurálásához szükséges lenne, egy példa konfigurációja találhatók [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Tudtuk nélkül konfigurálta az SHA-512 használata a kivonatoló algoritmusként FortiGate. Mert ez az algoritmus nem egy támogatott algoritmust a csoportházirend-alapú kapcsolatokhoz, a VPN-kapcsolat működik.

Ezek olyan problémák hibaelhárítása nehéz, és általában nem intuitív alapvető okait. Ebben az esetben is egy támogatási jegyet a probléma megoldása segítséget szeretne kérni. Az Azure Network Watcher hibaelhárítása API, azonosíthatja ezeket a problémákat, saját maga.

## <a name="troubleshooting-using-azure-network-watcher"></a>Hibaelhárítás az Azure Network Watcher használatával

A kapcsolat diagnosztizálásához, csatlakozhat az Azure PowerShell-lel, és a a `Start-AzNetworkWatcherResourceTroubleshooting` parancsmagot. Az információk a következő parancsmag használatával [elhárítása virtuális hálózati átjáró és kapcsolatok – PowerShell](network-watcher-troubleshoot-manage-powershell.md). Ez a parancsmag igénybe vehet néhány percet végrehajtásához.

A parancsmag befejeződése után navigálhat a parancsmag részletes információkhoz juthat kapcsolatos a probléma és a naplók a megadott tárolási helyre. Az Azure Network Watcher a következő naplófájlokat tartalmazó zip mappát hoz létre:

![1][1]

Nyissa meg a IKEErrors.txt nevű fájlt, és megjeleníti a következő hiba, a helyszíni problémáját jelzi az IKE-beállítás hibás.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Részletes információkat szerezhet a hibáról, Scrubbed-wfpdiag.txt a, ebben az esetben említi, hogy hiba történt `ERROR_IPSEC_IKE_POLICY_MATCH` érdeklődő kapcsolat nem működik megfelelően.

Egy másik gyakori hiba a konfiguráció a megadásával helytelen megosztott kulcsok. Az előző példában megadott kellett különböző megosztott kulcsokat, ha a IKEErrors.txt jeleníti meg a következő hibával: `Error: Authentication failed. Check shared key`.

Az Azure Network Watcher hibaelhárítása szolgáltatás lehetővé teszi a diagnosztika és hibaelhárítás a VPN Gateway és a kapcsolat egy egyszerű PowerShell-parancsmag az alkalmazásaiba. Jelenleg a Microsoft támogatja a következő feltételek diagnosztizálása, és felé több feltétel hozzáadása dolgozik.

### <a name="gateway"></a>Átjáró

| Hiba típusa | Ok | Napló|
|---|---|---|
| NoFault | Ha nem történt hiba észlelhető. |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance |  Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Ha egy felhasználó frissítése folyamatban van. Ez lehet egy átméretezési művelet. | Nem |
| VipUnResponsive | Az elsődleges példány az átjáró nem érhető el. Ez akkor történik, ha az állapotfigyelő mintavételező nem sikerült. | Nem |
| PlatformInActive | Nincs a platform problémáját. | Nem|
| ServiceNotRunning | A mögöttes szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Az átjáró kapcsolat nem létezik. Ez a figyelmeztetés csak.| Nem|
| ConnectionsNotConnected | A kapcsolatok egyik sem kapcsolódik. Ez a figyelmeztetés csak.| Igen|
| GatewayCPUUsageExceeded | A jelenlegi CPU-használat átjáró használata > 95 %-os. | Igen |

### <a name="connection"></a>Kapcsolat

| Hiba típusa | Ok | Napló|
|---|---|---|
| NoFault | Ha nem történt hiba észlelhető. |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance | Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Ha egy felhasználó frissítése folyamatban van. Ez lehet egy átméretezési művelet.  | Nem |
| VipUnResponsive | Az elsődleges példány az átjáró nem érhető el. Ez történik, ha az állapotadat-mintavétel meghiúsul. | Nem |
| ConnectionEntityNotFound | Kapcsolat konfigurációja hiányzik. | Nem |
| ConnectionIsMarkedDisconnected | A kapcsolat meg van jelölve "leválasztott". |Nem|
| ConnectionNotConfiguredOnGateway | A mögöttes szolgáltatás nem rendelkezik konfigurált kapcsolat. | Igen |
| ConnectionMarkedStandby | A mögöttes szolgáltatás készenléti van megjelölve.| Igen|
| Authentication | Előmegosztott kulcs eltérés. | Igen|
| PeerReachability | A társ-átjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társ-átjáró IKE-szabályzatok, amelyek nem támogatottak az Azure rendelkezik. | Igen|
| WfpParse Error | Hiba történt a Windows Fájlvédelem napló elemzése. |Igen|

## <a name="next-steps"></a>További lépések

Ismerje meg, ellenőrizze a VPN-átjáró kapcsolatot a PowerShell és az Azure Automation funkcionáló [figyelő VPN Gateway-átjárók az Azure Network Watcher hibáinak elhárítása](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
