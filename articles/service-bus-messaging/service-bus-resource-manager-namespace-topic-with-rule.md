---
title: Azure Service Bus témakör-előfizetés létrehozása és a szabály az Azure Resource Manager-sablonnal |} A Microsoft Docs
description: A témakör, előfizetés és Azure Resource Manager-sablon használatával szabály egy Service Bus-névtér létrehozása
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 42be349a5f89a2057d67b5bcab5eda70c5c9e7eb
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064202"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Service Bus-névtér létrehozása a témakör, előfizetés és a szabály egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használható az Azure Resource Manager-sablon, amely egy Service Bus-névteret hoz létre egy témakört, előfizetéssel és szabállyal (szűrő). A cikk azt ismerteti, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan adhat meg a paramétereket, amelyek a megadott az üzembe helyezés végrehajtása esetén. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

Azure-erőforrások elnevezési konvenciói eljárások és minták kapcsolatos további információkért lásd: [ajánlott az Azure-erőforrások elnevezési konvenciói][Recommended naming conventions for Azure resources].

A teljes sablont, tekintse meg a [témakör, előfizetés és a szabály a Service Bus-névtér] [ Service Bus namespace with topic, subscription, and rule] sablont.

> [!NOTE]
> Az alábbi Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el.
> 
> * [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása az üzenetsorhoz](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> 
> A legújabb sablonokat keressen, látogasson el a [Azure gyorsindítási sablonok] [ Azure Quickstart Templates] katalógusban, és keresse meg a Service Bus.
> 
> 

## <a name="what-do-you-deploy"></a>Milyen tegye üzembe helyezni?

A sablon üzembe helyezése egy Service Bus-névtér témakörrel, előfizetéssel és szabály (szűrő).

[Service Bus-üzenettémák és előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz típusú kommunikációt biztosítanak az egy *közzétételi/előfizetési* mintát. Elosztott alkalmazások összetevői nem kommunikálnak közvetlenül egymással, témakörök és előfizetések használata esetén inkább keresztül váltanak üzeneteket témakör, amely közvetítőként működik. Egy témakör-előfizetés hasonlít egy virtuális üzenetsorra, amely megkapja a témakörbe küldött üzenetek másolatát. Egy szűrő, az előfizetés lehetővé teszi, hogy adja meg, mely egy témakörbe küldött üzenetek jelenjenek meg egy adott témakör-előfizetésben.

## <a name="what-are-rules-filters"></a>Mik azok a szabályok (szűrőkkel)?

Sok esetben üzeneteket, amelyek meghatározott jellemzőkkel rendelkeznek, különböző módon kell feldolgozni. Ahhoz, hogy az egyéni feldolgozási, konfigurálhatja az előfizetések található üzeneteket, amelyek az adott tulajdonságokkal rendelkezik, és hajtsa végre a módosításokat, azokat a tulajdonságokat. Service Bus-előfizetések a témakörbe küldött összes üzenet látható, bár csak másolhatja azokat az üzeneteket egy részét az virtuális előfizetés üzenetsorába. Előfizetés-szűrők használatával történik. Szabályok (szűrőkkel) kapcsolatos további információkért lásd: [szabályok és műveletek](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Manager adja meg a sablon üzembe helyezésekor kívánt értékeket a paraméterek meghatározása. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Adjon meg ezeket az értékeket, amelyek paramétert, a projekt telepítésekor vagy telepíti, akkor a környezet alapján. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon meghatározza a következő:

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
### <a name="servicebusrulename"></a>serviceBusRuleName
A Service Bus-névtér létrehozása az rule(filter) neve.

```json
   "serviceBusRuleName": {
   "type": "string",
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
Létrehoz egy standard szintű Service Bus-névtér típusú **üzenetkezelés**, témakörrel és előfizetéssel és szabályokat.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
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

JSON-szintaxist és a Tulajdonságok [névterek](/azure/templates/microsoft.servicebus/namespaces), [témakörök](/azure/templates/microsoft.servicebus/namespaces/topics), [előfizetések](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions), és [szabályok](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

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

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta és erőforrások Azure Resource Managerrel üzembe helyezett, megtudhatja, hogyan kezelhetők ezek a cikkek alapján:

* [Az Azure Service Bus kezelése](service-bus-management-libraries.md)
* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

