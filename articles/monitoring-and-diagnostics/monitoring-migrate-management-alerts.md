---
title: "Telepítse át az Azure-riasztások felügyeleti események napló Tevékenységriasztásokat |} Microsoft Docs"
description: "A felügyeleti események riasztások eltávolítja a október 1. Készíti áttelepítése meglévő riasztásokat."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Telepítse át a felügyeleti események Azure riasztások tevékenységnapló riasztások


> [!WARNING]
> A felügyeleti események riasztások ki lesz kapcsolva a következőnél október 1. Az alábbi utasításokat követve segítségével azonosíthatja, ha ezek a riasztások, és át őket, ha így van.
>
> 

## <a name="what-is-changing"></a>Mi van módosítása

Az Azure Monitor (korábban Azure Insights) érhető el egy olyan képességet, amely felügyeleti események ki indított és értesítések egy webhook URL-címe vagy e-mail címekre létrehozott riasztás létrehozása. Előfordulhat, hogy létrehozta az egyik riasztást küld, az alábbi módszerek bármelyikét:
* Az Azure-portálon az egyes erőforrástípusok figyelés -> riasztások-hozzáadása riasztás, ha "Riasztás" értéke "Események" >
* Az Add-AzureRmLogAlertRule PowerShell-parancsmag futtatásával
* Közvetlenül a [riasztási REST API](http://docs.microsoft.com/rest/api/monitor/alertrules) rendelkező odata.type = "ManagementEventRuleCondition" és a dataSource.odata.type = "RuleManagementEventDataSource"
 
A következő PowerShell-parancsfájl az összes riasztás listáját, amelyeknek az előfizetés, valamint az egyes riasztások beállítása feltételek felügyeleti események adja vissza.

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Felügyeleti események rendelkezik nincs riasztás, ha a fenti PowerShell-parancsmag kimeneteként az alábbihoz hasonló figyelmeztetés üzenetből:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

A figyelmeztető üzenetek figyelmen kívül hagyható. Ha a felügyeleti események riasztások rendelkezik, a PowerShell parancsmag kimenete a következőképpen jelenik meg:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Minden riasztás szaggatott vonal választja el egymástól, és a részletek közé tartoznak az erőforrás-azonosítója a riasztás és figyeli az adott szabályra.

Ez a funkció átváltott [Azure tevékenység napló riasztások](monitoring-activity-log-alerts.md). Az új értesítések lehetővé teszik a tevékenységnapló események állítson be feltételt, és értesítést kaphat, ha egy új esemény megfelel a következő feltételt. Számos fejlesztést tartalmaz a riasztásokat a management események is biztosítanak:
* A csoport értesítési címzettek ("műveletek") között számos riasztásokról felhasználhatja [művelet csoportok](monitoring-action-groups.md), módosítása, akik egy riasztást kell fogadnia összetettsége csökkentése.
* Közvetlenül a telefonján a SMS művelet csoportokkal is kap értesítést.
* Is [Tevékenységriasztásokat napló létrehozása a Resource Manager-sablonok](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Nagyobb rugalmasságot és igény szerinti erősségét feltételek hozhatja létre.
* Értesítések a gyorsabban érkeznek.
 
## <a name="how-to-migrate"></a>Hogyan telepítheti át
 
Hozzon létre egy új tevékenység napló értesítés, a következő lehetőségek közül választhat:
* Hajtsa végre a [útmutatónkban kapcsolatos riasztás létrehozása az Azure-portálon](monitoring-activity-log-alerts.md)
* Megtudhatja, hogyan [Resource Manager-sablon használatával riasztás létrehozása](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Riasztások a korábban létrehozott felügyeleti események nem lesz automatikusan áttelepítve a tevékenység napló riasztásokat. A fenti PowerShell-parancsfájl segítségével a riasztásokat a felügyeleti események van beállítva, és manuálisan hozza létre őket tevékenység napló riasztásként kell. A október 1, amely után riasztások felügyeleti események a már nem lesznek láthatók az Azure-előfizetése előtt kell végezni. Más típusú Azure riasztások, például Azure metrika riasztások, az Application Insights-riasztások és Naplóelemzési riasztások nem érinti ez a változás. Ha kérdése van, az alábbi megjegyzések fel.


## <a name="next-steps"></a>Következő lépések

* További információ [műveletnapló](monitoring-overview-activity-logs.md)
* Konfigurálása [napló Tevékenységriasztásokat Azure-portálon](monitoring-activity-log-alerts.md)
* Konfigurálása [Tevékenységriasztásokat napló erőforrás-kezelő használatával](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md)
* További információ [szolgáltatáshoz értesítést](monitoring-service-notifications.md)
* További információ [művelet csoportok](monitoring-action-groups.md)
