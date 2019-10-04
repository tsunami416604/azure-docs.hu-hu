---
title: Eseménynaplók megtekintése az Azure-erőforrások RBAC változásaihoz | Microsoft Docs
description: A szerepköralapú hozzáférés-vezérlés (RBAC) által az elmúlt 90 napra vonatkozó Azure-erőforrások változásainak megtekintése.
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
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172403"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Az Azure-erőforrásokra vonatkozó RBAC-változásokkal kapcsolatos tevékenységek naplóinak megtekintése

Előfordulhat, hogy az Azure-erőforrások szerepköralapú hozzáférés-vezérlési (RBAC) módosításaival kapcsolatos információkra van szüksége, például naplózási vagy hibaelhárítási célból. Ha valaki módosítja a szerepkör-hozzárendeléseket vagy a szerepkör-definíciókat az előfizetéseken belül, akkor a módosítások bekerülnek az [Azure-tevékenység naplójába](../azure-monitor/platform/activity-logs-overview.md). A tevékenységek naplóit megtekintve megtekintheti az elmúlt 90 nap összes RBAC változását.

## <a name="operations-that-are-logged"></a>Naplózott műveletek

Itt láthatók a RBAC kapcsolatos műveletek:

- Szerepkör-hozzárendelés létrehozása
- Szerepkör-hozzárendelés törlése
- Egyéni szerepkör-definíció létrehozása vagy frissítése
- Egyéni szerepkör-definíció törlése

## <a name="azure-portal"></a>Azure Portal

Első lépésként a legkönnyebben megtekintheti a tevékenység naplóit a Azure Portal. Az alábbi képernyőfelvételen egy olyan műveletnapló látható, amely a szerepkör-hozzárendelés és a szerepkör-definíciós műveletek megjelenítésére lett szűrve. Emellett egy hivatkozást is tartalmaz, amellyel a naplófájlok a CSV-fájlként tölthetők le.

![A portált használó tevékenységek naplói – képernyőfelvétel](./media/change-history-report/activity-log-portal.png)

A portálon a tevékenység naplója több szűrőt tartalmaz. A RBAC-hez kapcsolódó szűrők:

|Szűrés  |Value  |
|---------|---------|
|Eseménykategória     | <ul><li>Rendszergazdai</li></ul>         |
|Művelet     | <ul><li>Szerepkör-hozzárendelés létrehozása</li> <li>Szerepkör-hozzárendelés törlése</li> <li>Egyéni szerepkör-definíció létrehozása vagy frissítése</li> <li>Egyéni szerepkör-definíció törlése</li></ul>      |


További információ a tevékenységi naplókról: [események megtekintése a tevékenység naplójában](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Ha Azure PowerShellkal szeretné megtekinteni a tevékenység naplóit, használja a [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) parancsot.

Ez a parancs felsorolja az előfizetés összes szerepkör-hozzárendelésének változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Ez a parancs felsorolja az adott erőforráscsoport összes szerepkör-definíciójának változását az elmúlt hét napban:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Ez a parancs felsorolja az összes szerepkör-hozzárendelést és szerepkör-definíciót az előfizetésben az elmúlt hét napban, és megjeleníti az eredményeket egy listában:

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
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI-vel szeretné megtekinteni a tevékenység naplóit, használja az az [monitor Activity-log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) parancsot.

Ez a parancs az erőforráscsoport tevékenység-naplóit listázza a kezdési időpont óta:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Ez a parancs a kezdési időpont óta felsorolja az engedélyezési erőforrás-szolgáltató tevékenységi naplóit:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Azure monitor a naplók](../log-analytics/log-analytics-overview.md) egy másik eszköz, amellyel összegyűjtheti és elemezheti az összes Azure-erőforrás RBAC-módosításait. Azure Monitor naplók előnyei a következők:

- Összetett lekérdezések és logika írása
- Integrálás riasztásokkal, Power BIekkel és egyéb eszközökkel
- Adatok mentése hosszabb megőrzési időtartamokra
- Kereszthivatkozás más naplókba, például a biztonság, a virtuális gép és az egyéni

Az első lépésekhez a következő alapvető lépések szükségesek:

1. [Log Analytics munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace.md).

1. [Konfigurálja a Activity log Analytics megoldást](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) a munkaterületre.

1. [Tekintse meg a tevékenységek naplóit](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). A Activity Log Analytics megoldás áttekintő oldalának gyors eléréséhez kattintson a **log Analytics** lehetőségre.

   ![Azure Monitor naplók lehetőség a portálon](./media/change-history-report/azure-log-analytics-option.png)

1. A naplók lekérdezéséhez [](../log-analytics/log-analytics-log-search.md) vagy a [speciális elemzési portálhoz](../azure-monitor/log-query/get-started-portal.md) is használhatja a naplókat, és megtekintheti azokat. További információ erről a két lehetőségről: [a naplók keresése vagy a speciális elemzési portál](../azure-monitor/log-query/portals.md).

A következő egy lekérdezés, amely a célként megadott erőforrás-szolgáltató által szervezett új szerepkör-hozzárendeléseket adja vissza:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Az alábbi lekérdezés a diagramon megjelenített szerepkör-hozzárendelési módosításokat adja vissza:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Tevékenységek naplói a speciális elemzési portál használatával – képernyőfelvétel](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>További lépések
* [Események megtekintése a tevékenységnaplóban](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Előfizetési tevékenységek monitorozása az Azure-tevékenységnaplóval](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
