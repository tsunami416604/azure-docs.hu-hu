---
title: 'Rövid útmutató: Azure Service Bus névtér és üzenetsor létrehozása Azure Resource Manager sablon használatával'
description: 'Gyors útmutató: Service Bus névtér és üzenetsor létrehozása Azure Resource Manager sablon használatával'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 40aeff225cfa6d499ebdfae6736e0684b2a27244
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718798"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Gyors útmutató: Service Bus névtér és üzenetsor létrehozása Azure Resource Manager sablon használatával

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager-sablon, amely létrehoz egy Service Bus névteret és egy, az adott névtéren belüli várólistát. A cikk azt ismerteti, hogyan határozható meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

További információ a sablonok létrehozásáról: Azure Resource Manager- [sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablonhoz tekintse meg a [Service Bus névtér és várólista sablonját][Service Bus namespace and queue template] a githubon.

> [!NOTE]
> A következő Azure Resource Manager sablonok tölthetők le és üzemelő példányban.
> 
> * [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonok kereséséhez látogasson el az [Azure Gyorsindítás sablonok][Azure Quickstart Templates] galériába, és keressen rá **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezzel a sablonnal egy Service Bus névteret helyez üzembe egy várólistával.

[Service Bus Queues](service-bus-queues-topics-subscriptions.md#queues) először a, az első kimenő (FIFO) üzenet kézbesítését kínálja egy vagy több versenytárs fogyasztónak.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy `Parameters` nevű szakaszt, amely az összes paraméter értékét tartalmazza. Meg kell határoznia a paramétert azokhoz az értékekhez, amelyek a telepített projekttől függően változnak, vagy amely az Ön által telepített környezet alapján változik. Ne definiáljon paramétereket olyan értékekhez, amelyek mindig azonosak maradnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
A létrehozandó Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
A Service Bus névtérben létrehozott üzenetsor neve.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A sablon Service Bus API-verziója.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
Egy **üzenetküldési**típusú szabványos Service Bus névteret hoz létre egy üzenetsor használatával.

```json
{
    "resources": [{
        "apiVersion": "2017-04-01",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
            "name": "Standard"
        },
        "properties": {},
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }]
}
```

A JSON szintaxis és tulajdonságok esetében lásd: [névterek](/azure/templates/microsoft.servicebus/namespaces) és [várólisták](/azure/templates/microsoft.servicebus/namespaces/queues).

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő témakört, amely bemutatja, hogyan hozhat létre egy engedélyezési szabályt a névtérhez vagy a várólistához: [Service Bus engedélyezési szabály létrehozása a névtérhez és a várólistához egy Azure Resource Manager sablon használatával](service-bus-resource-manager-namespace-auth-rule.md)

Ismerje meg, hogyan kezelheti ezeket az erőforrásokat a következő cikkek megtekintésével:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [Service Bus erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
