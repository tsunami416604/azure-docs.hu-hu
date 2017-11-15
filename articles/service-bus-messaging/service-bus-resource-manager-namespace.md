---
title: "Hozzon létre a Service Bus-névtér Azure Resource Manager-sablonnal |} Microsoft Docs"
description: "Service Bus-névtér létrehozása az Azure Resource Manager-sablon segítségével"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;shvija
ms.openlocfilehash: c8a42638c79a8a53f80102fc344eccb521e4c1c5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablonnal Service Bus-névtér létrehozása

Ez a cikk ismerteti az Azure Resource Manager sablon használata, amely létrehoz egy Service Bus-névtér típusú **Messaging** a Standard Termékváltozat. A cikk is definiálja a megadott paraméterek, a telepítés végrehajtásához. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Azure Resource Manager-sablonok készítése) című témakörben talál.

A teljes sablon, tekintse meg a [Service Bus-névtér sablon] [ Service Bus namespace template] a Githubon.

> [!NOTE]
> A következő Azure Resource Manager-sablonok letöltése és központi telepítés érhetők el. 
> 
> * [Hozzon létre egy Service Bus-névtér várólista](service-bus-resource-manager-namespace-queue.md)
> * [Hozzon létre egy Service Bus-névtér témakör és előfizetés](service-bus-resource-manager-namespace-topic.md)
> * [Hozzon létre egy Service Bus-névtér várólista és engedélyezési szabály](service-bus-resource-manager-namespace-auth-rule.md)
> * [A témakör, előfizetés és a szabály a Service Bus-névtér létrehozása](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Ellenőrizze a legutóbbi sablonok, látogasson el a [Azure gyors üzembe helyezési sablonokat] [ Azure Quickstart Templates] gyűjteménye, majd keresse meg a Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Mit fog üzembe helyezni?
Ezen sablon esetén a Service Bus-névtér telepít egy [Standard vagy prémium](https://azure.microsoft.com/pricing/details/service-bus/) Termékváltozat.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon nevű szakaszban tartalmaz `Parameters` , amely tartalmazza az összes a paraméterértékek. Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva.

Ez a sablon meghatározza, hogy a következő paraméterekkel:

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
A Service Bus neve [SKU](https://azure.microsoft.com/pricing/details/service-bus/) létrehozásához.

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

A sablon határozza meg az értékeket, amelyek ehhez a paraméterhez (Standard vagy prémium) megengedettek. Ha nincs érték megadva, az erőforrás-kezelő (általános) alapértelmezett értéket rendeli hozzá.

A Service Bus árazással kapcsolatos további információkért lásd: [Service Bus árak és számlázás][Service Bus pricing and billing].

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
### <a name="service-bus-namespace"></a>Service Bus-névtér
Létrehoz egy standard Service Bus-névtér típusú **Messaging**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
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
```azurecli
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Következő lépések
Most, hogy már létrehozott és telepített Azure Resource Manager eszközzel, megtudhatja, hogyan kezelheti ezeket az erőforrásokat ezek a cikkek olvasásával:

* [A PowerShell használatával a Service Bus kezelése](service-bus-manage-with-ps.md)
* [A Service Bus Explorer Service Bus-erőforrások kezelése](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
