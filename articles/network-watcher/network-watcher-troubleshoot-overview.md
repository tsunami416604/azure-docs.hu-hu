---
title: Bevezetés az Azure hálózati figyelőt a hibakeresési erőforrás |} Microsoft Docs
description: Ezen a lapon a hálózati figyelőt erőforrás hibaelhárítási képességei áttekintése
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
ms.openlocfilehash: 2f8a41834c1451d80c53cfed4bae3b7e36281702
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Bevezetés az Azure hálózati figyelőt a hibakeresési erőforrás

Virtuális hálózati átjárók biztosít a helyi erőforrások és más Azure-ban virtuális hálózatok közötti kapcsolatot. Átjárók figyelési és a kapcsolatok kulcsfontosságúként kommunikáció biztosításához megszakad nem. Hálózati figyelő lehetővé teszi az átjárók és kapcsolatok hibáinak elhárítása. A funkció a portálra, a PowerShell, az Azure parancssori felület vagy a REST API hívása. Meghívásakor, a hálózati figyelőt diagnosztizálja az átjárót, vagy a kapcsolat állapotát, és a megfelelő eredményeket ad vissza. A kérelem teljesítése hosszú ideig futó tranzakció. A diagnosztikai végrehajtása után a rendszer visszairányítja az eredményeket.

![portal][2]

## <a name="results"></a>Results (Eredmények)

Adott előzetes eredmények osszon ki az erőforrás állapotának átfogó képet. Mélyebb információk biztosítható, hogy az erőforrások a következő szakaszban látható módon:

Az alábbi lista a értékeket ad vissza, amelyben a hibaelhárítás API:

* **startTime** -Ez az érték az az idő a hibaelhárítás API-hívás elindítva.
* **endTime** -Ez az érték az az idő, amikor a hibaelhárítás befejeződött.
* **kód** -ezt az értéket nem kifogástalan állapotú, akkor, ha sikertelen egy egyetlen elemzés céljából.
* **eredmények** -eredményt adja vissza a rendszer a kapcsolatot, vagy a virtuális hálózati átjáró eredmények gyűjteménye.
    * **azonosító** -ezt az értéket a hiba típusa.
    * **összegző** -értéke a tartalék összegzését.
    * **részletes** -ezt az értéket a hiba részletes leírását tartalmazza.
    * **recommendedActions** – Ez a tulajdonság gyűjteményei, javasolt műveleteket kell elvégezni.
      * **actionText** -ezt az értéket tartalmaz a végrehajtandó műveletet leíró szöveg.
      * **actionUri** -Ez az érték szolgál az URI-dokumentáció való működésre.
      * **actionUriText** -Ez az érték a művelet szöveg rövid leírását.

Az alábbi táblázatok bemutatják a különböző tartalék típusok (azonosító: a fenti listából eredmények alapján), és ha a tartalék hoz létre a naplókat.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs telepítve |Nem|
| PlannedMaintenance |  Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Ez a hiba akkor fordul elő, amikor a felhasználó frissítése folyamatban van. A frissítés egy átméretezés lehet. | Nem |
| VipUnResponsive | Ez a hiba akkor fordul elő, ha az elsődleges példány az átjáró egy állapot-mintavételi hiba miatt nem érhető el. | Nem |
| PlatformInActive | A platformon probléma van. | Nem|
| ServiceNotRunning | Az alapul szolgáló szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Kapcsolat nem létezik az átjáróhoz. Ez a hiba csak egy figyelmeztetés.| Nem|
| ConnectionsNotConnected | Kapcsolatok nem kapcsolódnak. Ez a hiba csak egy figyelmeztetés.| Igen|
| GatewayCPUUsageExceeded | Az aktuális átjáró CPU-használat > 95 %. | Igen |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nincs hiba észlelhető |Igen|
| GatewayNotFound | Nem található átjáró vagy az átjáró nincs telepítve |Nem|
| PlannedMaintenance | Átjárópéldány karbantartás alatt áll.  |Nem|
| UserDrivenUpdate | Ez a hiba akkor fordul elő, amikor a felhasználó frissítése folyamatban van. A frissítés egy átméretezés lehet.  | Nem |
| VipUnResponsive | Ez a hiba akkor fordul elő, ha az elsődleges példány az átjáró egy állapot-mintavételi hiba miatt nem érhető el. | Nem |
| ConnectionEntityNotFound | Kapcsolat konfigurációja hiányzik. | Nem |
| ConnectionIsMarkedDisconnected | A kapcsolat "leválasztott" van megjelölve. |Nem|
| ConnectionNotConfiguredOnGateway | Az alapul szolgáló szolgáltatás nincs konfigurálva a kapcsolat. | Igen |
| ConnectionMarkedStandy | Az alapul szolgáló szolgáltatás készenléti jelölésű.| Igen|
| Hitelesítés | Előmegosztott kulcsa eltérő | Igen|
| PeerReachability | A társ-átjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társ átjáró rendelkezik IKE szabályzatok Azure által nem támogatott. | Igen|
| WfpParse hiba | Hiba történt a Windows Fájlvédelem napló elemzésekor. |Igen|

