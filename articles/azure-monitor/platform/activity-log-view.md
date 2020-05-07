---
title: Azure-Tevékenységnaplók eseményeinek megtekintése Azure Monitor
description: Megtekintheti az Azure-beli tevékenység naplóját Azure Monitor és beolvashatja a PowerShell, a CLI és a REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 4ea29888d4dcf589e3e5d4dfe594f5f4bff2287e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559971"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése

Az [Azure-tevékenység naplója](platform-logs-overview.md) betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Ez a cikk a tevékenységek naplózási eseményeinek megtekintésére és beolvasására szolgáló különböző módszerekkel kapcsolatos információkat tartalmazza.

## <a name="azure-portal"></a>Azure Portal
A Azure Portal **figyelő** menüjében tekintse meg a tevékenység naplóját az összes erőforráshoz. Egy adott erőforráshoz tartozó tevékenység naplójának megtekintése az erőforrás menüjének **tevékenység napló** lehetőségével.

![Műveletnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

A tevékenységek naplózási eseményeit a következő mezők alapján szűrheti:

* **TimeSpan**: az események kezdési és befejezési időpontja.
* **Kategória**: az esemény kategóriája, a [tevékenység naplójának kategóriák részében](activity-log-view.md#categories-in-the-activity-log)leírtak szerint.
* **Előfizetés**: egy vagy több Azure-előfizetés neve.
* **Erőforráscsoport**: egy vagy több erőforráscsoport a kijelölt előfizetéseken belül.
* **Erőforrás (név)**: – egy adott erőforrás neve.
* **Erőforrás típusa**: az erőforrás típusa, például _Microsoft. számítási/virtualmachines_.
* **Művelet neve** – egy Azure Resource Manager művelet neve, például _Microsoft. SQL/Servers/Write_.
* **Súlyosság**: az esemény súlyossági szintje. Az elérhető értékek: _tájékoztató_, _Figyelmeztetés_, _hiba_, _kritikus_.
* **Esemény kezdeményezője**: a műveletet végrehajtó felhasználó.
* **Nyissa meg a keresést**: Nyissa meg a szöveges keresőmezőt, amely az összes esemény összes mezőjében megkeresi a karakterláncot.

## <a name="categories-in-the-activity-log"></a>Kategóriák a tevékenység naplójában
A tevékenység naplójának minden eseménye egy adott kategóriával rendelkezik, amelyet az alábbi táblázat ismertet. A kategóriák sémák kapcsolatos részletes információkért lásd: [Azure Activity napló esemény sémája](activity-log-schema.md). 

| Kategória | Leírás |
|:---|:---|
| Adminisztratív | A Resource Manageren keresztül végrehajtott összes létrehozási, frissítési, törlési és műveleti művelet rekordját tartalmazza. Ilyenek például a _virtuális gépek létrehozása_ és a _hálózati biztonsági csoport törlése_.<br><br>Egy felhasználó vagy alkalmazás által a Resource Managerrel végrehajtott összes művelet egy adott erőforrástípus műveletének megfelelően van modellezve. Ha a művelet típusa _írás_, _Törlés_vagy _művelet_, akkor a művelet kezdési és sikerességi rekordjait is rögzíti a rendszer a felügyeleti kategóriában. A rendszergazdai események az előfizetés szerepköralapú hozzáférés-vezérlésének változásait is tartalmazzák. |
| Service Health | Az Azure-ban történt összes szolgáltatás-egészségügyi incidens rekordját tartalmazza. Az _USA keleti régiójában SQL Azure Service Health eseménynek például állásidőt tapasztalhat_. <br><br>Az események Service Health hat fajta: _beavatkozás szükséges_, _támogatott helyreállítás_, _incidens_, _karbantartás_, _információ_vagy _Biztonság_. Ezek az események csak akkor jönnek létre, ha van olyan erőforrása az előfizetésben, amelyet az esemény érint.
| Resource Health | Az Azure-erőforrásokra vonatkozó összes erőforrás-állapottal kapcsolatos esemény rekordját tartalmazza. Resource Health eseményre például a _virtuális gép állapota nem érhető el értékre módosult_.<br><br>Resource Health események a négy állapot egyikét jelezhetik: _elérhető_, nem _elérhető_, _csökkentett teljesítményű_és _ismeretlen_. Emellett Resource Health eseményeket úgy is kategorizálhatja, hogy _platform kezdeményezett_ vagy _felhasználó által kezdeményezett_. |
| Riasztás | Az Azure-riasztások aktiválási rekordját tartalmazza. Egy riasztási esemény például a _MyVM CPU%-a az elmúlt 5 percben 80_.|
| Automatikus méretezés | Az adott előfizetésben definiált bármely, az autoskálázási motor működésével kapcsolatos események rekordját tartalmazza. Az autoskálázási eseményre például _nem sikerült a vertikális Felskálázási művelet_. |
| Ajánlás | A Azure Advisor ajánlásainak eseményeit tartalmazza. |
| Biztonság | A Azure Security Center által generált riasztások rekordját tartalmazza. A biztonsági eseményekre példaként a rendszer _gyanús kettős kiterjesztésű fájlt futtat_. |
| Szabályzat | A Azure Policy által végrehajtott összes hatás művelet műveleteit tartalmazza. Példák a házirendi eseményekre: _naplózás_ és _Megtagadás_. A házirend által végrehajtott összes művelet az erőforráson végzett műveletként van modellezve. |

## <a name="view-change-history"></a>Változási előzmények megtekintése

A tevékenység naplójának áttekintésekor a művelet segíthet megtekinteni, hogy milyen változások történtek az adott esemény ideje alatt. Ezeket az információkat megtekintheti a **változások előzményeivel**. Válasszon ki egy olyan eseményt a tevékenységi naplóból, amelyet mélyebbre szeretne keresni. Az eseményhez kapcsolódó módosítások megtekintéséhez válassza a **change History (előzetes verzió)** lapot.

![Eseményhez tartozó előzmények listájának módosítása](media/activity-logs-overview/change-history-event.png)

Ha az eseményhez kapcsolódó változások vannak, akkor a kiválasztott módosítások listája látható. Ekkor megnyílik a **change History (előzetes verzió)** oldal. Ezen a lapon láthatja az erőforrás módosításait. Ahogy az alábbi példában is látható, láthatjuk, hogy a virtuális gép mérete nem változik, de a virtuális gép korábbi mérete a változás előtt volt, és a módosult.

![Változások az előzmények között oldalon látható különbségek](media/activity-logs-overview/change-history-event-details.png)

További információ a változási előzményekről: [erőforrás-változások beolvasása](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
A [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) parancsmaggal kérheti le a tevékenység naplóját a powershellből. Az alábbiakban néhány gyakori példát talál.

> [!NOTE]
> `Get-AzLog`csak 15 napos előzményt biztosít. Használja a **-MaxRecord** paramétert az utolsó N esemény lekérdezésére 15 napon belül. A 15 napnál régebbi események eléréséhez használja a REST API vagy az SDK-t. Ha **nem tartalmazza az**időpontot, az alapértelmezett érték a **befejezési időpont** mínusz egy óra. Ha nem tartalmazza a **befejezési**időt, az alapértelmezett érték az aktuális idő. Minden alkalommal UTC-ben van.


Megadott időpontot követően létrehozott naplóbejegyzések beolvasása:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Naplóbejegyzések beolvasása a dátum időintervalluma között:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beolvasása egy adott erőforráscsoporthoz:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Naplóbejegyzések beolvasása egy adott erőforrás-szolgáltatótól a dátum időintervalluma között:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beolvasása adott hívóval:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Az utolsó 1000 esemény beolvasása:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>parancssori felület
Az az [monitor Activity-log](cli-samples.md#view-activity-log-for-a-subscription) paranccsal kérheti le a tevékenység naplóját a parancssori felületről. Az alábbiakban néhány gyakori példát talál.


Az összes elérhető beállítás megtekintése.

```azurecli
az monitor activity-log list -h
```

Naplóbejegyzések beolvasása egy adott erőforráscsoporthoz:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Naplóbejegyzések beolvasása adott hívóval:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Naplók beolvasása a hívó által egy adott időtartományon belül:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
A [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) használatával kérheti le a tevékenység naplóját egy Rest-ügyfélről. Az alábbiakban néhány gyakori példát talál.

Tevékenységek naplóinak beolvasása szűrővel:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Tevékenységek naplóinak beolvasása szűrővel és kijelölés:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységek naplóinak beolvasása a Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységek naplóinak beolvasása szűrés vagy kiválasztás nélkül:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>További lépések

* [A platform naplófájljainak áttekintése](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozása a tevékenységek naplófájljainak más célhelyekre való küldéséhez](diagnostic-settings.md)
