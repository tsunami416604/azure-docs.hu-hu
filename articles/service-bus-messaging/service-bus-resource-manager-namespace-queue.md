---
title: Az Azure Service Bus-névtér létrehozása, és a várólistára Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Service Bus-névtér és az Azure Resource Manager-sablon használatával üzenetsor létrehozása
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 370ef54f39c585ffe0babd4aa54ed7ed89e9dfbc
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849600"
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Service Bus-névtér és a egy Azure Resource Manager-sablonnal üzenetsor létrehozása

Ez a cikk bemutatja, hogyan használható az Azure Resource Manager-sablon, amely létrehoz egy Service Bus-névtér és a egy adott névtéren belül várólista. A cikk azt ismerteti, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan adhat meg a paramétereket, amelyek a megadott az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Sablonok létrehozásával kapcsolatos további információkért tekintse meg [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablont, tekintse meg a [a Service Bus-névtérhez és üzenetsorhoz sablon] [ Service Bus namespace and queue template] a Githubon.

> [!NOTE]
> Az alábbi Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el.
> 
> * [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonokat keressen, látogasson el a [Azure gyorsindítási sablonok] [ Azure Quickstart Templates] katalógusban, és keresse meg a **a Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

A sablon üzembe helyezése Service Bus-névtér,-üzenetsorhoz.

[Service Bus-üzenetsorok](service-bus-queues-topics-subscriptions.md#queues) ajánlat First In, első ki (FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz nevű szakaszban `Parameters` , amely tartalmazza az összes paraméter értékét. Meg kell határozni egy paramétere ezeket az értékeket, amelyek alapján a projekt telepítésekor, vagy telepíti, akkor a környezet alapján változhatnak. Nem határoznak meg paramétereket olyan értékhez, amely mindig érintetlen marad. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
A Service Bus-névtér létrehozása neve.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
A Service Bus-névtér létrehozása az üzenetsor neve.

```json
"serviceBusQueueName": {
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
Létrehoz egy standard szintű Service Bus-névtér típusú **üzenetkezelés**,-üzenetsorhoz.

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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

JSON-szintaxist és a Tulajdonságok [névterek](/azure/templates/microsoft.servicebus/namespaces) és [üzenetsorok](/azure/templates/microsoft.servicebus/namespaces/queues).

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta és erőforrások Azure Resource Managerrel üzembe helyezett, megtudhatja, hogyan kezelhetők ezek a cikkek alapján:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
