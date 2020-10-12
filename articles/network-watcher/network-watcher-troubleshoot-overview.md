---
title: Az erőforrás-hibaelhárítás bemutatása
titleSuffix: Azure Network Watcher
description: Ez az oldal áttekintést nyújt a Network Watcher erőforrás-hibaelhárítási képességeiről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 95edcee32c1917d23e4808e805f947d18d2fa7f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986227"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Az erőforrás-hibaelhárítás bemutatása az Azure Network Watcher

Virtual Network átjárók kapcsolatot biztosítanak a helyszíni erőforrások és az Azure-on belüli más virtuális hálózatok között. Az átjárók és azok kapcsolatainak monitorozása kritikus fontosságú a kommunikáció megfelelő működésének biztosításában. A Network Watcher lehetővé teszi az átjárók és a kapcsolatok hibakeresését. A képesség a portál, a PowerShell, az Azure CLI vagy a REST API használatával hívható meg. A híváskor Network Watcher diagnosztizálja az átjáró állapotát vagy a csatlakozást, és a megfelelő eredményeket adja vissza. A kérelem hosszú ideig futó tranzakció. Az eredményeket a rendszer a diagnosztika befejeződése után adja vissza.

![A képernyőképen Network Watcher V P N diagnosztika látható.][2]

## <a name="results"></a>Results (Eredmények)

A visszaadott előzetes eredmények átfogó képet adnak az erőforrás állapotáról. Mélyebb információk is megadhatók az erőforrásokhoz az alábbi szakaszban látható módon:

A következő lista a hibakeresési API-val visszaadott értékeket tartalmazza:

* **kezdő időpont** – ez az érték a hibakeresési API-hívás elindításának időpontja.
* **végső Befejezés** – ez az érték az az idő, amikor a hibaelhárítás befejeződött.
* **kód** – ez az érték nem kifogástalan, ha egyetlen diagnosztikai hiba van.
* **eredmények** – az eredmények a kapcsolatok vagy a virtuális hálózati átjáró által visszaadott eredmények gyűjteményei.
    * **azonosító** – ez az érték a hiba típusa.
    * **Summary (összefoglalás** ) – Ez az érték a hiba összegzése.
    * **részletes** – ez az érték a hiba részletes leírását tartalmazza.
    * **recommendedActions** – ez a tulajdonság a végrehajtandó javasolt műveletek gyűjteménye.
      * **actionText** – ez az érték tartalmazza azt a szöveget, amely leírja, hogy milyen műveletet kell végrehajtania.
      * **actionUri** – ez az érték biztosítja az URI-t a működéséhez szükséges dokumentációhoz.
      * **actionUriText** – ez az érték a művelet szövegének rövid leírása.

Az alábbi táblázatokban láthatók a különböző típusú hibák (azonosítók az előző lista eredményei között), amelyek elérhetők, és ha a hiba naplókat hoz létre.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance |  Az átjáró példánya karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor lép fel, ha felhasználói frissítés van folyamatban. A frissítés lehet átméretezési művelet. | Nem |
| VipUnResponsive | Ez a hiba akkor jelentkezik, ha az átjáró elsődleges példánya az állapotadat-mintavétel hibája miatt nem elérhető. | Nem |
| PlatformInActive | A platformmal kapcsolatos probléma áll fenn. | Nem|
| ServiceNotRunning | A mögöttes szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Nem találhatók kapcsolatok az átjárón. Ez a hiba csak figyelmeztetés.| Nem|
| ConnectionsNotConnected | A kapcsolatok nincsenek csatlakoztatva. Ez a hiba csak figyelmeztetés.| Igen|
| GatewayCPUUsageExceeded | Az átjáró jelenlegi CPU-használata > 95%. | Igen |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance | Az átjáró példánya karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor lép fel, ha felhasználói frissítés van folyamatban. A frissítés lehet átméretezési művelet.  | Nem |
| VipUnResponsive | Ez a hiba akkor jelentkezik, ha az átjáró elsődleges példánya az állapotadat-mintavétel hibája miatt nem elérhető. | Nem |
| ConnectionEntityNotFound | Hiányzik a kapcsolatok konfigurációja | Nem |
| ConnectionIsMarkedDisconnected | A kapcsolat meg van jelölve "leválasztva" |Nem|
| ConnectionNotConfiguredOnGateway | A mögöttes szolgáltatáshoz nincs konfigurálva a hálózat. | Igen |
| ConnectionMarkedStandby | A mögöttes szolgáltatás készenléti állapotban van megjelölve.| Igen|
| Hitelesítés | Nem megfelelő előmegosztott kulcs | Igen|
| PeerReachability | A társ-átjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társ-átjáró olyan IKE-szabályzatokkal rendelkezik, amelyeket az Azure nem támogat. | Igen|
| WfpParse hiba | Hiba történt a WFP-napló elemzése során. |Igen|

