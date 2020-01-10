---
title: Azure-Tevékenységnaplók eseményeinek megtekintése Azure Monitor
description: Megtekintheti az Azure-beli tevékenység naplóját Azure Monitor és beolvashatja a PowerShell, a CLI és a REST API.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 46d26aa5dccd32438b2028e21eaa94f7993944d1
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749518"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése

Az [Azure-tevékenység naplója](platform-logs-overview.md) betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Ez a cikk a tevékenységek naplózási eseményeinek megtekintésére és beolvasására szolgáló különböző módszerekkel kapcsolatos információkat tartalmazza.

## <a name="azure-portal"></a>Azure portál
A Azure Portal **figyelő** menüjében tekintse meg a tevékenység naplóját az összes erőforráshoz. Egy adott erőforráshoz tartozó tevékenység naplójának megtekintése az erőforrás menüjének **tevékenység napló** lehetőségével.

![Műveletnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

A tevékenységek naplózási eseményeit a következő mezők alapján szűrheti:

* **TimeSpan**: az események kezdési és befejezési időpontja.
* **Kategória**: az esemény kategóriája, a [tevékenység naplójának kategóriák részében](activity-log-view.md#categories-in-the-activity-log)leírtak szerint.
* **Előfizetés**: egy vagy több Azure-előfizetés neve.
* **Erőforráscsoport**: egy vagy több erőforráscsoport a kijelölt előfizetéseken belül.
* **Erőforrás (név)** : – egy adott erőforrás neve.
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
> `Get-AzLog` csak a 15 napos előzményeket biztosítja. Használja a **-MaxEvents** paramétert az utolsó N esemény lekérdezésére 15 napon belül. A 15 napnál régebbi események eléréséhez használja a REST API vagy az SDK-t. Ha **nem tartalmazza az**időpontot, az alapértelmezett érték a **befejezési időpont** mínusz egy óra. Ha nem tartalmazza a **befejezési**időt, az alapértelmezett érték az aktuális idő. Minden alkalommal UTC-ben van.


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
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
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


## <a name="activity-logs-analytics-monitoring-solution"></a>Activity logs Analytics monitorozási megoldás
Az Azure Log Analytics-figyelési megoldás több naplózási lekérdezést és nézetet tartalmaz a Log Analytics munkaterületen található műveletnapló-rekordok elemzéséhez.

### <a name="prerequisites"></a>Előfeltételek
Létre kell hoznia egy diagnosztikai beállítást, hogy az előfizetéséhez tartozó műveletnapló elküldje egy Log Analytics munkaterületre. Lásd: az [Azure platform-naplók gyűjtése Azure Monitor log Analytics munkaterületen](resource-logs-collect-workspace.md).

### <a name="install-the-solution"></a>A megoldás telepítése
A **Activity log Analytics** megoldás telepítéséhez használja a [figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution) című szakaszt. Nincs szükség további konfigurációra.

### <a name="use-the-solution"></a>A megoldás használata
A **műveletnapló** lap tetején található **naplók** elemre kattintva nyissa meg az előfizetéshez tartozó [Activity log Analytics figyelési megoldást](activity-log-collect.md) . Vagy a Azure Portal összes figyelési megoldásának elérése az előfizetési **figyelő** menüjében. Válassza a **továbbiak** lehetőséget a **betekintési** szakaszban, hogy megnyissa az **Áttekintés** lapot a megoldás csempével. Az **Azure-tevékenység naplói** csempén a munkaterületen található **AzureActivity** -rekordok száma látható.

![Az Azure-tevékenység naplói csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységek naplói** csempére az **Azure-tevékenység naplói** nézetének megnyitásához. A nézet a következő táblázatban szereplő vizualizációs részeket tartalmazza. Mindegyik rész legfeljebb 10 olyan elemet sorol fel, amelyek megfelelnek a megadott időtartományra vonatkozó feltételeknek. Futtathat egy olyan naplózási lekérdezést, amely az összes egyező rekordot visszaadja, ha az **összes megtekintése** elemre kattint a rész alján.

![Azure Activity-naplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)

| Vizualizációs rész | Leírás |
| --- | --- |
| Azure-Tevékenységnaplók bejegyzései | Megjeleníti az Azure-beli tevékenység naplójának felső rekordjának a kiválasztott dátumtartományt tartalmazó oszlopát, és megjeleníti az első 10 tevékenység-hívó listáját. Kattintson a sávdiagramra a `AzureActivity`naplójának kereséséhez. Kattintson egy hívó elemre, és futtassa az adott elemhez tartozó összes műveletnapló-bejegyzést. |
| Tevékenységek naplói állapot szerint | Megjeleníti az Azure-tevékenység naplójának állapotát a kiválasztott dátumtartomány esetében, valamint az első tíz állapotüzenetek listáját. Kattintson a diagramra a `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`naplózási lekérdezésének futtatásához. Kattintson egy állapot elemre az adott állapotjelző rekord összes tevékenységi naplójára vonatkozó naplóbeli keresés futtatásához. |
| Tevékenységek naplói erőforrás szerint | Megjeleníti a tevékenységi naplókkal rendelkező erőforrások teljes számát, és felsorolja az egyes erőforrásokhoz tartozó rekordok számát tartalmazó első tíz erőforrást. Kattintson a teljes területen a `AzureActivity | summarize AggregatedValue = count() by Resource`naplójának kereséséhez, amely megjeleníti a megoldás számára elérhető összes Azure-erőforrást. Kattintson egy erőforrásra az adott erőforráshoz tartozó összes tevékenységi rekord visszaadására szolgáló naplózási lekérdezés futtatásához. |
| Tevékenységek naplói erőforrás-szolgáltató szerint | Megjeleníti a tevékenységek naplóit előállító erőforrás-szolgáltatók teljes számát, és felsorolja a tíz legfontosabbat. Az összes Azure-erőforrás-szolgáltatót megjelenítő `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`hoz tartozó napló lekérdezésének futtatásához kattintson a teljes területen. Kattintson egy erőforrás-szolgáltatóra egy olyan log-lekérdezés futtatásához, amely a szolgáltató összes tevékenységi rekordját visszaadja. |




## <a name="next-steps"></a>Következő lépések

* [A platform naplófájljainak áttekintése](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozása a tevékenységek naplófájljainak más célhelyekre való küldéséhez](diagnostic-settings.md)
