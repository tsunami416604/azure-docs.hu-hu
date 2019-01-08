---
title: Hozzon létre az Azure Service Bus-erőforrások Resource Manager-sablonokkal |} A Microsoft Docs
description: Automatizálható a Service Bus-erőforrások létrehozása Azure Resource Manager-sablonok használatával
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: e23173b006fcc83f0e4b30b59a65e772b68a612d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062009"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Hozzon létre a Service Bus-erőforrások Azure Resource Manager-sablonok használatával

Ez a cikk ismerteti, hogyan hozhat létre, és üzembe helyezése a Service Bus-erőforrások Azure Resource Manager-sablonok, a PowerShell és a Service Bus erőforrás-szolgáltató használatával.

Az Azure Resource Manager-sablonok segítségével meghatározhatja a az erőforrásokat a megoldás üzembe helyezése, és adja meg a paramétereket és változókat, amelyek segítségével beviheti a különböző környezetekhez tartozó értékeket. A sablon JSON nyelven van megírva, és a következő kifejezések, amelyek segítségével kialakíthatja az üzemelő példány értékeit tartalmazza. Az Azure Resource Manager-sablonok és a sablon formátuma hatásának a megbeszélését írt kapcsolatos részletes információkért lásd: [szerkezetének és szintaxisának az Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Ebben a cikkben szereplő példák bemutatják, hogyan hozzon létre egy Service Bus-névtér és üzenetküldési entitások (üzenetsor) az Azure Resource Manager használatával. Egyéb sablon példákat talál a [Azure gyorsindítási sablonok katalógusában] [ Azure Quickstart Templates gallery] és keressen rá a **a Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager-sablonok

A Service Bus Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el. Részletes ismertetése, a github-sablonokra mutató hivatkozásokat a következő hivatkozásokra kattintva:

* [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
* [Service Bus-névtér létrehozása az üzenetsorhoz](service-bus-resource-manager-namespace-queue.md)
* [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
* [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Az alábbi eljárás ismerteti, hogyan lehet üzembe helyezése Azure Resource Manager-sablon, amely létrehoz egy Standard szintű Service Bus-névtér és a egy adott névtéren belül üzenetsorba a PowerShell használatával. Ez a példa alapján a [várakozási sorral rendelkező Service Bus-névtér létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) sablont. A hozzávetőleges munkafolyamat a következőképpen történik:

1. Telepítse a PowerShell.
2. Hozzon létre a sablont, és (opcionálisan) egy alkalmazásparaméter-fájlt.
3. A PowerShellben jelentkezzen be az Azure-fiókjával.
4. Hozzon létre egy új erőforráscsoportot, ha még nem létezik.
5. A telepítés tesztelésére.
6. Ha szükséges, állítsa be az üzembe helyezési mód.
7. A sablon üzembe helyezéséhez.

Az Azure Resource Manager-sablonokkal való telepítéséről részletes információkért lásd: [erőforrások Azure Resource Manager-sablonok üzembe helyezése][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>A PowerShell telepítése

Azure PowerShell telepítéséhez a következő témakör utasításait követve [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Sablon létrehozása

Klónozza a tárházban vagy a példány a [201-servicebus--várólista létrehozása](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) sablont a Githubból:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>A paraméterfájl létrehozása (opcionális)

Egy választható paramétereket tartalmazó fájlt használ, másolja át a [201-servicebus--várólista létrehozása](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) fájlt. Értékét cserélje `serviceBusNamespaceName` szeretné a központi telepítés létrehozása, és értékét cserélje le a Service Bus-névtér nevét `serviceBusQueueName` szeretne létrehozni az üzenetsor nevére.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

További információkért lásd: a [paraméterek](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) cikk.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure-ba, és állítsa be az Azure-előfizetés

Egy PowerShell-parancssorban futtassa a következő parancsot:

```powershell
Connect-AzureRmAccount
```

Jelentkezzen be az Azure-fiókja kéri. A bejelentkezés után futtassa a következő parancsot a rendelkezésre álló előfizetések megtekintéséhez:

```powershell
Get-AzureRMSubscription
```

Ez a parancs elérhető Azure-előfizetések listáját adja vissza. Válasszon egy előfizetést, az aktuális munkamenet a következő parancs futtatásával. Cserélje le `<YourSubscriptionId>` használni kívánt Azure-előfizetéshez tartozó GUID Azonosítóval rendelkező:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Állítsa be az erőforráscsoport

Ha nem rendelkezik egy meglévő erőforrást, csoport, hozzon létre egy új erőforráscsoportot a ** New-AzureRmResourceGroup ** parancsot. Adja meg az erőforráscsoportot és helyet használni kívánt nevét. Példa:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Ha ez sikeres, az új erőforráscsoport összegzését jelenik meg.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

A telepítés ellenőrzése futtatásával a `Test-AzureRmResourceGroupDeployment` parancsmagot. A központi telepítés tesztelésekor paramétereket megadnia, pontosan, mint a központi telepítés végrehajtása közben.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Az üzemelő példány létrehozása

Az új központi telepítés létrehozásához futtassa a `New-AzureRmResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméterek tartalmaznia kell egy nevet az üzembe helyezés a sablonfájl az erőforráscsoport és az elérési útja vagy URL-cím nevére. Ha a **mód** paraméter nincs megadva, az alapértelmezett érték **növekményes** szolgál. További információkért lásd: [növekményes és teljes körű központi telepítések](../azure-resource-manager/deployment-modes.md).

A következő parancs kéri a három paramétert a PowerShell-ablakban:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Ehelyett adja meg a paramétereket tartalmazó fájlt, használja a következő parancsot:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Emellett használhatja a beágyazott paraméterek, a központi telepítési parancsmag futtatásakor. A parancs a következőképpen történik:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Futtatásához egy [teljes](../azure-resource-manager/deployment-modes.md) központi telepítését, állítsa be a **mód** paramétert **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése
Ha az erőforrások telepítése sikeresen megtörtént, a központi telepítés összegzését a PowerShell-ablakban jelenik meg:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>További lépések
Most láthatta az alapvető munkafolyamat és a parancsok az Azure Resource Manager-sablon üzembe helyezéséhez. További részletes információkért látogasson el az alábbi hivatkozásokat:

* [Az Azure Resource Manager áttekintése][Azure Resource Manager overview]
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)
* [Microsoft.ServiceBus erőforrástípusok](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