## <a name="supported-gateway-types"></a>Támogatott átjárók típusai

A következő táblázat felsorolja, hogy mely átjárók és kapcsolatok támogatottak Network Watcher hibaelhárítás esetén:

| Átjáró vagy a kapcsolatok | Támogatott  |
|---------|---------|
|**Átjárótípusok**   |         |
|VPN      | Támogatott        |
|ExpressRoute | Nem támogatott |
|**VPN-típusok** | |
|Útvonal-alapú | Támogatott|
|Házirend-alapú | Nem támogatott|
|**Kapcsolattípusok**||
|IPsec| Támogatott|
|VNet2Vnet| Támogatott|
|ExpressRoute| Nem támogatott|
|VPNClient| Nem támogatott|

## <a name="log-files"></a>Naplófájlok

Az erőforrás-hibaelhárítási naplófájlok tárolása egy Storage-fiókban történik az erőforrás-hibaelhárítás befejeződése után. Az alábbi képen egy olyan hívás tartalma látható, amely hibát eredményezett.

![zip-fájl][1]

> [!NOTE]
> Bizonyos esetekben a rendszer csak a naplófájlok egy részhalmazát írja a tárolóba.

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért tekintse meg az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-dotnet-how-to-use-blobs.md)című témakört. Egy másik eszköz is használható Storage Explorer. Storage Explorer további információ a következő hivatkozáson található: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

A **ConnectionStats.txt** fájl a kapcsolatok általános statisztikáit tartalmazza, beleértve a bejövő és kimenő bájtokat, a kapcsolatok állapotát és a kapcsolatok létrejöttének időpontját.

> [!NOTE]
> Ha a hibaelhárítási API meghívása kifogástalan állapotot ad vissza, akkor a zip-fájlban szereplő egyetlen dolog **ConnectionStats.txt** fájl.

A fájl tartalma a következő példához hasonló:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

A **CPUStats.txt** fájl a tesztelési időszakban rendelkezésre álló CPU-használatot és memóriát tartalmaz.  A fájl tartalma a következő példához hasonló:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

A **IKEErrors.txt** fájl a figyelés során talált IKE-hibákat tartalmaz.

Az alábbi példában egy IKEErrors.txt fájl tartalma látható. A hibák a probléma függvényében eltérőek lehetnek.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

A **Scrubbed-wfpdiag.txt** naplófájl tartalmazza a WFP-naplót. Ez a napló a csomagok eldobásának és az IKE/AuthIP hibák naplózását tartalmazza.

A következő példa a Scrubbed-wfpdiag.txt fájl tartalmát mutatja. Ebben a példában a kapcsolatok megosztott kulcsa nem megfelelő, mert a harmadik sorból a lent látható. A következő példa csak a teljes napló töredékét adja meg, mert a napló hossza a probléma függvényében lehet.

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

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt. Sum

A **wfpdiag.txt. Sum** fájl egy napló, amely a feldolgozott puffereket és eseményeket mutatja.

A következő példa egy wfpdiag.txt. Sum fájl tartalmát adja meg.
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

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan diagnosztizálhatja az átjáróval vagy átjáróval kapcsolatos problémákat, tekintse meg a [hálózatok közötti kommunikációs problémák diagnosztizálását](diagnose-communication-problem-between-networks.md)ismertető témakört.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
