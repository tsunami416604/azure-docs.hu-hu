---
title: "Azure Service Bus-névtér létrehozása és a feldolgozási sor az Azure Resource Manager-sablonnal |} Microsoft Docs"
description: "A Service Bus-névtér és az Azure Resource Manager sablonnal várólista létrehozása"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;shvija
ms.openlocfilehash: 4358130a2c8e897a0fdd1f9560f766d6e22db4d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>A Service Bus-névtér és az Azure Resource Manager-sablonnal várólista létrehozása

Ez a cikk bemutatja, hogyan hozza létre a Service Bus-névtér és az adott névtérben várólista Azure Resource Manager sablonnal. Megtudhatja, hogyan határozza meg, mely erőforrásokat központilag telepíti, és hogyan adhat meg a paramétereket, amelyek megadott, amikor a központi telepítés végrehajtása. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates].

A teljes sablon, tekintse meg a [Service Bus-névteret és a várólista sablon] [ Service Bus namespace and queue template] a Githubon.

> [!NOTE]
> A következő Azure Resource Manager-sablonok letöltése és központi telepítés érhetők el.
> 
> * [Hozzon létre egy Service Bus-névtér várólista és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Hozzon létre egy Service Bus-névtér témakör és előfizetés](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [A témakör, előfizetés és a szabály a Service Bus-névtér létrehozása](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Ellenőrizze a legutóbbi sablonok, látogasson el a [Azure gyors üzembe helyezési sablonokat] [ Azure Quickstart Templates] gyűjteménye, és keressen a "Service Bus."
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezen sablon esetén telepíti a Service Bus-névtér üzenetsorokat.

[Service Bus-üzenetsorok](service-bus-queues-topics-subscriptions.md#queues) kínálnak First In, First Out (, FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon nevű szakaszban tartalmaz `Parameters` , amely tartalmazza az összes a paraméterértékek. Meg kell határozni egy paramétert ezeket az értékeket, amelyek a projekt telepít vagy telepít, hogy a környezet alapján változhatnak. Az értékeket, amelyeket a rendszer mindig ugyanaz maradjon paraméterek nem határoznak meg. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

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
A várólista létrehozása a Service Bus-névtér neve.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A sablon Service Bus API verzióját.

```json
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
Létrehoz egy standard Service Bus-névtér típusú **Messaging**, üzenetsorokat.

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
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

## <a name="next-steps"></a>Következő lépések
Most, hogy már létrehozott és telepített Azure Resource Manager eszközzel, megtudhatja, hogyan kezelheti ezeket az erőforrásokat megtekintésével, ezek a cikkek:

* [A PowerShell használatával a Service Bus kezelése](service-bus-manage-with-ps.md)
* [A Service Bus Explorer Service Bus-erőforrások kezelése](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
