---
title: Hibaelhárítás az Azure Network Watcher erőforrás bemutatása |} A Microsoft Docs
description: Ez az oldal áttekintést a Network Watcher erőforrás hibaelhárítási képességeket kínál
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 8048dde6158d9eaa9bf38a8c3020420b81bdd55b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099778"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Hibaelhárítás az Azure Network Watcher erőforrás bemutatása

Virtuális hálózati átjárók adja meg a helyszíni erőforrásokhoz és más Azure-on belüli virtuális hálózatok közötti kapcsolatot. Az átjárók és azok kapcsolatainak monitorozása kritikus fontosságú a kommunikáció megfelelő működésének biztosításában. A Network Watcher lehetővé teszi az átjárók és kapcsolatok hibaelhárítása. A funkció a portal, PowerShell, az Azure CLI vagy REST API használatával nem hívható meg. Meghívni, Network Watcher diagnosztizálja az átjárót, vagy a kapcsolat állapotát, és a megfelelő eredményeket adja vissza. A kérelem egy hosszú ideig futó tranzakció. Az eredményeket a diagnosztizálás befejeződése után vissza.

![portal][2]

## <a name="results"></a>Results (Eredmények)

Az előzetes eredményeket ad vissza az erőforrás állapotáról átfogó képet biztosítanak. Részletesebb információhoz juthat az erőforrások adható meg a következő szakaszban látható módon:

Az alábbi lista a hibaelhárítás API a visszaadott értékek:

* **startTime** – Ez az érték az az idő, a hibaelhárítás API-hívás elindítva.
* **endTime** – Ez az érték az az idő, amikor a hibaelhárítás befejeződött.
* **kód** – Ez az érték nem megfelelő, akkor, ha a egyetlen diagnosztikai hiba történik.
* **eredmények** -eredmények gyűjteménye, a kapcsolat vagy a virtuális hálózati átjáró a visszaadott eredményeket.
    * **ID** – Ez az érték egy tartalék típus.
    * **összefoglaló** – Ez az érték található egy összefoglaló az a hiba.
    * **részletes** – ezt az értéket nyújt a hiba részletes leírását.
    * **recommendedActions** – Ez a tulajdonság nem javasolt műveleteket gyűjteménye.
      * **actionText** – Ez az érték tartalmazza a végrehajtandó műveletet leíró szöveget.
      * **actionUri** – ezt az értéket az URI-t dokumentációja nyújt az való működésre.
      * **actionUriText** -értéke a művelet szöveg rövid leírását.

Az alábbi táblázatokban a rendelkezésre álló különböző tartalék-típusokat (azonosító alapján az előző listában eredményeket), és a tartalék naplókat hoz létre.

### <a name="gateway"></a>Átjáró

| Hiba típusa | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance |  Átjárópéldány karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor fordul elő, amikor egy felhasználó frissítése folyamatban van. A frissítés egy átméretezési művelet lehet. | Nem |
| VipUnResponsive | Ez a hiba akkor fordul elő, ha az elsődleges példány az átjáró nem érhető el a health mintavételi hiba miatt. | Nem |
| PlatformInActive | Nincs a platform problémáját. | Nem|
| ServiceNotRunning | A mögöttes szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Kapcsolat nem létezik az átjárón. Ez a hiba csak figyelmeztetés.| Nem|
| ConnectionsNotConnected | Kapcsolatok nem csatlakoznak. Ez a hiba csak figyelmeztetés.| Igen|
| GatewayCPUUsageExceeded | A CPU-használat gateway > 95 %-os. | Igen |

### <a name="connection"></a>Kapcsolat

