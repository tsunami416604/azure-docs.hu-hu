---
title: Hálózati figyelő – NSG-folyamatnaplók létrehozása Azure Resource Manager-sablon használatával
description: Az NSG-folyamatnaplók egyszerű beállításához használjon Azure Resource Manager-sablont és PowerShellt.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 35d185a625a81a259c366a45999769ecf76c6a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538158"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>NSG-folyamatnaplók konfigurálása Azure Resource Manager-sablonból

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Az Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) az Azure natív és hatékony módja az [infrastruktúra kódként való](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)kezelésének.

Ez a cikk bemutatja, hogyan engedélyezheti [az NSG flow naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programozott használatával egy Azure Resource Manager-sablon és az Azure PowerShell. Kezdjük azzal, hogy áttekintést nyújt az NSG flow log objektum tulajdonságairól, majd néhány mintasablonról. Ezután a sablon telepítése egy helyi PowerShell-példány használatával.


## <a name="nsg-flow-logs-object"></a>NSG-folyamatnaplók objektum

Az NSG flow naplók objektum az összes paramétert alább látható.
A tulajdonságok teljes áttekintéséhez olvassa el az [NSG flow naplók sablonhivatkozást.](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft.Network/networkWatchers/flowLogs erőforrás létrehozásához adja hozzá a fenti JSON-t a sablon erőforrásszakaszához.


## <a name="creating-your-template"></a>A sablon létrehozása

Ha első alkalommal használja az Azure Resource Manager-sablonokat, az alábbi hivatkozások segítségével többet is megtudhat róluk.

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Oktatóanyag: Az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Az alábbiakban két példa a teljes sablonokat az NSG flow naplók beállításához.

**1. példa:** A fentiek legegyszerűbb verziója, amelynek minimális paraméterei átmentek. Az alábbi sablon lehetővé teszi az NSG flow naplók a cél NSG és tárolja őket egy adott tárfiókban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Az erőforrás neve "Szülőerőforrás>/Gyermek erőforrás" formátumú. Itt a szülő erőforrás a regionális Hálózatfigyelő példánya (Formátum: NetworkWatcher_<RegionName>. Példa: NetworkWatcher_centraluseuap)
> * targetResourceId a cél NSG erőforrásazonosítója
> * storageId a céltárfiók erőforrásazonosítója

**2. példa:** A következő sablonok, amelyek engedélyezik az NSG-folyamatnaplókat (2-es verzió) 5 napig tartó megőrzésével. A Traffic Analytics engedélyezése 10 perces feldolgozási időközzel.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon telepítése

Ez az oktatóanyag feltételezi, hogy rendelkezik egy meglévő erőforráscsoporttal és egy NSG-vel, amelyen engedélyezheti a Flow-bejelentkezést.
A fenti példasablonok bármelyikét helyileg `azuredeploy.json`mentheti . Frissítse a tulajdonságértékeket, hogy azok érvényes erőforrásokra mutassanak az előfizetésben.

A sablon központi telepítéséhez futtassa a következő parancsot a PowerShellben.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>A telepítés ellenőrzése

Többféleképpen is ellenőrizheti, hogy a központi telepítés sikeres-e. A PowerShell-konzolnak "Sikeres" állapotban kell megjelenítenie a "ProvisioningState" (Kiépítésállapot) lehetőséget. Ezenkívül az [NSG flow naplók portálon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) megerősítheti a módosításokat. Ha problémák merültek fel a központi telepítéssel kapcsolatban, tekintse meg az [Azure Resource Manager gyakori Azure-telepítési hibáinak elhárítása című témakört.](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg az NSG Flow-adatokat az:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
