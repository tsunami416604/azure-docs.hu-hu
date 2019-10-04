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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444773"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Service Bus-névtér létrehozása az Azure Resource Manager-sablon használatával

Ismerje meg, hogyan helyezhet üzembe a Service Bus-névtér létrehozása egy Azure Resource Manager-sablont. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/).

Az alábbi sablonok is elérhetők a Service Bus-névterek létrehozásának:

* [Service Bus-névtér létrehozása az üzenetsorhoz](./service-bus-resource-manager-namespace-queue.md)
* [Service Bus-névtér létrehozása témakörrel és előfizetéssel](./service-bus-resource-manager-namespace-topic.md)
* [Service Bus-névtér létrehozása az üzenetsor és engedélyezési szabály](./service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabály](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-a-service-bus-namespace"></a>Service bus-névtér létrehozása

Ez a rövid egy [meglévő Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) a [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

További sablonminták talál [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

A service bus-névtér létrehozása a sablon üzembe helyezésével:

1. Válassza ki **kipróbálás** az az alábbi kód letiltása, és kövesse az utasításokat követve jelentkezzen be az Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Az erőforráscsoport neve a service bus-névtér neve a **rg** hozzáfűzve.

2. Válassza ki **másolási** másolása a PowerShell-parancsfájlt.
3. Kattintson a jobb gombbal a rendszerhéj-konzolon, és válassza **beillesztési**.

Eseményközpont létrehozása néhány percet vesz igénybe.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A telepített service bus-névtér megtekintéséhez is nyissa meg az erőforráscsoport az Azure Portalról, vagy használja a következő Azure PowerShell-parancsfájlt. Ha a Cloud shellben folyamatban, nem kell másolási/futtassa a következő szkript első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Ebben az oktatóanyagban a sablon üzembe helyezése az Azure PowerShell segítségével. Egyéb sablon központi telepítési módszer lásd:

* [Az Azure portal használatával](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a Cloud shellben folyamatban, nem kell másolási/futtassa a következő szkript első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott Service Bus-névtér. A többi rövid útmutató megtudhatja, hogyan hozhat létre az üzenetsorok, üzenettémák, előfizetések, tekintse meg, és használja őket:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Ismerkedés a Service Bus-témakörök](service-bus-dotnet-how-to-use-topics-subscriptions.md)
