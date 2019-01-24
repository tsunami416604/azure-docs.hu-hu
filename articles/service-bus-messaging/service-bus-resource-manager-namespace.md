---
title: Hozzon létre a Service Bus üzenetkezelési névteret Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Azure Resource Manager-sablon használatával létrehozhat egy Service Bus Messaging-névteret
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: e925ea91518d8f093cd270e238b7ffd09674e726
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844092"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Hozzon létre egy Azure Resource Manager-sablon használatával a Service Bus-névtér
Ebben a rövid útmutatóban létrehozhat egy Azure Resource Manager-sablon, amely létrehozza a Service Bus-névtér, típus **üzenetkezelés** együtt egy **Standard** Termékváltozat. A cikk azt is meghatározza, a megadott paraméterek, a telepítés végrehajtására. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál. A teljes sablont, tekintse meg a [a Service Bus-névtér sablon] [ Service Bus namespace template] a Githubon.

> [!NOTE]
> Az alábbi Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el. 
> 
> * [Service Bus-névtér létrehozása az üzenetsorhoz](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonokat keressen, látogasson el a [Azure gyorsindítási sablonok] [ Azure Quickstart Templates] katalógusban, és keresse meg a Service Bus.

## <a name="quick-deployment"></a>Gyors üzembe helyezés
Bármely JSON írása, és a PowerShell vagy a parancssori felületen parancs futtatása nélkül a minta futtatásához válasszon az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Hozhat létre és helyezheti üzembe a sablont manuálisan, nyissa meg a következő szakaszok ebben a cikkben.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

Ha a használni kívánt **Azure PowerShell-lel** a Resource Manager-sablon üzembe helyezéséhez [Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Ha a használni kívánt **Azure CLI-vel** a Resource Manager-sablon üzembe helyezéséhez [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>A Resource Manager-sablon JSON létrehozása 
Hozzon létre egy JSON-fájlt **MyServiceBusNamespace.json** az alábbi tartalommal: 

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
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Ez a sablon létrehoz egy standard szintű Service Bus-névteret. JSON-szintaxist és a Tulajdonságok [névterek](/azure/templates/microsoft.servicebus/namespaces) tárfióksablonok referenciáját.

## <a name="create-the-parameters-json"></a>Hozza létre a paramétereket JSON
Az előző lépésben létrehozott sablont rendelkezik nevű szakaszban `Parameters`. Meghatározhat paramétereket ezeket az értékeket, amelyek alapján a projekthez telepíti, vagy a célkörnyezet alapján. Ez a sablon meghatározza a következő paramétereket: **serviceBusNamespaceName**, **serviceBusSku**, és **hely**. A Service Bus termékváltozatok kapcsolatos további információkért lásd: [Service Bus-termékváltozatok](https://azure.microsoft.com/pricing/details/service-bus/) hozhat létre.

Hozzon létre egy JSON-fájlt **MyServiceBusNamespace-Parameters.json** az alábbi tartalommal: 

> [!NOTE] 
> Adja meg a Service Bus-névtér nevét. 


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>A sablon üzembe helyezése az Azure PowerShell használatával

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
1. Indítsa el az Azure PowerShell-lel

2. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Ha rendelkezik adja ki az alábbi parancsokat az aktuális előfizetési környezetet:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Erőforrások üzembe helyezése
Az Azure PowerShell-lel erőforrásokat üzembe kívánja, váltson arra a mappára, ahová mentette a JSON-fájlokat, és futtassa a következő parancsokat:

> [!IMPORTANT]
> Adja meg az Azure-erőforráscsoport nevét értékként $resourceGroupName parancsok futtatása előtt. 

1. Deklaráljon egy változót az erőforráscsoport nevét, és hozzá tartozó érték adható meg. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Azure-erőforráscsoport létrehozása

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. A Resource Manager-sablon üzembe helyezéséhez. Adja meg a nevek, üzembe helyezéséhez tartoznak, erőforráscsoport, a sablon JSON-fájlt a paramétereket JSON-fájlt

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>A sablon üzembe helyezése az Azure CLI használatával

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

    ```azurecli
    az login
    ```
2. Állítsa be az aktuális előfizetési környezetet. A `MyAzureSub` értéket cserélje le a használni kívánt Azure-előfizetés nevére:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Erőforrások üzembe helyezése
Az Azure CLI-vel erőforrások üzembe helyezéséhez a JSON-fájlok váltson arra a mappára, és futtassa a következő parancsokat:

> [!IMPORTANT]
> Adjon meg egy nevet, az Azure-erőforráscsoportot az csoport paranccsal hozzon létre. .

1. Azure-erőforráscsoport létrehozása 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. A Resource Manager-sablon üzembe helyezéséhez. Adja meg az erőforráscsoport, telepítés, a sablon JSON-fájlt, paraméterek JSON-fájl nevét.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott Service Bus-névtér. A többi rövid útmutató megtudhatja, hogyan hozhat létre az üzenetsorok, üzenettémák, előfizetések, tekintse meg, és használja őket: 

- [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
- [Ismerkedés a Service Bus-témakörök](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
