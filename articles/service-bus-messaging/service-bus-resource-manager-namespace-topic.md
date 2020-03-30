---
title: Az Azure Service Bus névtertémakörlétrehozása sablon használatával
description: 'Rövid útmutató: Service Bus-névtér létrehozása témakörrel és előfizetéssel az Azure Resource Manager sablonhasználatával'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c94b670a33f7640d2d6f428287b3ba0fab766bc5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384874"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Rövid útmutató: Service Bus-névtér létrehozása témakörrel és előfizetéssel egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használhatja az Azure Resource Manager sablont, amely létrehoz egy Service Bus névtér és egy témakör és előfizetés a névtérben. A cikk bemutatja, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

A teljes sablont a [Service Bus témakörrel és előfizetési sablonnal rendelkező névtere című témakörben talál.][Service Bus namespace with topic and subscription]

> [!NOTE]
> A következő Azure Resource Manager-sablonok tölthetők le és telepíthetik.
> 
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása várólistával](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása várólista- és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonok kereséséhez keresse fel az [Azure gyorsútmutatósablonok][Azure Quickstart Templates] gyűjteményét, és keresse meg a Service Bus című **szolgáltatást.**

## <a name="what-do-you-deploy"></a>Mit vetsz be?

Ezzel a sablonnal telepíti a Service Bus névteret témakörrel és előfizetéssel.

[A Service Bus-témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz kommunikációs formát biztosítanak egy *közzétételi/előfizetési* mintában.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz `Parameters` egy nevű szakaszt, amely az összes paraméterértéket tartalmazza. Adjon meg egy paramétert azokhoz az értékekhez, amelyek a telepíti a projekttől vagy a környezettől függően változnak, amelybe a központi telepítést alkalmazza. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket határozza meg:

### <a name="servicebusnamespacename"></a>szolgáltatásBusNamespaceName

A létrehozandó Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

A Service Bus névtérben létrehozott témakör neve.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

A Service Bus névtérben létrehozott előfizetés neve.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>szolgáltatásBusApiVersion

A Service Bus API verziója a sablon.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások

Létrehoz egy szabványos Service Bus névteret **Üzenetküldés**, témakörrel és előfizetéssel.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

A JSON szintaxisát és tulajdonságait lásd: [névterek](/azure/templates/microsoft.servicebus/namespaces), [témakörök](/azure/templates/microsoft.servicebus/namespaces/topics)és [előfizetések.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group deployment create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta és üzembe helyezte az erőforrásokat az Azure Resource Manager használatával, ismerje meg, hogyan kezelheti ezeket az erőforrásokat az alábbi cikkek megtekintésével:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Intézővel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
