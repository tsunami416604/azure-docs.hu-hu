---
title: Hozzon létre egy Azure Event Hubs névtér és a fogyasztói csoportot egy sablon használatával |} A Microsoft Docs
description: Event Hubs-névtér létrehozása egy eseményközponttal, valamint egy fogyasztói csoportot az Azure Resource Manager-sablonok használatával
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 4b4dc5be9697bb96aec658fccbdf13b299e79e9e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309851"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Event Hubs-névtér létrehozása az Azure Resource Manager-sablon használatával event hub és a fogyasztói csoport

Ez a cikk bemutatja, hogyan típusú névtér létrehozása az Azure Resource Manager-sablon használatával [az Event Hubs](event-hubs-what-is-event-hubs.md), és az egy eseményközponttal és a egy fogyasztói csoporton. A cikk bemutatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Sablonok létrehozásával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablont, tekintse meg a [Event hub és a fogyasztói csoport sablon] [ Event Hub and consumer group template] a Githubon.

> [!NOTE]
> A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.
> 
> 

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

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
