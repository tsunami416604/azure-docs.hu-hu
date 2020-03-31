---
title: Az erőforrásokkal kapcsolatos hibaelhárítás – bevezetés
titleSuffix: Azure Network Watcher
description: Ez a lap áttekintést nyújt a Hálózatfigyelő erőforrás-hibaelhárítási lehetőségeiről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840689"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Az Azure Network Watcher erőforrás-hibaelhárításának bemutatása

A virtuális hálózati átjárók kapcsolatot biztosítanak a helyszíni erőforrások és az Azure-on belüli más virtuális hálózatok között. Az átjárók és azok kapcsolatainak monitorozása kritikus fontosságú a kommunikáció megfelelő működésének biztosításában. A Network Watcher lehetővé teszi az átjárók és kapcsolatok hibaelhárítását. A funkció a portálon, a PowerShellen, az Azure CLI-n vagy a REST API-n keresztül hívható meg. A hálózatfigyelő a rendszer megnevezése után diagnosztizálja az átjáró vagy a kapcsolat állapotát, és visszaadja a megfelelő eredményeket. A kérelem egy hosszú ideig futó tranzakció. Az eredményeket a diagnózis befejezése után adja vissza a rendszer.

![portál][2]

## <a name="results"></a>Results (Eredmények)

A visszaadott előzetes eredmények átfogó képet adnak az erőforrás állapotáról. Az erőforrásokról a következő szakaszban látható mélyebb információk is rendelkezésre állnak:

Az alábbi lista az API hibaelhárítási api-val visszaadott értékeket tartalmazza:

* **startTime** – Ez az érték az API-hívás hibaelhárításának időpontja.
* **endTime** - Ez az érték az az időpont, amikor a hibaelhárítás véget ért.
* **code** - Ez az érték nem kifogástalan, ha egyetlen diagnosztikai hiba van.
* **results** - Az eredmények a kapcsolaton vagy a virtuális hálózati átjárón visszaadott eredmények gyűjteménye.
    * **id** - Ez az érték a hiba típusa.
    * **summary** - Ez az érték a hiba összegzése.
    * **részletes** - Ez az érték a hiba részletes leírását tartalmazza.
    * **recommendedActions** – Ez a tulajdonság az ajánlott műveletek gyűjteménye.
      * **actionText** - Ez az érték a végrehajtandó műveletet leíró szöveget tartalmazza.
      * **actionUri** – Ez az érték biztosítja az URI-t a működésre vonatkozó dokumentációhoz.
      * **actionUriText** - Ez az érték a műveletszöveg rövid leírása.

Az alábbi táblázatok az elérhető különböző hibatípusokat (az előző lista eredményei alatti azonosítót) mutatják be, és ha a hiba naplókat hoz létre.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelő hiba |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance |  Az átjárópéldány karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor lép fel, ha felhasználói frissítés van folyamatban. A frissítés lehet átméretezési művelet. | Nem |
| VipUnResponsive | Ez a hiba akkor jelentkezik, ha az átjáró elsődleges példánya az állapotadat-mintavétel hibája miatt nem elérhető. | Nem |
| PlatformInActive | A platformmal kapcsolatos probléma áll fenn. | Nem|
| ServiceNotRunning szolgáltatás | Az alapul szolgáló szolgáltatás nem fut. | Nem|
| NoConnectionsFoundForGateway | Nincs kapcsolat az átjárón. Ez a hiba csak figyelmeztetés.| Nem|
| KapcsolatokNincs csatlakoztatva | A kapcsolatok nincsenek csatlakoztatva. Ez a hiba csak figyelmeztetés.| Igen|
| GatewayCPUUsageExceedt túllépve | A jelenlegi átjáró CPU-használat > 95%. | Igen |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelő hiba |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve |Nem|
| PlannedMaintenance | Az átjárópéldány karbantartás alatt áll  |Nem|
| UserDrivenUpdate | Ez a hiba akkor lép fel, ha felhasználói frissítés van folyamatban. A frissítés lehet átméretezési művelet.  | Nem |
| VipUnResponsive | Ez a hiba akkor jelentkezik, ha az átjáró elsődleges példánya az állapotadat-mintavétel hibája miatt nem elérhető. | Nem |
| A connectionentitynem található | Hiányzik a kapcsolat konfigurációja | Nem |
| ConnectionIsMarkedLeed | A kapcsolat "leválasztva" jelöléssel van ellátva |Nem|
| ConnectionNotConfiguredOnGateway | Az alapul szolgáló szolgáltatás nem rendelkezik a kapcsolat konfigurálva. | Igen |
| ConnectionMarkedStandby | Az alapul szolgáló szolgáltatás készenléti állapotként van megjelölve.| Igen|
| Hitelesítés | Előmegosztott kulcs eltérése | Igen|
| Társelérés | A társátjáró nem érhető el. | Igen|
| IkePolicyMismatch | A társátjáró olyan IKE-szabályzatokkal rendelkezik, amelyeket az Azure nem támogat. | Igen|
| WfpParse-hiba | Hiba történt a WfP-napló elemzésével. |Igen|

