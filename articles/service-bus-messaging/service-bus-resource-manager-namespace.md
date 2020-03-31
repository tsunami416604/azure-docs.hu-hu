---
title: Azure Service Bus-névtér létrehozása sablon használatával
description: Service Bus Messaging névtér létrehozása az Azure Resource Manager-sablonnal
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
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264475"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Service Bus-névtér létrehozása Azure Resource Manager-sablon használatával

Megtudhatja, hogyan telepíthet egy Azure Resource Manager-sablont a Service Bus-névtér létrehozásához. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. A sablonok létrehozásáról az [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/)olvashat bővebben.

A Service Bus-névterek létrehozásához a következő sablonok is rendelkezésre állnak:

* [Service Bus-névtér létrehozása várólistával](./service-bus-resource-manager-namespace-queue.md)
* [Service Bus-névtér létrehozása témakörrel és előfizetéssel](./service-bus-resource-manager-namespace-topic.md)
* [Service Bus-névtér létrehozása várólista- és engedélyezési szabállyal](./service-bus-resource-manager-namespace-auth-rule.md)
* [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-a-service-bus-namespace"></a>Szolgáltatásbusz-névtér létrehozása

Ebben a rövid útmutatóban egy [meglévő Erőforrás-kezelő sablont](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) használ az [Azure gyorsútmutatósablonjaiból:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

További sablonmintákat az [Azure gyorsútmutató-sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)ban talál.

Szolgáltatásbusz-névtér létrehozása sablon telepítésével:

1. Válassza **a Próbálja ki** a következő kódblokkból, majd kövesse az utasításokat az Azure Cloud rendszerhéjba való bejelentkezéshez.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Az erőforráscsoport neve a szolgáltatásbusz névtérneve **rg** csatolt.

2. Válassza **a Másolás lehetőséget** a PowerShell-parancsfájl másolásához.
3. Kattintson a jobb gombbal a rendszerhéj-konzolra, és válassza **a Beillesztés parancsot.**

Az eseményközpont létrehozása néhány percet vesz igénybe.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

Az üzembe helyezett szolgáltatásbusz-névtér megtekintéséhez nyissa meg az erőforráscsoportot az Azure Portalon, vagy használja a következő Azure PowerShell-parancsfájlt. Ha a felhőrendszerhéj még nyitva van, nem kell másolnia/futtatnia a következő parancsfájl első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Az Azure PowerShell ebben az oktatóanyagban a sablon üzembe helyezéséhez szolgál. A sablonok további telepítési módszereiről a következő témakörben olvashat:

* [Az Azure Portal használatával.](../azure-resource-manager/templates/deploy-portal.md)
* [Az Azure CLI használatával.](../azure-resource-manager/templates/deploy-cli.md)
* [Rest API használatával.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a felhőrendszerhéj még nyitva van, nem kell másolnia/futtatnia a következő parancsfájl első és második sorát.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Service Bus-névteret. Tekintse meg a többi rövid útmutatót, amelyből megtudhatja, hogyan hozhat létre várólistákat, témaköröket/előfizetéseket, és hogyan használhatja őket:

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Bevezetés a Service Bus-üzenettémák használatába](service-bus-dotnet-how-to-use-topics-subscriptions.md)
