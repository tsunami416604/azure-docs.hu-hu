---
title: Hozzon létre az Azure Service Bus témakör-előfizetés névtér Azure Resource Manager-sablon használatával |} A Microsoft Docs
description: Service Bus-névtér létrehozása témakörrel és előfizetéssel Azure Resource Manager-sablon használatával
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: b5512186913eb59be2b89ce8b8bb9fb881f59cd8
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699821"
---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Service Bus-névtér létrehozása témakörrel és előfizetéssel egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használható az Azure Resource Manager-sablon, amely létrehozza a Service Bus-névtér és a egy üzenettémát és egy előfizetést adott névtéren belül. A cikk azt ismerteti, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan adhat meg a paramétereket, amelyek a megadott az üzembe helyezés végrehajtása esetén. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

Sablonok létrehozásával kapcsolatos további információkért tekintse meg [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablont, tekintse meg a [Service Bus-névtér témakörrel és előfizetéssel] [ Service Bus namespace with topic and subscription] sablont.

> [!NOTE]
> Az alábbi Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el.
> 
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása az üzenetsorhoz](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonokat keressen, látogasson el a [Azure gyorsindítási sablonok] [ Azure Quickstart Templates] katalógusban, és keresse meg a **a Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

A sablon üzembe helyezése egy Service Bus-névtér témakörrel és előfizetéssel.

[Service Bus-üzenettémák és előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz típusú kommunikációt biztosítanak az egy *közzétételi/előfizetési* mintát.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz nevű szakaszban `Parameters` , amely tartalmazza az összes paraméter értékét. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
A Service Bus-névtér létrehozása neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
A Service Bus-névtérben létrehozott üzenettéma nevére.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Az előfizetés létrehozása a Service Bus-névtér neve.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A Service Bus API verzióját a sablont.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
Létrehoz egy standard szintű Service Bus-névtér típusú **üzenetkezelés**, témakörrel és előfizetéssel.

```json
"resources ": [{
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

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta és erőforrások Azure Resource Managerrel üzembe helyezett, megtudhatja, hogyan kezelhetők ezek a cikkek alapján:

* [Service Bus kezelése a PowerShell](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
