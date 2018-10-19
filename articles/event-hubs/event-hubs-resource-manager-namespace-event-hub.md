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
ms.openlocfilehash: e3a7b2a7ad866fe6c70c638dc5203b9a31c6b5b3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426634"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Event Hubs-névtér létrehozása az Azure Resource Manager-sablon használatával event hub és a fogyasztói csoport
Az Azure Event Hubs egy kiválóan méretezhető adatstreamelési platform és feldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Ez a rövid útmutató bemutatja, hogyan hozzon létre egy eseményközpontot, az Azure Resource Manager-sablon használatával.

Az Azure Resource Manager-sablon használatával hozzon létre egy névteret típusú [az Event Hubs](event-hubs-what-is-event-hubs.md), és az egy eseményközponttal és a egy fogyasztói csoporton. A cikk bemutatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Sablonok létrehozásával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablont, tekintse meg a [Event hub és a fogyasztói csoport sablon] [ Event Hub and consumer group template] a Githubon.

> [!NOTE]
> A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot] [-] megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha használ **Azure PowerShell-lel** a Resource Manager-sablon üzembe helyezése helyi, futtatnia kell a rövid útmutató elvégzéséhez a PowerShell legújabb verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Ha úgy dönt, telepítése és használata **Azure CLI-vel** a Resource Manager-sablon üzembe helyezéséhez helyileg, az oktatóanyaghoz, hogy futnak-e az Azure CLI 2.0.4-es vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezzel a sablonnal egy Event Hubs-névtér egy eseményközponttal, valamint egy fogyasztói csoport üzembe helyezése.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Olyan értékeket, amelyek eltérnek a projekt telepítésekor vagy a környezet, amely telepíti alapján paramétert meg kell határozni. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezett erőforrásokat határozza meg.

A sablon meghatározza a következő:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

A létrehozandó Event Hubs-névtér neve.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Az Event Hubs-névtérben létrehozott eseményközpont neve.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

A létrehozott eseményközpont fogyasztói csoport neve.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

A sablon API-verziója.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások

Létrehoz egy típusú névteret **EventHubs**, és az eseményközpont és a egy fogyasztói csoportot:

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```
### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára

Az Event Processor Host leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az ellenőrzőpontokat és a párhuzamos fogadókat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. A tárfiók létrehozásához és a hozzá tartozó kulcsok lekéréséhez futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

Az eseményközponthoz való csatlakozáshoz és az események feldolgozásához szükség van egy kapcsolati sztringre. A kapcsolati sztring lekéréséhez futtassa a következőt:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```


## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára
```azurecli-interactive
# Create a general purpose standard storage account for Event Processor Host
az storage account create --name storageAccountName --resource-group myResourceGroup --location eastus2 --sku Standard_RAGRS --encryption blob

# List the storage account access keys
az storage account keys list --resource-group myResourceGroup --account-name storageAccountName

# Get namespace connection string
az eventhubs namespace authorization-rule keys list --resource-group myResourceGroup --namespace-name namespaceName --name RootManageSharedAccessKey
```

Másolja ki és illessze be a kapcsolati sztringet egy átmeneti helyre, például a Jegyzettömbbe, mert később szüksége lesz rá.

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Most már elkezdhet streamelni az Event Hubsba. A mintákat letöltheti vagy Git-klónozhatja az [Event Hubs-adattárból](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Események betöltése

Az események streamelésének megkezdéséhez töltse le a [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) mintát a GitHubról, vagy klónozza az [Event Hubs GitHub-adattárát](https://github.com/Azure/azure-event-hubs) a következő paranccsal:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Lépjen az \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender mappába, és töltse be a SampleSender.sln fájlt a Visual Studióba.

Ezután adja hozzá a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-csomagot a projekthez.

A Program.cs fájlban cserélje le a következő helyőrzőket az eseményközpont nevére és a kapcsolati sztringre:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Hozza létre és futtassa a mintát. Láthatja, ahogy az események betöltődnek az eseményközpontba:

![][3]

### <a name="receive-and-process-events"></a>Események fogadása és feldolgozása

Most töltse le az Event Processor Host-fogadómintát, amely fogadja az elküldött üzeneteket. Töltse le a [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) mintát a GitHubról, vagy klónozza az [Event Hubs GitHub-adattárát](https://github.com/Azure/azure-event-hubs) a következő paranccsal:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Lépjen az \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver mappába, és töltse be a SampleEphReceiver.sln megoldásfájlt a Visual Studióba.

Ezután adja hozzá a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) és a [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) NuGet-csomagokat a projekthez.

A Program.cs fájlban cserélje le a következő állandókat a megfelelő értékekre:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Hozza létre és futtassa a mintát. Láthatja, ahogy a mintaalkalmazás fogadja az eseményeket:

![][4]

Az Azure Portalon az itt látható módon tekintheti meg az események feldolgozási sebességét egy adott Event Hubs-névtérnél:

![][5]


## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott Event Hubs-névtér és a mintaalkalmazások használt események küldéséhez és fogadásához az eseményközpontból. Események küldése (vagy) események fogadása az event hubs részletes útmutatás a következő oktatóanyagokban talál: 

1. **Események küldése eseményközpontba**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js ](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
2. **Események fogadása az event hub**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ NODE.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
