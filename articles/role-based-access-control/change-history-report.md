---
title: Az RBAC-módosítások Tevékenységnaplók megtekintése az Azure-ban |} A Microsoft Docs
description: A Tevékenységnaplók megtekintése a szerepköralapú hozzáférés-vezérlés (RBAC) módosításokat az elmúlt 90 nap.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ff09647dc3271c947b3037afcfacc33951ddb07
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117648"
---
# <a name="view-activity-logs-for-rbac-changes"></a>Az RBAC-módosítások Tevékenységnaplók megtekintése

Néha szüksége információ a szerepköralapú hozzáférés-vezérlés (RBAC) módosításokat, mint például a naplózás és hibaelhárítás céljából. A módosítások valaki módosít szerepkör-hozzárendeléseket vagy szerepkör-definíciók belül az előfizetések bármikor első bejelentkezve [Azure-tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md). Megtekintheti a vizsgálati naplók az elmúlt 90 napra vonatkozó összes RBAC-módosítások megtekintéséhez.

## <a name="operations-that-are-logged"></a>Naplózott műveletek

Az alábbiakban az RBAC kapcsolatos műveletek a tevékenységnaplóban naplózott:

- Szerepkör-hozzárendelés létrehozása
- Szerepkör-hozzárendelés törlése
- Egyéni szerepkör-definíció létrehozása vagy módosítása
- Egyéni szerepkör-definíció törlése

## <a name="azure-portal"></a>Azure Portal

Első lépésként a legegyszerűbb módja, hogy az Azure Portalon a Tevékenységnaplók megtekintése. A következő képernyőképen látható egy példa egy tevékenységnapló szűrt szerepkör-hozzárendelés és szerepkör-definíció műveletek megjelenítéséhez. A naplók letöltése CSV-fájlként hivatkozást is tartalmaz.

![A Tevékenységnaplók a portállal – képernyőkép](./media/change-history-report/activity-log-portal.png)

A tevékenységnaplóban a portálon számos szűrőket tartalmaz. Az RBAC-kapcsolódó szűrők a következők:

|Szűrés  |Érték  |
|---------|---------|
|Eseménykategória     | <ul><li>Adminisztratív</li></ul>         |
|Művelet     | <ul><li>Szerepkör-hozzárendelés létrehozása</li> <li>Szerepkör-hozzárendelés törlése</li> <li>Egyéni szerepkör-definíció létrehozása vagy módosítása</li> <li>Egyéni szerepkör-definíció törlése</li></ul>      |


A Tevékenységnaplók kapcsolatos további információkért lásd: [események megtekintése a tevékenységnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Az Azure PowerShell-lel tevékenységeket tartalmazó naplók megtekintéséhez használja a [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) parancsot.

Ez a parancs felsorolja az összes szerepkör hozzárendelés változásait egy előfizetést az elmúlt hét nap:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Ez a parancs egy erőforráscsoportba tartozó összes szerepkör definíciómódosítások felsorolja az elmúlt hét nap:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Ez a parancs felsorolja az összes szerepkör-hozzárendelés és a egy előfizetést az elmúlt hét nap szerepkör definíciójának változásai, és megjeleníti az eredményeket egy listában:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI-vel tevékenységeket tartalmazó naplók megtekintéséhez használja a [az monitor tevékenységnapló lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot.

Ez a parancs felsorolja a tevékenységnaplókat, az erőforráscsoport, a kezdési időpont óta:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Ez a parancs felsorolja a vizsgálati naplók az engedélyezési erőforrás-szolgáltató a kezdési időpont óta:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Az Azure Log Analytics](../log-analytics/log-analytics-overview.md) egy másik eszköz használatával gyűjti össze és elemzi az RBAC-módosítások az összes Azure-erőforrásokhoz. A log Analytics az alábbi előnyökkel jár:

- Összetett lekérdezéseket és logika írása
- Riasztások, a Power BI és egyéb eszközök integrálása
- Hosszabb megőrzési idők az adatok mentése
- Egyéb naplóival, például a biztonság, a virtuális gép és az egyéni kereszthivatkozás

Íme az alapvető lépéseken, a kezdéshez:

1. [Hozzon létre egy Log Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md).

1. [Az Activity Log Analytics megoldás konfigurálása](../azure-monitor/platform/collect-activity-logs.md#configuration) a munkaterületen.

1. [A Tevékenységnaplók megtekintése](../azure-monitor/platform/collect-activity-logs.md#using-the-solution). A Tevékenységnaplók elemzésének áttekintése lapon keresse meg gyorsan, hogy a **Log Analytics** lehetőséget.

   ![A portál a log Analytics beállítás](./media/change-history-report/azure-log-analytics-option.png)

1. Lehetősége van a [naplóbeli keresés](../log-analytics/log-analytics-log-search.md) lap vagy az [bővített analitika portál](../azure-monitor/log-query/get-started-portal.md) lekérdezése és a naplók megtekintéséhez. E két beállítás kapcsolatos további információkért lásd: [naplók keresése oldalát, vagy a bővített analitika portál](../azure-monitor/log-query/portals.md).

Íme egy lekérdezést, amely az új szerepkör-hozzárendelések célként megadott erőforrás-szolgáltató szerint rendezve adja vissza:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

A következő szerepkör-hozzárendelések módosításainak egy diagram visszaadó:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![A Tevékenységnaplók segítségével a bővített analitika portál – képernyőkép](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>További lépések
* [Események megtekintése a tevékenységnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Az Azure-tevékenységnapló-előfizetési tevékenység figyelése](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
