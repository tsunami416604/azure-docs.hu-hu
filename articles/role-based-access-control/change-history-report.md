---
title: Tevékenység naplók RBAC változásainak megtekintéséhez az Azure-ban |} Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlési változások az elmúlt 90 napban tevékenység naplók megtekintése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e48ea2293c186bbc337f9d70464df374d64b5e61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>A szerepköralapú hozzáférés-vezérlési változásokat tevékenység naplók megtekintése

Valaki módosítást hajt végre szerepkör-definíciók vagy szerepkör-hozzárendelések belül előfizetése, amikor a módosítások naplózásra [Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) a felügyeleti kategória. A tevékenység naplók megtekintéséhez a szerepköralapú hozzáférés-vezérlést (RBAC) változások az elmúlt 90 napban tekintheti meg.

## <a name="operations-that-are-logged"></a>Műveletek, amelyeket a rendszer

Az alábbiakban az RBAC-kapcsolatos műveletek, amelyeket a rendszer műveletnaplóban:

- Egyéni szerepkör-definíció létrehozása vagy módosítása
- Egyéni szerepkör-definíció törlése
- Szerepkör-hozzárendelés létrehozása
- Szerepkör-hozzárendelés törlése

## <a name="azure-portal"></a>Azure Portal

A kezdéshez legkönnyebben a tevékenységi naplóit, és az Azure portál megtekintése. Az alábbi képernyőfelvételen látható egy példa egy szűrt változatának visszaadása megjelenítendő tevékenységnapló a **felügyeleti** kategória szerepkör-definíció- és szerepkör-hozzárendelési műveletek együtt. A naplók letöltése CSV-fájlként hivatkozást is tartalmaz.

![Tevékenység naplókat a portálon – képernyőkép](./media/change-history-report/activity-log-portal.png)

További információkért lásd: [események megtekintéséhez műveletnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell tevékenységi naplóit megtekintéséhez használja a [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) parancsot.

Felsorolja az összes szerepkör hozzárendelésének módosításai az előfizetést az elmúlt hét napban:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Felsorolja az összes szerepkör definíciómódosítások erőforráscsoportban számára az elmúlt hét napban:

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Ez a parancs felsorolja az összes szerepkör-hozzárendelés és az elmúlt hét napban előfizetést szerepkör definition változásairól, és egy listán megjeleníti az eredményeket:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
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

Az Azure parancssori felülettel tevékenységi naplóit megtekintéséhez használja a [az figyelő tevékenységnapló lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot.

Ez a parancs felsorolja a műveletnaplóban erőforráscsoportban a kezdési időpont óta:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Ez a parancs felsorolja a tevékenységi naplóit az erőforrás-engedélyezési szolgáltató a kezdési időpont óta:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Az Azure Naplóelemzés](../log-analytics/log-analytics-overview.md) használhatja, amely összegyűjti és elemzi a szerepköralapú hozzáférés-vezérlési módosításokat az összes Azure-erőforrások egy másik eszköz. Naplóelemzési használata a következő előnyökkel jár:

- Írási összetett lekérdezések és logika
- Integrálható a riasztásokat, a Power bi-ban és a más eszközök
- Hosszabb megőrzési ideig adatok mentése
- A többi naplófájlt, például a biztonsági, a virtuális gép és az egyéni kereszthivatkozás

Első lépésként alapvető lépései a következők:

1. [A Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

1. [A tevékenység Naplóelemzési megoldás konfigurálása](../log-analytics/log-analytics-activity.md#configuration) munkaterület.

1. [A tevékenység naplók megtekintéséhez](../log-analytics/log-analytics-activity.md#using-the-solution). Keresse meg a tevékenység napló Analytics – áttekintés oldalra gyorsan: kattintson a **Naplóelemzési** lehetőséget.

   ![Log Analytics kapcsoló portálon](./media/change-history-report/azure-log-analytics-option.png)

1. Szükség esetén a [naplófájl-keresési](../log-analytics/log-analytics-log-search.md) lap vagy az [Advanced Analytics portál](https://docs.loganalytics.io/docs/Learn) lekérdezése, és a naplók megtekintéséhez. E két beállítás kapcsolatos további információkért lásd: [napló keresése oldal, vagy a Advanced Analytics portál](../log-analytics/log-analytics-log-search-portals.md).

Íme egy lekérdezést, amely a célként megadott erőforrás-szolgáltató szerint vannak rendszerezve új szerepkör-hozzárendelések adja vissza:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Íme egy diagram szerepkör hozzárendelési módosítások visszaadó:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Tevékenység naplók használata a Advanced Analytics portál – képernyőkép](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>További lépések
* [Események megtekintése a tevékenységnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Az Azure tevékenységnapló figyelő előfizetés tevékenység](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