## <a name="supported-gateway-types"></a>Támogatott átjáró típusok

A következő táblázat sorolja fel, melyik átjárót és a kapcsolatok támogatottak a hibaelhárításban hálózati figyelőt:

|  |  |
|---------|---------|
|**Átjáró típusa**   |         |
|VPN      | Támogatott        |
|ExpressRoute | Nem támogatott |
|**VPN-típusai** | |
|Útvonal alapján | Támogatott|
|Csoportházirend-alapú | Nem támogatott|
|**Kapcsolat típusai**||
|IPSec| Támogatott|
|VNet2Vnet| Támogatott|
|ExpressRoute| Nem támogatott|
|VPNClient| Nem támogatott|

## <a name="log-files"></a>Naplófájlok

Az erőforrás hibaelhárítás naplófájljai vannak tárolva egy tárfiókot, erőforrás hibakeresési befejeződése után. A következő kép bemutatja, hibát eredményezett hívás példa tartalmát.

![zip-fájl][1]

> [!NOTE]
> Bizonyos esetekben csak egy részét a naplófájlokat tároló íródik.

A fájlok letöltését az azure storage-fiókok útmutatásért tekintse meg [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz, amely használható a Tártallózó. Tártallózó további információt itt található: a következő hivatkozásra: [Tártallózó](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

A **ConnectionStats.txt** fájl tartalmazza az összesített statisztikák a kapcsolat, beleértve a bemenő és kimenő bájtokat, kapcsolat állapota és az idő, a kapcsolat jött létre.

> [!NOTE]
> A hibaelhárítási API-hívás kifogástalan adja vissza, ha van-e a zip-fájlban szereplő visszaadott egyedül a **ConnectionStats.txt** fájlt.

Ez a fájl tartalma az alábbihoz hasonló:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

A **CPUStats.txt** CPU-használat és a tesztelés során rendelkezésre álló memória-fájl tartalmazza.  Ez a fájl tartalmát a következőhöz hasonló:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

A **IKEErrors.txt** fájl bármely ellenőrzése során talált IKE hibákat tartalmazza.

A következő példa bemutatja egy IKEErrors.txt fájl tartalmát. A hibák eltérhetnek attól függően, hogy a problémát.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

A **Scrubbed-wfpdiag.txt** naplófájl tartalmazza a Windows fájlvédelem naplót. Ez a napló tartalmaz csomagjai és IKE/AuthIP hibák naplózása.

A következő példa bemutatja a Scrubbed-wfpdiag.txt fájl tartalmát. Ebben a példában a kapcsolat megosztott kulcsa nem volt pontosak, mivel a harmadik sorban a lista aljáról is látható. Az alábbi példa esetén csak a teljes napló, a részlet, mert a napló megnőhet, attól függően, hogy a problémát.

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

A **wfpdiag.txt.sum** fájl egy napló, a buffers és a feldolgozott események megjelenítése.

A következő példa egy wfpdiag.txt.sum fájl tartalmának.
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

Átjáró vagy gateway-kapcsolatot a probléma diagnosztizálása érdekében, lásd: [hálózatok közötti kommunikációs problémák diagnosztizálásához](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