## <a name="supported-gateway-types"></a>Támogatott átjárótípusok

Az alábbi táblázat felsorolja, hogy mely átjárókés kapcsolatok támogatottak a Hálózatfigyelő hibaelhárításával:

|  |  |
|---------|---------|
|**Átjárótípusok**   |         |
|VPN      | Támogatott        |
|ExpressRoute | Nem támogatott |
|**VPN-típusok** | |
|Útvonal alapú | Támogatott|
|Házirend-alapú | Nem támogatott|
|**Kapcsolattípusok**||
|IPsec| Támogatott|
|VNet2Vnet| Támogatott|
|ExpressRoute| Nem támogatott|
|VPNClient| Nem támogatott|

## <a name="log-files"></a>Naplófájlok

Az erőforrás hibaelhárítási naplófájljait az erőforrás-hibaelhárítás befejezése után egy tárfiók tárolja. Az alábbi képen egy hiba miatt bekövetkező hívás példatartalma látható.

![zip fájl][1]

> [!NOTE]
> Bizonyos esetekben a naplófájloknak csak egy részhalmaza kerül a tárolóba.

A fájlok Azure storage-fiókokból való letöltésével kapcsolatos tudnivalókat az [Azure Blob storage .NET használatával történő használatának első lépései .](../storage/blobs/storage-dotnet-how-to-use-blobs.md) Egy másik eszköz, amely használható a Storage Explorer. További információ a Storage Explorer ről itt található az alábbi linken: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt fájl

A **ConnectionStats.txt** fájl tartalmazza a kapcsolat általános statisztikáit, beleértve a be- és kimenő bájtokat, a kapcsolat állapotát és a kapcsolat létrehozásának idejét.

> [!NOTE]
> Ha a hibaelhárítási API-hoz intézett hívás kifogástalan állapotú, a zip-fájlban csak egy **ConnectionStats.txt** fájlt adott vissza.

A fájl tartalma hasonló a következő példához:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

A **CPUStats.txt** fájl tartalmazza a CPU-használat és a rendelkezésre álló memória idején a vizsgálat.  A fájl tartalma hasonló a következő példához:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>Internetes kulcshibák.txt

Az **IKEErrors.txt** fájl tartalmazza a figyelés során talált IKE-hibákat.

A következő példa egy IKEErrors.txt fájl tartalmát mutatja be. A hibák a problémától függően eltérőek lehetnek.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Súrolt-wfpdiag.txt

A **Scrubbed-wfpdiag.txt** naplófájl tartalmazza a wfp naplót. Ez a napló a csomagátadási és az IKE/AuthIP hibák naplózását tartalmazza.

A következő példa a Scrubbed-wfpdiag.txt fájl tartalmát mutatja be. Ebben a példában a kapcsolat megosztott kulcsa nem volt helyes, ahogy az alulról a harmadik sorból látható. A következő példa csak egy részlet a teljes napló, mivel a napló lehet hosszadalmas attól függően, hogy a probléma.

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

A **wfpdiag.txt.sum** fájl a feldolgozott puffereket és eseményeket megjelenítő napló.

A következő példa a wfpdiag.txt.sum fájl tartalma.
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

Ha tudni szeretné, hogyan diagnosztizálhat problémát átjáróval vagy átjárókapcsolattal, olvassa el [a Hálózatok közötti kommunikációs problémák diagnosztizálása](diagnose-communication-problem-between-networks.md)című témakört.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
