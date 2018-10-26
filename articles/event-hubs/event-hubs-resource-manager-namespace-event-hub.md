---
title: Hozzon létre egy Azure Event Hubs névtér és a fogyasztói csoportot egy sablon használatával |} A Microsoft Docs
description: Event Hubs-névtér létrehozása egy eseményközponttal, valamint egy fogyasztói csoportot az Azure Resource Manager-sablonok használatával
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 584696303bfbaed07f416fb0b3febbcf59d05b35
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085739"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Gyors útmutató: Azure Resource Manager-sablon használatával eseményközpont létrehozása
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ebben a rövid útmutatóban létrehozhat egy eseményközpontba, egy Azure Resource Manager-sablon használatával. Az Azure Resource Manager-sablon használatával hozzon létre egy névteret típusú [az Event Hubs](event-hubs-what-is-event-hubs.md), és az egy eseményközponttal és a egy fogyasztói csoporton. A cikk bemutatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Sablonok létrehozásával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].


> [!NOTE]
> A teljes sablont, tekintse meg a [Event hub és a fogyasztói csoport sablon] [ Event Hub and consumer group template] a Githubon. Ez a sablon mellett az event hub-névtér és eseményközpont fogyasztói csoportot hozott létre. A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

Ha a használni kívánt **Azure PowerShell-lel** a Resource Manager-sablon üzembe helyezéséhez [Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Ha a használni kívánt **Azure CLI-vel** a Resource Manager-sablon üzembe helyezéséhez [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>A Resource Manager-sablon JSON létrehozása
Hozzon létre egy JSON-fájlt MyEventHub.json az alábbi tartalommal, és mentse egy mappába (Példa: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Hozza létre a paramétereket JSON
Hozzon létre egy Azure Resource Manager-sablonja paramétereket tartalmaz MyEventHub Parameters.json nevű JSON-fájlt. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
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

### <a name="provision-resources"></a>Erőforrások kiosztása
Az Azure PowerShell-lel erőforrások üzembe helyezése/kiépítését, váltson át a C:\EventHubsQuickStart\ARM\ mappában futtassa a következő parancsokat:

> [!IMPORTANT]
> Adja meg az Azure-erőforráscsoport nevét értékként $resourceGroupName parancsok futtatása előtt. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>A sablon üzembe helyezése az Azure CLI használatával

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az alábbi lépések nem szükségesek, ha a parancsokat a Cloud Shellben futtatja. Ha helyileg futtatja a CLI-t, hajtsa végre az alábbi lépéseket, jelentkezzen be az Azure-ba, és állítsa be a jelenlegi előfizetésében:

Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

```azurecli
az login
```

Állítsa be az aktuális előfizetési környezetet. A `MyAzureSub` értéket cserélje le a használni kívánt Azure-előfizetés nevére:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Erőforrások kiosztása
Az Azure CLI-vel erőforrásokat üzembe kívánja, váltson arra a mappára, C:\EventHubsQuickStart\ARM\, és futtassa a következő parancsokat:

> [!IMPORTANT]
> Adjon meg egy nevet, az Azure-erőforráscsoportot az csoport paranccsal hozzon létre. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Gratulálunk! Az Azure Resource Manager-sablon létrehozása az Event Hubs-névtér és eseményközpont adott névtéren belül használt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott Event Hubs-névtér és a mintaalkalmazások használt események küldéséhez és fogadásához az eseményközpontból. Események küldése (vagy) események fogadása az event hubs részletes útmutatás a következő oktatóanyagokban talál: 

- **Események küldése eseményközpontba**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js ](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Események fogadása az event hub**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ NODE.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
