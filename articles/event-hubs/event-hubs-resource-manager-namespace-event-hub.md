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
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457086"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Event Hubs-névtér létrehozása az Azure Resource Manager-sablon használatával event hub és a fogyasztói csoport
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ebben a rövid útmutatóban létrehozhat egy eseményközpontba, egy Azure Resource Manager-sablon használatával. Az Azure Resource Manager-sablon használatával hozzon létre egy névteret típusú [az Event Hubs](event-hubs-what-is-event-hubs.md), és az egy eseményközponttal és a egy fogyasztói csoporton. A cikk bemutatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Sablonok létrehozásával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

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

A központi telepítés automatikusan futtatja, válasszon az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>Paraméterek megadása
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

## <a name="define-resources-to-deploy"></a>Adja meg az üzembe helyezendő erőforrások

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

## <a name="azure-powershell"></a>Azure PowerShell
Az Azure PowerShell-lel erőforrások üzembe helyezéséhez futtassa a következő parancsot:

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
Az Azure PowerShell-lel erőforrások üzembe helyezéséhez futtassa a következő parancsot:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
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
