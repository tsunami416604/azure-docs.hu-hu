---
title: Resource Manager-sablonok – minták a Azure Monitor
description: Azure Monitor szolgáltatások üzembe helyezése és konfigurálása Resource Manager-sablonok használatával
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 2c9287e6f4bda429309dd0041215b271678d03d3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860698"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Resource Manager-sablonok – minták a Azure Monitor

Azure Monitor üzembe helyezhető és konfigurálható [Azure Resource Manager sablon](../../azure-resource-manager/templates/template-syntax.md)használatával. A következő cikkek különböző Azure Monitor-funkciókhoz biztosítanak mintákat. Ezek a minták módosíthatók az adott követelmények alapján, és üzembe helyezhetők a Resource Manager-sablonok üzembe helyezésének bármely szabványos metódusával. 

## <a name="deploying-the-sample-templates"></a>A sablonok üzembe helyezése
A minták használatának alapvető lépései a következők:

1. Másolja a sablont, és mentse JSON-fájlként.
2. Módosítsa a környezet paramétereit, és mentse JSON-fájlként.
4. A sablon üzembe helyezése a [Resource Manager-sablonok bármely üzembe helyezési módszerének](../../azure-resource-manager/templates/deploy-powershell.md)használatával. 

Az alábbi parancsokkal például telepítheti a sablont és a paramétereket tartalmazó fájlt az előfizetésbe a PowerShell vagy az Azure CLI használatával.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Sablonok listája

- [Ügynökök](resource-manager-agent.md) – log Analytics ügynök és diagnosztikai bővítmény üzembe helyezése és konfigurálása.
- Riasztások
  - [Riasztási szabályok naplózása](resource-manager-alerts-log.md) – riasztások naplózási lekérdezésekről és az Azure-tevékenység naplója.
  - [Metrikus riasztási szabályok](resource-manager-alerts-metric.md) – a metrikák különböző típusú logikát használó riasztásai.
- Application Insights – hamarosan elérhető lesz.
- [Diagnosztikai beállítások](resource-manager-diagnostic-settings.md) – diagnosztikai beállítások létrehozása a naplók és a metrikák továbbítására különböző erőforrástípusok alapján.
- [Naplók lekérdezése](resource-manager-log-queries.md) – mentett naplózási lekérdezések létrehozása egy log Analytics munkaterületen.
- [Log Analytics munkaterület](resource-manager-workspace.md) – log Analytics munkaterület létrehozása és különböző adatforrások gyűjteményének konfigurálása log Analytics-ügynökből.
- [Munkafüzetek](resource-manager-workbooks.md) – munkafüzetek létrehozása.
- [Azure monitor a tárolók](resource-manager-container-insights.md) számára – a fürtöket Azure monitor a tárolók számára.
- [Azure monitor for VMS](resource-manager-vminsights.md) – a virtuális gépeket Azure monitor for VMsba.



## <a name="next-steps"></a>További lépések

- További információ a [Resource Manager-sablonokról](../../azure-resource-manager/templates/overview.md)
