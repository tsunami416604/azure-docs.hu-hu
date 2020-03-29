---
title: Az Azure RBAC-módosítások tevékenységnaplóinak megtekintése
description: Tekintse meg az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) azure-erőforrásokon az elmúlt 90 napban végrehajtott változásainak tevékenységnaplóit.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161776"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Az Azure RBAC-módosítások tevékenységnaplóinak megtekintése

Néha szüksége van az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) változásaira vonatkozó információkra, például naplózási vagy hibaelhárítási célokra. Ha valaki módosítja a szerepkör-hozzárendeléseket vagy a szerepkör-definíciókat az előfizetésein belül, a módosítások belesznek jelentkezve az [Azure-tevékenységnaplóba.](../azure-monitor/platform/platform-logs-overview.md) Megtekintheti a tevékenységnaplókat az Azure RBAC összes változásának megtekintéséhez az elmúlt 90 napban.

## <a name="operations-that-are-logged"></a>Naplózott műveletek

Az Azure RBAC-hoz kapcsolódó műveletek et a tevékenységnaplóba naplózva:

- Szerepkör-hozzárendelés létrehozása
- Szerepkör-hozzárendelés törlése
- Egyéni szerepkör-definíció létrehozása vagy frissítése
- Egyéni szerepkör-definíció törlése

## <a name="azure-portal"></a>Azure portál

Első lépésként a legegyszerűbb módszer, ha megtekinti a tevékenységnaplókat az Azure Portalon. A következő képernyőképen egy példa a szerepkör-hozzárendelési műveletek a tevékenységnaplóban. Azt is lehetővé teszi, hogy töltse le a naplókat, mint egy CSV fájlt.

![Tevékenységnaplók a portál használatával - képernyőkép](./media/change-history-report/activity-log-portal.png)

A portálon lévő tevékenységnapló több szűrővel rendelkezik. Az Azure RBAC-hoz kapcsolódó szűrők:

| Szűrés | Érték |
| --------- | --------- |
| Eseménykategória | <ul><li>Adminisztratív</li></ul> |
| Művelet | <ul><li>Szerepkör-hozzárendelés létrehozása</li><li>Szerepkör-hozzárendelés törlése</li><li>Egyéni szerepkör-definíció létrehozása vagy frissítése</li><li>Egyéni szerepkör-definíció törlése</li></ul> |

A tevékenységnaplókról további információt az [Erőforrásokkal kapcsolatos műveletek figyeléséhez szükséges tevékenységnaplók megtekintése](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)című témakörben talál.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

A tevékenységnaplók megtekintéséhez az Azure PowerShell, használja a [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) parancsot.

Ez a parancs felsorolja az előfizetések összes szerepkör-hozzárendelési változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Ez a parancs felsorolja az erőforráscsoport összes erőforrás-definíciós változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Ez a parancs felsorolja az előfizetések összes szerepkör-hozzárendelési és szerepkör-definíciós változását az elmúlt hét napban, és az eredményeket egy listában jeleníti meg:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

A tevékenységnaplók megtekintéséhez az Azure CLI, használja az [az monitor tevékenységnapló-lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancs.

Ez a parancs február 27-től egy erőforráscsoport tevékenységnaplóit sorolja fel, hét napot tekintve:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Ez a parancs február 27-től felsorolja az engedélyezési erőforrás-szolgáltató tevékenységnaplóit, hét napot tekintve:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Az Azure Monitor naplói](../log-analytics/log-analytics-overview.md) egy másik eszköz, amellyel összegyűjtheti és elemezheti az Azure RBAC-módosításokat az összes Azure-erőforráshoz. Az Azure Monitor-naplók a következő előnyökkel járnak:

- Összetett lekérdezések és logika írása
- Integráció a figyelmeztetésekkel, a Power BI-val és más eszközökkel
- Adatok mentése hosszabb megőrzési időszakokra
- Vessünk össze más naplókkal, például a biztonsággal, a virtuális géppel és az egyéni

Az első lépések az alábbiak:

1. [Hozzon létre egy Log Analytics-munkaterületet.](../azure-monitor/learn/quick-create-workspace.md)

1. [Konfigurálja a tevékenységnapló-elemzési megoldást](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) a munkaterülethez.

1. [A tevékenységnaplók megtekintése](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). A Tevékenységnapló-elemzési megoldás áttekintése lapra való gyors navigálás gyors módja a **Naplók** lehetőségre kattintva.

   ![Az Azure Monitor naplói nak beállítása a portálon](./media/change-history-report/azure-log-analytics-option.png)

1. Szükség esetén az [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) segítségével lekérdezheti és megtekintheti a naplókat. További információ: [Az Azure Monitor naplólekérdezéseinek első lépései](../azure-monitor/log-query/get-started-queries.md)című témakörben talál.

Az alábbiakban a célerőforrás-szolgáltató szerint rendezett új szerepkör-hozzárendeléseket ad vissza:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Az alábbi lekérdezés a diagramon megjelenített szerepkör-hozzárendelési módosításokat adja vissza:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Tevékenységnaplók az Advanced Analytics portál használatával – képernyőkép](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>További lépések
* [Események megtekintése a tevékenységnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Előfizetési tevékenységek monitorozása az Azure-tevékenységnaplóval](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
