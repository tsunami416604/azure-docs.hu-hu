---
title: Az Azure felügyeleti eseményriasztások át a tevékenységnapló-riasztások
description: Felügyeleti eseményekkel kapcsolatos riasztásokat a rendszer eltávolítja. október 1-jén. Készítsünk áttelepítése meglévő riasztást.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: fb54e11c9da6bec2a1e0354317df6343140cbf09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794116"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Az Azure felügyeleti eseményriasztások át a tevékenységnapló-riasztások

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> Felügyeleti eseményriasztások ki lesz kapcsolva napon vagy azután. október 1. Az alábbi utasításokat követve segítségével azonosíthatja, ha ezek a riasztások, és telepítheti át őket, ha igen.

## <a name="what-is-changing"></a>Mi változik

Az Azure Monitor (korábbi nevén Azure Insights) egy olyan képességet, hogy minden felügyeleti események által aktivált, és a egy webhook URL-címet vagy e-mail-értesítések generált riasztás létrehozásához érhető el. Előfordulhat, hogy létrehozott egy ilyen riasztást küld, az alábbi módszerek bármelyikét:
* Az Azure Portalon, bizonyos erőforrástípusok, figyelés -> riasztások hozzáadása riasztás, ha "Riasztás" értéke "Események" ->
* Az Add-AzLogAlertRule PowerShell-parancsmag futtatásával
* Közvetlenül a [az alert REST API](https://docs.microsoft.com/rest/api/monitor/alertrules) az odata.type = "ManagementEventRuleCondition" és a dataSource.odata.type = "RuleManagementEventDataSource"
 
A következő PowerShell-parancsfájlt, amely rendelkezik az előfizetés, valamint az egyes riasztásokról a feltételek a felügyeleti események minden riasztás listáját adja vissza.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
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

Ha nincsenek riasztások felügyeleti események, a fenti PowerShell-parancsmag kimenete egy sorozat figyelmeztető üzenetek a következőhöz hasonló:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

A figyelmeztető üzenetek figyelmen kívül hagyható. Ha a felügyeleti események riasztások rendelkezik, a PowerShell-parancsmag kimenete néznek ki:

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

Minden riasztás szaggatott vonal választja el egymástól, és szerepel a riasztás és az adott szabályokat a figyelt erőforrás-Azonosítóját.

Ez a funkció átváltott [Azure Monitor tevékenységnapló-riasztások](../../azure-monitor/platform/activity-log-alerts.md). Ezek a riasztások lehetővé teszi a tevékenységnapló eseményei állítson be feltételt, és értesítést kaphat, ha új esemény megfelel a feltételnek. A felügyeleti események riasztásokból számos fejlesztést is elérhető:
* A csoport az értesítés címzettjeinek ("műveletek") között számos riasztásokról felhasználhatja [Műveletcsoportok](../../azure-monitor/platform/action-groups.md), egyszerűsíteni módosítása, akik egy riasztást kell fogadnia.
* Értesítést kaphat közvetlenül a Műveletcsoportok SMS használata telefonon.
* Is [tevékenységnapló-riasztások létrehozása a Resource Manager-sablonok](../../azure-monitor/platform/alerts-activity-log.md).
* Feltételek és nagyobb rugalmasságot és igények kielégítéséhez is létrehozhat.
* Értesítések a gyorsabban érkeznek.
 
## <a name="how-to-migrate"></a>Migrálási eljárás
 
Szeretne létrehozni egy új tevékenység Log riasztási, közül választhat:
* Hajtsa végre a [útmutatónk bemutatja a riasztás létrehozása az Azure Portalon](../../azure-monitor/platform/activity-log-alerts.md)
* Ismerje meg, hogyan [hozzon létre egy riasztást, Resource Manager-sablon használatával](../../azure-monitor/platform/alerts-activity-log.md)
 
Riasztások a korábban létrehozott felügyeleti események nem lesz automatikusan áttelepítve a tevékenységnapló-riasztásokra. Az előző PowerShell-parancsfájl használatával, hogy van konfigurálva, és manuálisan hozza létre őket újra, a tevékenységnapló-riasztások felügyeleti események riasztások felsorolása kell. Ez október 1., utána felügyeleti eseményriasztások többé nem jelenik meg az Azure-előfizetés előtt kell elvégezni. Más típusú Azure-riasztások, beleértve az Azure Monitor metrikákhoz kapcsolódó riasztások, az Application Insights-riasztások és Naplókeresési riasztások nem érinti a változás. Ha bármilyen kérdése van, az alábbi megjegyzések közzététele.


## <a name="next-steps"></a>További lépések

* Tudjon meg többet [tevékenységnapló](../../azure-monitor/platform/activity-logs-overview.md)
* Konfigurálása [tevékenységnapló-riasztások Azure-portálon](../../azure-monitor/platform/activity-log-alerts.md)
* Konfigurálása [Resource Manageren keresztül tevékenységnapló-riasztások](../../azure-monitor/platform/alerts-activity-log.md)
* Tekintse át a [tevékenység log riasztási webhook sémáról](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Tudjon meg többet [szolgáltatási értesítések](../../azure-monitor/platform/service-notifications.md)
* Tudjon meg többet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md)

