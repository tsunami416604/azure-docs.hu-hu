---
title: Az Azure tevékenységnapló-események megtekintése az Azure monitorban
description: Az Azure-tevékenységnapló megtekintése az Azure monitorban, és kérje le a PowerShell, CLI és REST API-t.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248114"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Megtekintheti, és az Azure-tevékenységnapló-események beolvasása

A [Azure-tevékenységnapló](activity-logs-overview.md) történt az Azure-beli előfizetés-szintű események betekintést nyújt. Ez a cikk részletesen tekinthetők meg és beolvasása a tevékenységnapló eseményei különböző módszereket.

## <a name="azure-portal"></a>Azure Portal
Az összes erőforrás a tevékenységnapló megtekintése a **figyelő** menü az Azure Portalon. Egy adott erőforráshoz, a tevékenységnapló megtekintése a **tevékenységnapló** lehetőség az adott erőforrás menüben.

![Tevékenységnapló megtekintése](./media/activity-logs-overview/view-activity-log.png)

Tevékenységnapló eseményei a következő mezők szerint szűrheti:

* **Időtartomány**: Események kezdő és befejező időpontját.
* **Kategória**: Az esemény kategóriáját leírtak szerint [kategóriák a tevékenységnaplóban](activity-logs-overview.md#categories-in-the-activity-log).
* **Előfizetés**: Egy vagy több Azure-előfizetés nevét.
* **Erőforráscsoport**: Egy vagy több erőforráscsoport a kijelölt előfizetésekben belül.
* **Erőforrás (név)** : – egy adott erőforrás nevét.
* **Erőforrástípus**: Az erőforrás, például típusát _Microsoft.Compute/virtualmachines_.
* **Název operace** – egy Azure Resource Manager-műveletet, például nevét _Microsoft.SQL/servers/Write_.
* **Súlyosság**: Az esemény súlyossági szintje. Elérhető értékek a következők _tájékoztató_, _figyelmeztetés_, _hiba_, _kritikus_.
* **Esemény kezdeményezője**: A műveletet végrehajtó felhasználó.
* **Keresés megnyitása**: Nyissa meg a keresési szövegmezőbe, amelyek között az összes esemény található összes mezőhöz adott karakterláncot keres.

### <a name="view-change-history"></a>Módosítási előzményeinek megtekintése

Ha a tevékenységnapló áttekintése, emellett segít tekintse meg az változások során adott esemény időpontja. Megtekintheti, hogy ezek az információk **módosítási előzmények**. Jelöljön ki egy eseményt a tevékenységnapló mélyebben elmerülhet keresni szeretné. Válassza ki a **módosítási előzmények (előzetes verzió)** lapot választva megtekintheti bármely adott eseményhez tartozó módosítások.

![Módosítsa az esemény előzménylista](media/activity-logs-overview/change-history-event.png)

Ha az eseményhez kapcsolódó módosításokat, látni fogja a választható módosítások listája olvasható. Megnyílik a **módosítási előzmények (előzetes verzió)** lapot. Ezen az oldalon láthatja a módosításokat az erőforráshoz. Ahogy az alábbi példában látható, jelenleg csak nem láthatja, hogy a virtuális gép méreteket, de mi az előző Virtuálisgép-méretet a váltás előtt volt, és mi megváltozott hogy megváltozott-e.

![Módosítási előzmények oldalról különbségek](media/activity-logs-overview/change-history-event-details.png)

Változások nyomon követése kapcsolatos további információkért lásd: [beolvasása az erőforrás-módosítások](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Kattintson a **naplók** felső részén a **tevékenységnapló** megnyitásához a [Activity Log Analytics figyelési megoldás](activity-log-collect.md) az előfizetéshez. Ez lehetővé teszi a tevékenységnapló elemzésének megtekintéséhez és futtatásához [lekérdezések naplózását](../log-query/log-query-overview.md) együtt a **AzureActivity** tábla. Ha a Log Analytics-munkaterület nincs csatlakoztatva a Tevékenységnaplót, a rendszer kéri, a konfiguráció.



## <a name="powershell"></a>PowerShell
Használja a [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) beolvasásához a tevékenységnaplóban a Powershellből. Az alábbiakban néhány gyakori példa.

> [!NOTE]
> `Get-AzLog` Előzmények 15 napos csak biztosít. Használja a **– a MaxEvents** 15 napos időszak letelte után az utolsó N események lekérdezési paramétert. 15 napnál régebbi események eléréséhez használja a REST API vagy SDK-t. Ha nem adja meg az **StartTime**, akkor az alapértelmezett érték **EndTime** mínusz egy óra. Ha nem adja meg az **EndTime**, akkor az alapértelmezett érték az aktuális idő. Minden esetben vannak (UTC).


Kérje le egy adott dátum és időpont után létrehozott log bejegyzéseket:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Kérje le a naplóbejegyzések között dátum időtartomány:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplózási bejegyzések beolvasása egy adott erőforráscsoporthoz:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Kérje le a naplóbejegyzések egy adott erőforrás-szolgáltató közötti dátum időtartomány:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Kérje le egy adott hívóval naplóbejegyzések:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Kérje le a legutóbbi 1000 esemény:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>parancssori felület
Használat [az monitor tevékenységnapló](cli-samples.md#view-activity-log-for-a-subscription) lekérdezni a parancssori felület a tevékenységnaplóban. Az alábbiakban néhány gyakori példa.


Az összes rendelkezésre álló beállítások megtekintéséhez.

```azurecli
az monitor activity-log list -h
```

Naplózási bejegyzések beolvasása egy adott erőforráscsoporthoz:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Kérje le egy adott hívóval naplóbejegyzések:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Hívó naplók lekérése az adott dátumtartományban egy erőforrás típusa:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
Használja a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) a tevékenységnapló lekérése a REST-ügyféllel. Az alábbiakban néhány gyakori példa.

Tevékenységeket tartalmazó naplók lekérése a szűrővel:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Tevékenységnaplók mindenre szűrőt, és válassza ki:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenység-naplók lekérése az egyes:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Kérje le a tevékenységeket tartalmazó naplók szűrőt, vagy válasszon nélkül:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>További lépések

* [Olvassa el a tevékenységnapló áttekintése](activity-logs-overview.md)
* [Storage a Tevékenységnaplót archiválhatja, illetve az Event Hubs-bA](activity-log-export.md)
* [Az Event hubs Azure tevékenységnapló Stream](activity-logs-stream-event-hubs.md)
* [A Storage Azure-tevékenységnapló archiválása](archive-activity-log.md)

