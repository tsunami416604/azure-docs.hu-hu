---
title: A Service Bus-témakör-előfizetés és -szabály létrehozása az Azure-sablon használatával
description: Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal az Azure Resource Manager-sablon használatával
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
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384891"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használhatja az Azure Resource Manager sablont, amely létrehoz egy Service Bus névtér egy témakör, előfizetés és szabály (szűrő). A cikk bemutatja, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ezt a sablont saját üzembe helyezési műveleteihez is használhatja, vagy akár igényeinek megfelelően testre is szabhatja

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

Az Azure-erőforrások elnevezési konvencióinak gyakorlatáról és mintáiról az [Azure-erőforrások ajánlott elnevezési konvenciói című témakörben][Recommended naming conventions for Azure resources]talál további információt.

A teljes sablont a [Service Bus témakörrel, előfizetéssel és szabálysablonnal rendelkező szolgáltatásában][Service Bus namespace with topic, subscription, and rule] talál.

> [!NOTE]
> A következő Azure Resource Manager-sablonok tölthetők le és telepíthetik.
> 
> * [Service Bus-névtér létrehozása várólista- és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása várólistával](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> 
> A legújabb sablonok kereséséhez keresse fel az [Azure gyorsútmutatósablonok][Azure Quickstart Templates] gyűjteményét, és keresse meg a Service Bus-t.

## <a name="what-do-you-deploy"></a>Mit vetsz be?

Ezzel a sablonnal egy Service Bus-névteret telepít témakörrel, előfizetéssel és szabállyal (szűrővel).

[A Service Bus-témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) egy-a-többhöz kommunikációs formát biztosítanak egy *közzétételi/előfizetési* mintában. Témakörök és előfizetések használatakor az elosztott alkalmazások összetevői nem kommunikálnak közvetlenül egymással, hanem közvetítőként szolgáló témakörön keresztül váltanak üzeneteket. A témakörre való előfizetés hasonlít egy virtuális várólistára, amely a témakörbe küldött üzenetek másolatait fogadja. Az előfizetésszűrő segítségével megadhatja, hogy mely témakörbe küldött üzenetek jelenjenek meg egy adott témakör-előfizetésen belül.

## <a name="what-are-rules-filters"></a>Mik azok a szabályok (szűrők)?

Számos esetben a speciális jellemzőkkel rendelkező üzeneteket különböző módon kell feldolgozni. Az egyéni feldolgozás engedélyezéséhez konfigurálhatja az előfizetéseket a meghatározott tulajdonságokkal rendelkező üzenetek keresésére, majd módosíthatja ezeket a tulajdonságokat. Bár a Service Bus-előfizetések a témakörbe küldött összes üzenetet látják, ezeknek az üzeneteknek csak egy részhalmazát másolhatja a virtuális előfizetési várólistába. Ez előfizetési szűrők használatával történik. A szabályokról (szűrőkről) a [Szabályok és műveletek](service-bus-queues-topics-subscriptions.md#rules-and-actions)című témakörben olvashat bővebben.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Manager segítségével adja meg a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablonban található egy `Parameters` nevű rész, amely magába foglalja az összes paraméterértéket. Adjon meg egy paramétert azokhoz az értékekhez, amelyek a telepített projekttől vagy a környezettől függően változnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

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

### <a name="servicebusrulename"></a>serviceBusRuleName

A Service Bus névterében létrehozott szabály(szűrő) neve.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Létrehoz egy szabványos Service Bus névteret **Üzenetküldés**típusú, témakörrel, előfizetéssel és szabályokkal.

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

A JSON szintaxisát és tulajdonságait lásd: [névterek](/azure/templates/microsoft.servicebus/namespaces), [témakörök](/azure/templates/microsoft.servicebus/namespaces/topics), [előfizetések](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)és [szabályok.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules)

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatja, hogyan kezelheti ezeket az erőforrásokat:

* [Az Azure Service Bus kezelése](service-bus-management-libraries.md)
* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Intézővel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
