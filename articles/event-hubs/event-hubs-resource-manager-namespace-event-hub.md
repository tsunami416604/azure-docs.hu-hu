---
title: "Azure Event Hubs névtér és felhasználó-csoport létrehozása sablon alapján |} Microsoft Docs"
description: "Hozzon létre egy Event Hubs-névtér az eseményközpontok és egy felhasználói csoport Azure Resource Manager-sablonok használatával"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/09/2017
ms.author: sethm;shvija
ms.openlocfilehash: 4cc9a0b9eaabb15a5a316e094deb178ef2219692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Az Event Hubs-névtér létrehozása Azure Resource Manager-sablonnal esemény hub és felhasználói csoport

Ez a cikk bemutatja, hogyan használható az Azure Resource Manager sablon, amely létrehoz egy névtér, az Event Hubs egy eseményközpontba való típusú és egy fogyasztói csoporton. A cikk bemutatja, hogyan határozza meg, mely erőforrásokat központilag telepíti, és hogyan adhat meg a paramétereket, amelyek megadott, amikor a központi telepítés végrehajtása. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

A teljes sablon, tekintse meg a [hub és felhasználói csoport eseménysablon] [ Event Hub and consumer group template] a Githubon.

> [!NOTE]
> A legújabb sablonokért keresse fel az [Azure-gyorssablonok][Azure Quickstart Templates] gyűjteményt, és keressen az Event Hubs kifejezésre.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?
Ezzel a sablonnal egy Event Hubs névtér eseményközpontban, és egy felhasználói csoport fog üzembe helyezni.

Az [Event Hubs](event-hubs-what-is-event-hubs.md) egy eseményfeldolgozási szolgáltatás, amely az Azure-ba irányuló, nagy léptékű esemény- és telemetriabevitelt biztosít alacsony késéssel és nagy megbízhatósággal.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Ezeket az értékeket, amelyek változhatnak, a projekt telepít vagy a környezet, amely telepíti alapján egy paramétert meg kell határozni. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablon minden paraméter értéke határozza meg a telepített erőforrások esetén.

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
A fogyasztói csoportot az eseményközpont létre neve.

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
Létrehoz egy névtér, típus **EventHubs**, az event hubs és a fogyasztói csoportot.

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
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
