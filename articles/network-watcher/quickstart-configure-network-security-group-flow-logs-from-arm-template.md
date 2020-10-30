---
title: 'Gyors útmutató: NSG-adatforgalmi naplók konfigurálása Azure Resource Manager sablon használatával'
description: Megtudhatja, hogyan engedélyezheti programozott módon a NSG-adatforgalmi naplókat egy Azure Resource Manager sablon (ARM-sablon) és Azure PowerShell használatával.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042759"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Gyors útmutató: NSG-adatforgalmi naplók konfigurálása ARM-sablon használatával

Ebben a rövid útmutatóban egy [Azure Resource Manager](../azure-resource-manager/management/overview.md) sablon (ARM-sablon) és Azure PowerShell használatával engedélyezheti a [NSG folyamat naplóit](network-watcher-nsg-flow-logging-overview.md) .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Kezdjük azzal, hogy áttekintjük a NSG flow log objektum tulajdonságait, amelyet néhány sablon követ. Ezután a sablon üzembe helyezése egy helyi PowerShell-példány használatával.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create) közül származik.

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Több erőforrás van definiálva a sablonban:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/üzemelő példányok](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG-folyamat naplófájljainak objektuma

Az NSG flow az összes paraméterrel rendelkező objektumot alább mutatja. A tulajdonságok teljes áttekintését lásd: [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Erőforrás létrehozásához `Microsoft.Network/networkWatchers/flowLogs` adja hozzá a fenti JSON-t a sablon erőforrások szakaszához.

## <a name="creating-your-template"></a>A sablon létrehozása

Ha első alkalommal használ ARM-sablonokat, többet is megtudhat ezekről az alábbi hivatkozásokat használva.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create) közül származik.

Az alábbi példában a teljes sablon a NSG-naplók beállításához megadott minimális paraméterekkel rendelkező legegyszerűbb verzió. További példákért látogasson el erre a [útmutatóra](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Példa** : az alábbi sablon lehetővé teszi, hogy a NSG a cél NSG, és tárolja őket egy adott Storage-fiókban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Az erőforrás neve a _szülő Resource_Child erőforrást_ formázza. Itt a szülő erőforrás a regionális Network Watcher-példány (Format: NetworkWatcher_RegionName. Példa: NetworkWatcher_centraluseuap)
> - `targetResourceId` a cél NSG erőforrás-azonosítója.
> - `storageId` a cél Storage-fiók erőforrás-azonosítója.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ez az oktatóanyag feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és egy NSG, amelyen engedélyezheti a folyamat naplózását.
A fenti példák bármelyikét helyileg is mentheti `azuredeploy.json` . Frissítse a tulajdonságértékek értékét úgy, hogy az előfizetése érvényes erőforrásokra mutasson.

A sablon üzembe helyezéséhez futtassa a következő parancsot a PowerShellben.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> A fenti parancsok egy erőforrást helyeznek üzembe a NetworkWatcherRG erőforráscsoporthoz, és nem a NSG tartalmazó erőforráscsoportot.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Több módon is ellenőrizhető, hogy sikeres volt-e a központi telepítés. A PowerShell-konzolnak a következőnek kell lennie: `ProvisioningState` `Succeeded` . Emellett a [NSG flow-naplók portál oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) is megtekintheti a módosításokat. Ha probléma merült fel a központi telepítéssel kapcsolatban, tekintse meg a [gyakori Azure-telepítési hibák elhárítása a Azure Resource Managerkal](../azure-resource-manager/templates/common-deployment-errors.md)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

Az Azure lehetővé teszi az erőforrás-törlést az `Complete` üzembe helyezési módban. A flow-naplók erőforrásának törléséhez olyan üzemmódot kell megadnia, amely `Complete` nem tartalmazza a törölni kívánt erőforrást. További információ a [teljes telepítési módból](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Azt is megteheti, hogy az alábbi lépések alapján letilthatja a NSG-flow naplóját Azure Portal:

1. Belépés az Azure Portalba
1. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a _Network Watcher_ kifejezést. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
1. A **naplók** területen válassza a **NSG flow-naplók** lehetőséget.
1. A NSG listából válassza ki azt a NSG, amelynek le szeretné tiltani a folyamat naplóit.
1. A **flow-naplók beállításai** alatt állítsa be a folyamatok naplójának állapota **kikapcsolva** értéket.
1. Görgessen le, és válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban engedélyezte a NSG folyamat naplóit. Most meg kell tanulnia, hogyan jelenítheti meg a NSG folyamat adatait a következő használatával:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Nyílt forráskódú eszközök](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure-Traffic Analytics](traffic-analytics.md)
