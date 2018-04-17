---
title: Azure Service Bus erőforrásainak használata a Resource Manager-sablonok létrehozása |} Microsoft Docs
description: Service Bus erőforrásainak automatizálhatja az Azure Resource Manager-sablonok segítségével
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: 876b1c30c09f31958555ff3dee769d8dd443a60d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Service Bus erőforrásainak használata Azure Resource Manager-sablonok létrehozása

Ez a cikk ismerteti, hogyan hozhat létre és telepíthet a Service Bus erőforrásainak Azure Resource Manager sablonok, PowerShell és a Service Bus erőforrás-szolgáltató használatával.

Az Azure Resource Manager-sablonok segítségével határozza meg az erőforrásokat, a megoldás központi telepítéséhez, és adja meg a paramétereket és változókat, amelyek segítségével beviheti a különböző környezetekhez tartozó értékeket. A sablon JSON nyelven van megírva, és az üzemelő példány értékeit összeállításához használó kifejezéseket tartalmaz. Azure Resource Manager-sablonok és a sablon formátum döntéseken írt kapcsolatos részletes információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Ebben a cikkben szereplő példák bemutatják, hogyan hozzon létre egy Service Bus-névtér és üzenetküldési entitásra (várólista) az Azure Resource Manager használatával. Más sablon példákat látogasson el a [Azure gyors üzembe helyezés sablontárban] [ Azure Quickstart Templates gallery] keresse meg a **Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager-sablonok

A Service Bus Azure Resource Manager sablonok letöltése és központi telepítés érhetők el. A következő hivatkozásokra kattintva minden egy, a sablonok a Githubon mutató hivatkozásokat tartalmaz információt:

* [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
* [Hozzon létre egy Service Bus-névtér várólista](service-bus-resource-manager-namespace-queue.md)
* [Hozzon létre egy Service Bus-névtér témakör és előfizetés](service-bus-resource-manager-namespace-topic.md)
* [Hozzon létre egy Service Bus-névtér várólista és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
* [A témakör, előfizetés és a szabály a Service Bus-névtér létrehozása](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Az alábbi eljárás ismerteti, hogyan lehet egy Service Bus-névtér Standard csomagra, és az adott névtérben várólista létrehozó Azure Resource Manager-sablon üzembe helyezése a PowerShell használatával. Ez a példa alapján a [hozzon létre egy Service Bus-névtér várólista](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) sablont. A hozzávetőleges munkafolyamata a következőképpen történik:

1. Telepítse a PowerShell.
2. A sablont, és (opcionálisan) a paraméterfájl létrehozása.
3. A PowerShellben jelentkezzen be az Azure-fiókjával.
4. Ha még nem létezik, hozzon létre egy új erőforráscsoportot.
5. A telepítés tesztelésére.
6. Ha szükséges, állítsa be a rendszerbe állítási mód.
7. A sablon telepítéséhez.

Azure Resource Manager-sablonok telepítésével kapcsolatos részletes információkért lásd: [telepítése Azure Resource Manager-sablonok erőforrások][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>A PowerShell telepítése

Azure PowerShell telepítése utasításait követve [Ismerkedés az Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Sablon létrehozása

Klónozza a tárházat vagy másolása a [201-szolgáltatásbusz--várólista létrehozása](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) sablont a Githubból:

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

### <a name="create-a-parameters-file-optional"></a>Hozzon létre egy paraméterfájl (nem kötelező)

Az opcionális paraméterek fájlt használ, másolja a [201-szolgáltatásbusz--várólista létrehozása](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) fájlt. Cserélje le a `serviceBusNamespaceName` nevű, a Service Bus-névtér létrehozása ebben a telepítésben, és cserélje le a kívánt `serviceBusQueueName` szeretne létrehozni a várólista nevét.

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

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Jelentkezzen be az Azure és az Azure-előfizetés beállítása

Egy PowerShell-parancssorba futtassa a következő parancsot:

```powershell
Login-AzureRmAccount
```

Jelentkezzen be az Azure-fiókjával kéri. A bejelentkezés után futtassa a következő parancsot az elérhető előfizetések megtekintéséhez:

```powershell
Get-AzureRMSubscription
```

Ez a parancs elérhető Azure-előfizetések listáját adja vissza. Válasszon egy előfizetést, az aktuális munkamenet a következő parancs futtatásával. Cserélje le `<YourSubscriptionId>` használni kívánt Azure-előfizetés GUID:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Az erőforráscsoport beállítása

Ha a csoport, hozzon létre egy új erőforráscsoportot a meglévő erőforrás még a ** New-AzureRmResourceGroup ** parancsot. Adja meg az erőforráscsoportot és helyet használni kívánt nevét. Példa:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Ha sikeres, az új erőforráscsoport összegzését jelenik meg.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

A központi telepítés futtatásával ellenőrizze a `Test-AzureRmResourceGroupDeployment` parancsmag. Ha a központi telepítés tesztelése, adja meg a paramétereket pontosan ugyanúgy, a központi telepítés végrehajtása közben.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>A központi telepítés létrehozásához

Az új központi telepítés létrehozásához futtassa a `New-AzureRmResourceGroupDeployment` parancsmagot, és adja meg a szükséges paramétereket, amikor a rendszer kéri. A paraméternek számít a központi telepítésre, az erőforráscsoport és az elérési út vagy URL neve a sablon fájl nevét. Ha a **mód** paraméter nincs megadva, az alapértelmezett érték **növekményes** szolgál. További információkért lásd: [növekményes és teljes telepítések](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

A következő parancs bekéri a PowerShell-ablakban három paramétert:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Ehelyett adja meg egy paraméterfájl, használja a következő parancsot:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Beágyazott paramétereket használhatja a központi telepítési parancsmag futtatása esetén is. A parancs a következőképpen történik:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Futtatásához egy [teljes](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) központi telepítését, állítsa be a **mód** paramétert **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>A telepítés ellenőrzése
Ha az erőforrások telepítése sikeres volt, a központi telepítés összegzését a PowerShell-ablakban jelenik meg:

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
Most láthatta, a munkafolyamat alapvető és parancsok telepítése Azure Resource Manager-sablonok. További részletes információkért látogasson el az alábbi hivatkozásokra:

* [Az Azure Resource Manager áttekintése][Azure Resource Manager overview]
* [Erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
