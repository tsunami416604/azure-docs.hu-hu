---
title: Service Bus engedélyezési szabály létrehozása Azure-sablon használatával
description: Service Bus engedélyezési szabály létrehozása névtérhez és várólistához az Azure Resource Manager-sablon használatával
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384925"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Service Bus engedélyezési szabály létrehozása névtérhez és várólistához egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használhat egy Azure Resource Manager-sablont, amely [engedélyezési szabályt](service-bus-authentication-and-authorization.md#shared-access-signature) hoz létre egy Service Bus-névtérhez és várólistahoz. A cikk bemutatja, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

A sablonok létrehozásáról további információt az [Azure Resource Manager-sablonok készítése című témakörben talál.][Authoring Azure Resource Manager templates]

A teljes sablon, tekintse meg a [Service Bus engedélyezési szabály sablon][Service Bus auth rule template] a GitHubon.

> [!NOTE]
> A következő Azure Resource Manager-sablonok tölthetők le és telepíthetik.
> 
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása várólistával](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonok kereséséhez keresse fel az [Azure gyorsútmutatósablonok][Azure Quickstart Templates] gyűjteményét, és keresse meg a Service Bus című **szolgáltatást.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezzel a sablonnal üzembe helyez egy Service Bus engedélyezési szabályt egy névtérhez és egy üzenetküldési entitáshoz (ebben az esetben egy várólistához).

Ez a sablon [megosztott hozzáférésű aláírást (SAS)](service-bus-sas.md) használ a hitelesítéshez. A SAS lehetővé teszi, hogy az alkalmazások hitelesítsék magukat a Service Bus számára a névtéren konfigurált hozzáférési kulcs, vagy az üzenetkezelő entitás (várólista vagy témakör), amelyhez adott jogok vannak társítva. Ezután ezzel a kulccsal létrehozhat egy SAS-jogkivonatot, amelyet az ügyfelek viszont a Service Bus hitelesítéséhez használhatnak.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz `Parameters` egy nevű szakaszt, amely az összes paraméterértéket tartalmazza. Meg kell határoznia egy paramétert az értékekhez, amely a telepített projekttől vagy a környezettől függően változik. Ne adjon meg paramétereket olyan értékekhez, amelyek mindig változatlanok maradnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

A sablon a következő paramétereket adja meg.

### <a name="servicebusnamespacename"></a>szolgáltatásBusNamespaceName

A létrehozandó Service Bus névtér neve.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName

A névtér engedélyezési szabályának neve.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

A várólista neve a Service Bus névtérben.

```json
"serviceBusQueueName": {
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

Létrehoz egy szabványos Service Bus névteret **Üzenetek**típusú , és egy Service Bus engedélyezési szabályt a névtérhez és az entitáshoz.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

A JSON szintaxisát és tulajdonságait lásd: [Névterek](/azure/templates/microsoft.servicebus/namespaces), [várólisták](/azure/templates/microsoft.servicebus/namespaces/queues)és [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta és üzembe helyezte az erőforrásokat az Azure Resource Manager használatával, ismerje meg, hogyan kezelheti ezeket az erőforrásokat az alábbi cikkek megtekintésével:

* [A Service Bus kezelése a PowerShell használatával](service-bus-powershell-how-to-provision.md)
* [A Service Bus-erőforrások kezelése a Service Bus Intézővel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus-hitelesítés és -engedélyezés](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
