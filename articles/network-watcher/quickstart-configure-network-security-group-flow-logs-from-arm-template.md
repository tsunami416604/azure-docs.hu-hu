---
title: 'Gyors útmutató: NSG-adatforgalmi naplók konfigurálása Azure Resource Manager sablon használatával'
description: Megtudhatja, hogyan engedélyezheti programozott módon a NSG-flow naplóit egy Azure Resource Manager sablon és Azure PowerShell használatával.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986317"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Gyors útmutató: NSG-adatforgalmi naplók konfigurálása ARM-sablonból

Ebben a rövid útmutatóban egy [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) sablon (ARM-sablon) és Azure PowerShell használatával engedélyezheti a [NSG-folyamatok](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programozott használatát. 

Kezdjük azzal, hogy áttekintjük a NSG flow log objektum tulajdonságait, amelyet néhány sablon követ. Ezután a sablon üzembe helyezése egy helyi PowerShell-példány használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="nsg-flow-logs-object"></a>NSG-folyamat naplófájljainak objektuma

Az NSG flow az összes paraméterrel rendelkező objektumot alább mutatja.
A tulajdonságok teljes áttekintéséhez olvassa el a [NSG flow-naplók sablonjának referenciáját](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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
Microsoft. Network/networkWatchers/flowLogs erőforrás létrehozásához adja hozzá a fenti JSON-t a sablon erőforrások szakaszához.


## <a name="creating-your-template"></a>A sablon létrehozása

Ha első alkalommal használja Azure Resource Manager-sablonokat, további információt az alábbi hivatkozásokon talál.

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create) közül származik.

Az alábbi példában a teljes sablon a NSG-naplók beállításához megadott minimális paraméterekkel rendelkező legegyszerűbb verzió. További példákért látogasson el erre a [hivatkozásra](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Példa**: az alábbi sablon lehetővé teszi, hogy a NSG a cél NSG, és tárolja őket egy adott Storage-fiókban.

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
> * Az erőforrás neve "szülő Resource_Child erőforrás" formátumú. Itt a szülő erőforrás a regionális Network Watcher-példány (Format: NetworkWatcher_RegionName. Példa: NetworkWatcher_centraluseuap)
> * a Targetresourceid azonosítója a cél NSG erőforrás-azonosítója.
> * a tárolóazonosítót a cél Storage-fiók erőforrás-azonosítója.


## <a name="deploying-your-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon üzembe helyezése

Ez az oktatóanyag feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és egy NSG, amelyen engedélyezheti a folyamat naplózását.
A fenti példák bármelyikét helyileg is mentheti `azuredeploy.json` . Frissítse a tulajdonságértékek értékét úgy, hogy az előfizetése érvényes erőforrásokra mutasson.

A sablon üzembe helyezéséhez futtassa a következő parancsot a PowerShellben.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> A fenti parancsok egy erőforrást helyeznek üzembe a NetworkWatcherRG erőforráscsoporthoz, és nem a NSG tartalmazó erőforráscsoportot.


## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Több módon is ellenőrizhető, hogy sikeres volt-e a központi telepítés. A PowerShell-konzol a "ProvisioningState" kifejezést "sikeres" értékre kell mutatnia. Emellett a [NSG flow-naplók portál oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) is megtekintheti a módosításokat. Ha probléma merült fel az üzembe helyezéssel kapcsolatban, tekintse meg a [gyakori Azure-telepítési hibák elhárítását Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Erőforrás törlése
Az Azure lehetővé teszi az erőforrások törlését a "teljes" üzembe helyezési módban. A flow-naplók erőforrásának törléséhez adja meg a telepítést teljes módban anélkül, hogy a törölni kívánt erőforrást is tartalmazza. További információ a [teljes telepítési módból](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Azt is megteheti, hogy az alábbi lépések alapján letilthatja a NSG-flow naplóját Azure Portal:
1. Belépés az Azure Portalba
2. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
3. A **naplók**területen válassza a **NSG flow-naplók** lehetőséget.
4. A NSG listából válassza ki azt a NSG, amely számára le szeretné tiltani a folyamat naplóit
5. A **flow-naplók beállításai**alatt állítsa be a folyamatok naplójának állapota **kikapcsolva** értéket.
6. Görgessen le, és válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban engedélyezte a NSG folyamat naplóit. Most meg kell tanulnia, hogyan jelenítheti meg a NSG folyamat adatait a következő használatával: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure-Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
