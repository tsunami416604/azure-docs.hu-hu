---
title: Az Azure-tevékenységnapló eseményeinek megtekintése az Azure Monitorban
description: Tekintse meg az Azure-tevékenységnaplót az Azure Monitorban, és olvassa be a PowerShell, a CLI és a REST API használatával.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397302"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása

Az [Azure-tevékenységnapló](platform-logs-overview.md) betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekbe. Ez a cikk a tevékenységnapló-események megtekintésének és beolvasásának különböző módszereit ismerteti.

## <a name="azure-portal"></a>Azure Portal
Tekintse meg az összes erőforrás tevékenységnaplóját az Azure Portal **Monitor** menüjéből. Egy adott erőforrás tevékenységnaplójának megtekintése az adott erőforrás **menüjének Tevékenységnapló** beállításából.

![Tevékenységnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

A tevékenységnapló-eseményeket a következő mezők szerint szűrheti:

* **Timespan**: Az események kezdési és befejezési időpontja.
* **Kategória**: A [tevékenységnapló kategóriák ban](activity-log-view.md#categories-in-the-activity-log)leírt eseménykategória .
* **Előfizetés**: Egy vagy több Azure-előfizetés neve.
* **Erőforráscsoport**: Egy vagy több erőforráscsoport a kijelölt előfizetéseken belül.
* **Erőforrás (név)**: - Egy adott erőforrás neve.
* **Erőforrás típusa**: Az erőforrás típusa, például _Microsoft.Compute/virtualmachines_.
* **Művelet neve** - Egy Azure Resource Manager-művelet neve, például _Microsoft.SQL/servers/Write_.
* **Súlyosság**: Az esemény súlyossági szintje. A rendelkezésre álló értékek _a következők: Tájékoztató_, _Figyelmeztetés_, _Hiba_, _Kritikus_.
* **Esemény által kezdeményezett**: A felhasználó, aki a műveletet végrehajtotta.
* **Keresés**megnyitása: Nyissa meg a szövegkeresőt, amely az adott karakterláncot keresi az összes esemény összes mezőjében.

## <a name="categories-in-the-activity-log"></a>Kategóriák a tevékenységnaplóban
A tevékenységnapló ban minden eseményhez tartozik egy adott kategória, amelyet az alábbi táblázat ismertet. A kategóriák séma részleteiről az Azure [Activity Log eseményséma](activity-log-schema.md)című témakörben talál részletesinformációt. 

| Kategória | Leírás |
|:---|:---|
| Adminisztratív | Az Erőforrás-kezelőn keresztül végrehajtott összes létrehozási, frissítési, törlési és műveletművelet rekordját tartalmazza. A felügyeleti események közé tartozik például a _virtuális gép létrehozása_ és a hálózati biztonsági csoport _törlése._<br><br>Az Erőforrás-kezelőt használó felhasználó vagy alkalmazás minden művelete egy adott erőforrástípus műveleteként van modellezve. Ha a művelet típusa _Írás_, _Törlés_vagy _Művelet_, a művelet kezdetének és sikeressének rekordjai a Felügyeleti kategóriában kerülnek rögzítésre. A felügyeleti események közé tartozik az előfizetés szerepköralapú hozzáférés-vezérlése módosításai is. |
| Service Health | Az Azure-ban előforduló szolgáltatásállapot-incidensek rekordját tartalmazza. Egy példa egy Service Health esemény _SQL Azure-ban az USA keleti részén tapasztalható állásidő._ <br><br>A Szolgáltatás-egészségügyi események hat fajtából származnak: _Cselekvés szükséges,_ _támogatott helyreállítás,_ _incidens,_ _karbantartás_, _információ_vagy _biztonság_. Ezek az események csak akkor jönnek létre, ha rendelkezik egy erőforrással az előfizetésben, amelyhatással lenne az esemény.
| Resource Health | Az Azure-erőforrásokban előforduló erőforrás-állapotesemények rekordját tartalmazza. Erőforrás-állapot esemény például a _virtuális gép állapota nem érhető el._<br><br>Az erőforrás-egészségügyi események a következő négy állapot egyikét képviselhetik: _Elérhető_, _Nem érhető el_, _Degradálva_és _Ismeretlen_. Ezenkívül az erőforrás-állapotesemények _platform által kezdeményezettként_ vagy _felhasználó által kezdeményezettnek minősíthetők._ |
| Riasztás | Az Azure-riasztások aktiválásainak rekordját tartalmazza. Egy riasztási esemény például a _myVM CPU%-a az elmúlt 5 percben több mint 80 volt._|
| Automatikus méretezés | Az automatikus skálázási motor működésével kapcsolatos események rekordját tartalmazza az előfizetésben megadott automatikus skálázási beállítások alapján. Az automatikus skálázási esemény rekreált például az _automatikus skálázási felskálázási művelet sikertelen._ |
| Ajánlás | Az Azure Advisor ajánlási eseményeit tartalmazza. |
| Biztonság | Az Azure Security Center által létrehozott riasztások rekordját tartalmazza. Biztonsági esemény például a _gyanús kettős kiterjesztésű fájl végrehajtása._ |
| Szabályzat | Az Azure Policy által végrehajtott összes hatásművelet rekordjait tartalmazza. A házirend-események közé tartozik _például_ a Naplózás és _a Megtagadás_. A szabályzat által végrehajtott minden művelet egy erőforrás-műveletként van modellezve. |

## <a name="view-change-history"></a>Módosítási előzmények megtekintése

A tevékenységnapló áttekintésekénsegíthet annak áttekintésében, hogy milyen változások történtek az adott esemény ideje alatt. Ezt az információt a **Módosítási előzmények**között tekintheti meg. Válasszon ki egy eseményt a tevékenységnaplóból, amelybe mélyebbre szeretne tekinteni. Az eseményhez kapcsolódó módosítások megtekintéséhez válassza a **Módosítási előzmények (Előnézet)** lapot.

![Esemény előzménylistájának módosítása](media/activity-logs-overview/change-history-event.png)

Ha az eseményhez bármilyen módosítás tartozik, megjelenik a választható módosítások listája. Ez megnyitja a **Módosítási előzmények (Előnézet)** lapot. Ezen a lapon láthatja az erőforrás módosításait. Amint a következő példából látható, láthatjuk, hogy nem csak, hogy a virtuális gép mérete megváltozott, de mi az előző virtuális gép mérete volt, mielőtt a módosítás, és mi volt módosítva.

![Különbségeket megjelenítő véleményváltozási lap](media/activity-logs-overview/change-history-event-details.png)

Ha többet szeretne megtudni a módosítási előzményekről, olvassa el az [Erőforrás-módosítások beszerezni című témakört.](../../governance/resource-graph/how-to/get-resource-changes.md)






## <a name="powershell"></a>PowerShell
A [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) parancsmag segítségével a PowerShell ből a tevékenységnapló t. Az alábbiakban néhány gyakori példa látható.

> [!NOTE]
> `Get-AzLog`csak 15 napos történelmet biztosít. A **-MaxEvents** paraméterrel 15 napon túl is lekérdezheti az utolsó N-eseményeket. A 15 napnál régebbi események eléréséhez használja a REST API-t vagy az SDK-t. Ha nem adja meg a **StartTime**értéket, akkor az alapértelmezett érték **az EndTime** mínusz egy óra. Ha nem adja meg **az EndTime**értéket, akkor az alapértelmezett érték az aktuális idő. Minden alkalommal utc-ben van.


Adott dátum után létrehozott naplóbejegyzések betöltése:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Naplóbejegyzések betöltése a dátumidő-tartomány között:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beszereznie egy adott erőforráscsoportból:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Naplóbejegyzések beszereznie egy adott erőforrás-szolgáltatótól egy dátumidő-tartomány között:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beszereznie egy adott hívóval:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Szerezd meg az utolsó 1000 eseményt:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>parancssori felület
Az [figyelési tevékenységnapló](cli-samples.md#view-activity-log-for-a-subscription) használatával lekérheti a tevékenységnaplót a CLI-ből. Az alábbiakban néhány gyakori példa látható.


Az összes rendelkezésre álló lehetőség megtekintése.

```azurecli
az monitor activity-log list -h
```

Naplóbejegyzések beszereznie egy adott erőforráscsoportból:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Naplóbejegyzések beszereznie egy adott hívóval:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Naplók beszerezni hívó szerint egy erőforrástípuson belül, egy dátumtartományon belül:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
Az [Azure Monitor REST API-val](https://docs.microsoft.com/rest/api/monitor/) lekérheti a tevékenységnaplót egy REST-ügyfélből. Az alábbiakban néhány gyakori példa látható.

Tevékenységnaplók beszerezni szűrővel:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Tevékenységnaplók beszerezni e szűrővel, és válassza a következőt:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységnaplók beszerezni e jelölőnégyzetet a következő kkel:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységnaplók beszereznie szűrő nélkül, vagy válassza a következőlehetőséget:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>További lépések

* [Platformnaplók áttekintésének elolvasása](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozása a tevékenységnaplók más helyekre való küldéséhez](diagnostic-settings.md)
