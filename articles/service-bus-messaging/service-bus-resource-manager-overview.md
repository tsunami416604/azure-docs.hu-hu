---
title: Azure Service Bus-erőforrások létrehozása sablonok használatával
description: Az Azure Resource Manager-sablonok használatával automatizálhatja a Service Bus-erőforrások létrehozását
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
ms.openlocfilehash: 9bc784ee57b9bde393408cbefa9a197aebc59b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264458"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Service Bus-erőforrások létrehozása az Azure Resource Manager-sablonokkal

Ez a cikk ismerteti, hogyan hozhat létre és helyezhet üzembe Service Bus-erőforrások at Azure Resource Manager-sablonok, PowerShell és a Service Bus erőforrás-szolgáltató használatával.

Az Azure Resource Manager-sablonok segítségével meghatározhatja a megoldás üzembe helyezéséhez szükséges erőforrásokat, és megadhatja azokat a paramétereket és változókat, amelyek lehetővé teszik a különböző környezetek értékeinek bevitelét. A sablon JSON-ban íródott, és olyan kifejezésekből áll, amelyek segítségével értékeket hozhat létre a központi telepítéshez. Az Azure Resource Manager-sablonok írásával és a sablonformátummal kapcsolatos részletes információkért tekintse meg [az Azure Resource Manager-sablonok szerkezetét és szintaxisát.](../azure-resource-manager/templates/template-syntax.md)

> [!NOTE]
> Ebben a cikkben példák bemutatják, hogyan azure Resource Manager segítségével hozzon létre egy Service Bus névtér és üzenetkezelő entitás (várólista). További sablonpéldákért látogasson el az [Azure gyorsútmutatósablonok gyűjteményébe,][Azure Quickstart Templates gallery] és keresse meg a **Service Bus kifejezést.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Service Bus Erőforrás-kezelő sablonjai

Ezek a Service Bus Azure Resource Manager-sablonok letölthetők és üzembe helyezésére. Az alábbi hivatkozásokra kattintva a GitHubsablonjaira mutató hivatkozásokat talál:

* [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
* [Service Bus-névtér létrehozása várólistával](service-bus-resource-manager-namespace-queue.md)
* [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
* [Service Bus-névtér létrehozása várólista- és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Az alábbi eljárás azt ismerteti, hogyan használhatja a PowerShellt egy Azure Resource Manager-sablon üzembe helyezéséhez, amely létrehoz egy standard szintű Service Bus-névteret, és egy várólistát a névtérben. Ez a példa a [Szolgáltatásbusz létrehozása névtér létrehozása várólistasablonnal.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) A hozzávetőleges munkafolyamat a következő:

1. Telepítse a PowerShellt.
2. Hozza létre a sablont és (tetszés szerint) egy paraméterfájlt.
3. A PowerShellben jelentkezzen be az Azure-fiókjába.
4. Hozzon létre egy új erőforráscsoportot, ha nem létezik.
5. Tesztelje az üzembe helyezést.
6. Ha szükséges, állítsa be a telepítési módot.
7. Telepítse a sablont.

Az Azure Resource Manager-sablonok üzembe helyezéséről az [Erőforrások üzembe helyezése az Azure Resource Manager-sablonokkal című témakörben][Deploy resources with Azure Resource Manager templates]olvashat.

### <a name="install-powershell"></a>A PowerShell telepítése

Telepítse az Azure PowerShellt az [Azure PowerShell használatával való ismerkedés](/powershell/azure/get-started-azureps)című témakör utasításainak követésével.

### <a name="create-a-template"></a>Sablon létrehozása

Klónozza a tárházat, vagy másolja a [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) sablont a GitHubról:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

### <a name="create-a-parameters-file-optional"></a>Paraméterfájl létrehozása (nem kötelező)

Választható paraméterfájl használatához másolja a [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) fájlt. Cserélje le `serviceBusNamespaceName` a központi telepítésben létrehozni kívánt Service Bus névtér nevét, és `serviceBusQueueName` cserélje le a létrehozni kívánt várólista nevére.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

További információt a [Paraméterek](../azure-resource-manager/templates/parameter-files.md) című cikkben talál.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure-ba, és állítsa be az Azure-előfizetést

Egy PowerShell-parancssorból futtassa a következő parancsot:

```powershell
Connect-AzAccount
```

A rendszer kéri, hogy jelentkezzen be az Azure-fiókjába. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

```powershell
Get-AzSubscription
```

Ez a parancs az elérhető Azure-előfizetések listáját adja vissza. Válasszon előfizetést az aktuális munkamenethez a következő parancs futtatásával. Cserélje `<YourSubscriptionId>` le a használni kívánt Azure-előfizetés GUID azonosítójára:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Az erőforráscsoport beállítása

Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy új erőforráscsoportot a **New-AzResourceGroup** paranccsal. Adja meg a használni kívánt erőforráscsoport és hely nevét. Példa:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Ha sikeres, megjelenik az új erőforráscsoport összegzése.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Ellenőrizze a központi `Test-AzResourceGroupDeployment` telepítést a parancsmag futtatásával. A központi telepítés tesztelése során adja meg a paramétereket pontosan úgy, ahogy a központi telepítés végrehajtásakor.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>A központi telepítés létrehozása

Az új központi telepítés `New-AzResourceGroupDeployment` létrehozásához futtassa a parancsmast, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméterek tartalmazzák a központi telepítés nevét, az erőforráscsoport nevét, valamint a sablonfájl elérési útját vagy URL-címét. Ha a **Mode** paraméter nincs megadva, a **növekményes** alapértelmezett értéket használja a program. További információ: [Növekményes és teljes telepítések.](../azure-resource-manager/templates/deployment-modes.md)

A következő parancs a PowerShell-ablakban a három paramétert kéri:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

A paraméterfájl megadásához használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

A központi telepítési parancsmag futtatásakor szövegközi paramétereket is használhat. A parancs formátuma a következő:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[A teljes](../azure-resource-manager/templates/deployment-modes.md) telepítés futtatásához állítsa a **Mód** paramétert **Befejezés**beállításra:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>A telepítés ellenőrzése
Ha az erőforrások telepítése sikeresen megtörtént, a központi telepítés összegzése jelenik meg a PowerShell ablakban:

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
Most már látta az Azure Resource Manager-sablon üzembe helyezéséhez szükséges alapvető munkafolyamatot és parancsokat. További információkért látogasson el az alábbi linkekre:

* [Az Azure Resource Manager áttekintése][Azure Resource Manager overview]
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sablonok készítése](../azure-resource-manager/templates/template-syntax.md)
* [Microsoft.ServiceBus erőforrástípusok](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
