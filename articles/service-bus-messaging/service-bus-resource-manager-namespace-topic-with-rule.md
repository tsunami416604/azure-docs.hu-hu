---
title: "Azure Service Bus témakör előfizetés létrehozása és a szabály az Azure Resource Manager-sablonnal |} Microsoft Docs"
description: "Hozzon létre egy Service Bus-névtér témakör, előfizetés és Azure Resource Manager-sablon segítségével"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/10/2017
ms.author: sethm;shvija
ms.openlocfilehash: 976c7b425dd17f8ed38f18b6ffa50b4368ab44b3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Hozzon létre egy Service Bus-névtér témakör, előfizetés és Azure Resource Manager-sablonnal szabály

Ez a cikk bemutatja, hogyan hozza létre a Service Bus-névtér egy témakör, az előfizetés és a szabály (szűrő) Azure Resource Manager sablonnal. A cikk azt ismerteti, hogyan adhatja meg, mely erőforrásokat telepítve van, és hogyan adhat meg a paramétereket, amelyek megadott, amikor a központi telepítés végrehajtása. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

Az Azure-erőforrások elnevezési konvenciói eljárások és minták kapcsolatos további információkért lásd: [Azure-erőforrások elnevezési szabályai ajánlott][Recommended naming conventions for Azure resources].

A teljes sablon, tekintse meg a [témakör, előfizetés és a szabály a Service Bus-névtér] [ Service Bus namespace with topic, subscription, and rule] sablont.

> [!NOTE]
> A következő Azure Resource Manager-sablonok letöltése és központi telepítés érhetők el.
> 
> * [Hozzon létre egy Service Bus-névtér várólista és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Hozzon létre egy Service Bus-névtér várólista](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Hozzon létre egy Service Bus-névtér témakör és előfizetés](service-bus-resource-manager-namespace-topic.md)
> 
> Ellenőrizze a legutóbbi sablonok, látogasson el a [Azure gyors üzembe helyezési sablonokat] [ Azure Quickstart Templates] gyűjteménye, majd keresse meg a Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezen sablon esetén telepít egy Service Bus-névtér témakör, előfizetés és a szabály (szűrő).

[Service Bus-üzenettémák és előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy egy-a-többhöz típusú kommunikációt biztosítanak a egy *közzétételi/előfizetési* mintát. Az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, témakörök és előfizetések használata esetén ehelyett azok exchange keresztül témakör, amely közvetítőként működik. A témakör előfizetői hasonlít egy virtuális üzenetsorra, amely a témakörbe küldött üzenetek példányait megkapja. Előfizetés lehetővé teszi, hogy a szűrő megadhatja, mely egy témakörbe küldött üzenetek jelenjenek meg belül egy adott üzenettémakör-előfizetésben.

## <a name="what-are-rules-filters"></a>Mik azok a szabályok (szűrők)?

A sok esetben meghatározott jellemzőkkel rendelkező üzenetek különböző módon kell feldolgozni. Ahhoz, hogy az egyéni feldolgozási, konfigurálhatja az előfizetések található üzeneteket meghatározott jellemzőkkel rendelkezik, és végezze el ezen tulajdonságok módosításait. Bár a Service Bus-előfizetések a témakörbe küldött üzenetek láthatja, a virtuális előfizetés várólistára csak másolhatja azokat az üzeneteket egy részét. Mindez előfizetés-szűrők használata. Szabályok (szűrők) kapcsolatos további információkért lásd: [szabályok és a műveletek](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Manager érdemes definiálni paramétereinek adja meg a sablon telepítésekor kívánt értékeket. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon meghatározza a következő:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
A Service Bus-névtér létrehozása neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
A témakör a Service Bus-névtér létrehozása neve.

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
### <a name="servicebusrulename"></a>serviceBusRuleName
A Service Bus-névtér létrehozása rule(filter) neve.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
A sablon Service Bus API verzióját.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
Létrehoz egy standard Service Bus-névtér típusú **Messaging**, témakör és előfizetés és szabályok.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Következő lépések
Most, hogy már létrehozott és telepített Azure Resource Manager eszközzel, megtudhatja, hogyan kezelheti ezeket az erőforrásokat megtekintésével, ezek a cikkek:

* [Az Azure Service Bus kezelése](service-bus-management-libraries.md)
* [A PowerShell használatával a Service Bus kezelése](service-bus-manage-with-ps.md)
* [A Service Bus Explorer Service Bus-erőforrások kezelése](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