| Hiba típusa | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance | Átjárópéldány karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor fordul elő, amikor egy felhasználó frissítése folyamatban van. A frissítés egy átméretezési művelet lehet.  | Nem |
| VipUnResponsive | Ez a hiba akkor fordul elő, ha az elsődleges példány az átjáró nem érhető el a health mintavételi hiba miatt. | Nem |
| ConnectionEntityNotFound | Kapcsolat konfigurációja hiányzik. | Nem |
| ConnectionIsMarkedDisconnected | A kapcsolat "leválasztott" van megjelölve. |Nem|
| ConnectionNotConfiguredOnGateway | A mögöttes szolgáltatás nem rendelkezik konfigurált kapcsolat. | Igen |
| ConnectionMarkedStandby | A mögöttes szolgáltatás készenléti van megjelölve.| Igen|
| Authentication | Előmegosztott kulcsa eltérő | Igen|
| PeerReachability | A társ-átjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társ-átjáró IKE-szabályzatok, amelyek nem támogatottak az Azure rendelkezik. | Igen|
| WfpParse Error | Hiba történt a Windows Fájlvédelem napló elemzése. |Igen|

## <a name="supported-gateway-types"></a>Támogatott átjárótípusok

A következő táblázat felsorolja, hogy mely átjárók és kapcsolatok támogatottak a Network Watcher troubleshooting:

|  |  |
|---------|---------|
|**Átjárótípusok**   |         |
|VPN      | Támogatott        |
|ExpressRoute | Nem támogatott |
|**VPN-típusok** | |
|Útvonalalapú | Támogatott|
|Csoportházirend-alapú | Nem támogatott|
|**Kapcsolattípusok**||
|IPSec| Támogatott|
|VNet2Vnet| Támogatott|
|ExpressRoute| Nem támogatott|
|VPNClient| Nem támogatott|

## <a name="log-files"></a>Naplófájlok

Az erőforrás hibaelhárítás naplófájljai vannak tárolva egy tárfiókot, erőforrás hibaelhárítás befejezése után. Az alábbi képen látható példában tartalmát a hívás, amely hibát eredményezett.

![zip-fájl][1]

> [!NOTE]
> Bizonyos esetekben csak egy részét a naplófájlok tárolási íródik.

Fájlok letöltése az azure storage-fiókokra vonatkozó utasításokért tekintse meg [.NET használatával az Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz használható a Storage Explorer. További információ a Storage Explorer itt található, a következő hivatkozásra: [Storage Explorer](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

A **ConnectionStats.txt** fájl tartalmazza a kapcsolat, beleértve a bemenő és kimenő bájtokat, kapcsolat állapota és az időt, a kapcsolat létrehozásának módja összesített statisztikák.

> [!NOTE]
> Ha a hibaelhárítási API-hívás megfelelő értéket ad vissza, a zip-fájl által visszaadott egyedül van egy **ConnectionStats.txt** fájlt.

Ez a fájl tartalma a következő példához hasonló:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

A **CPUStats.txt** CPU-használat és a vizsgálat időpontjában elérhető memória-fájl tartalmazza.  Ez a fájl tartalmát a következő az alábbi példához hasonló:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

A **IKEErrors.txt** fájl ellenőrzése során talált IKE hibákat tartalmaz.

Az alábbi példa bemutatja egy IKEErrors.txt fájl tartalmát. A hibát a probléma függően eltérő lehet.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

A **Scrubbed-wfpdiag.txt** naplófájl tartalmazza a Windows fájlvédelem naplót. Ez a napló tartalmaz csomagvesztéseinek és internetes KULCSCSERE/AuthIP hibák naplózása.

Az alábbi példa bemutatja a Scrubbed-wfpdiag.txt fájl tartalmát. Ebben a példában a megosztott kulcsot a kapcsolat nem volt megfelelő, mivel a lista aljáról a harmadik sor is látható. Az alábbi példa a teljes naplót, csupán egy részlet megegyezik a napló lehet hosszabb, függően a problémát.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

A **wfpdiag.txt.sum** fájl egy naplót, a buffers és a feldolgozott események megjelenítése.

Az alábbi példában egy wfpdiag.txt.sum fájl tartalmát.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan-átjáróval vagy átjárókapcsolat problémáinak diagnosztizálása, lásd: [hálózatok közötti kommunikációs probléma diagnosztizálása](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
