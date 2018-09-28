---
title: Hozzon létre a Service Bus üzenetkezelési névteret Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Azure Resource Manager-sablon használatával létrehozhat egy Service Bus Messaging-névteret
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 6f3f44394ab11c1b66be3af976dbd1f7d23de96e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405784"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Hozzon létre egy Azure Resource Manager-sablon használatával a Service Bus-névtér

Ez a cikk azt ismerteti, hogyan használható az Azure Resource Manager-sablon, amely létrehozza a Service Bus-névtér, típus **üzenetkezelés** a Standard Termékváltozat. A cikk azt is meghatározza, a megadott paraméterek, a telepítés végrehajtására. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

A teljes sablont, tekintse meg a [a Service Bus-névtér sablon] [ Service Bus namespace template] a Githubon.

> [!NOTE]
> Az alábbi Azure Resource Manager-sablonok letöltése és központi telepítési érhetők el. 
> 
> * [Service Bus-névtér létrehozása az üzenetsorhoz](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> A legújabb sablonokat keressen, látogasson el a [Azure gyorsindítási sablonok] [ Azure Quickstart Templates] katalógusban, és keresse meg a Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?

Ezzel a sablonnal egy Service Bus-névtér telepít egy [Standard vagy prémium szintű](https://azure.microsoft.com/pricing/details/service-bus/) Termékváltozat.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek

Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz nevű szakaszban `Parameters` , amely tartalmazza az összes paraméter értékét. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

Ez a sablon határozza meg a következő paraméterekkel:

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

### <a name="servicebussku"></a>serviceBusSKU

A Service Bus neve [Termékváltozat](https://azure.microsoft.com/pricing/details/service-bus/) hozhat létre.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

A sablon határozza meg az értékeket, amelyeknél engedélyezve van a ezt a paramétert (Standard vagy prémium). Ha nem ad meg értéket, az erőforrás-kezelő (általános) alapértelmezett értéket rendeli hozzá.

További információ a Service Bus díjszabásáról: [a Service Bus díjszabása és számlázási][Service Bus pricing and billing].

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

### <a name="service-bus-namespace"></a>Service Bus-névtér

Létrehoz egy standard szintű Service Bus-névtér típusú **üzenetkezelés**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta és erőforrások Azure Resource Managerrel üzembe helyezett, megtudhatja, hogyan kezelhetők ezek a cikkek elolvasásával:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
