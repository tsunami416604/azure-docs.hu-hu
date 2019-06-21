---
title: Hozzon létre egy eseményközpont fogyasztói csoport – Azure Event Hubs |} A Microsoft Docs
description: Event Hubs-névtér létrehozása egy eseményközponttal, valamint egy fogyasztói csoportot az Azure Resource Manager-sablonok használatával
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/14/2019
ms.author: shvija
ms.openlocfilehash: 007e016672f8548956b37b961805183a504d6bf0
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154085"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Gyors útmutató: Eseményközpont létrehozása Azure Resource Manager-sablon használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban létrehozhat egy eseményközpont használatával egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-overview.md). Típusú névtér létrehozása egy Azure Resource Manager-sablon telepítése [az Event Hubs](event-hubs-what-is-event-hubs.md), és egy eseményközpont. A cikk bemutatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. Sablonok létrehozásával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése][Authoring Azure Resource Manager templates]. A JSON-szintaxist és a egy sablonban használandó tulajdonságokat: [hátralékának erőforrástípusok](/azure/templates/microsoft.eventhub/allversions).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ez a rövid egy [meglévő Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json). További sablonminták talál [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

A sablon üzembe helyezéséhez:

1. Válassza ki **kipróbálás** az az alábbi kód letiltása, és kövesse az utasításokat követve jelentkezzen be az Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Eseményközpont létrehozása néhány percet vesz igénybe.

1. Válassza ki **másolási** másolása a PowerShell-parancsfájlt.
1. Kattintson a jobb gombbal a rendszerhéj-konzolon, és válassza **beillesztési**.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A telepítés ellenőrzése, vagy nyissa meg az erőforráscsoport a [az Azure portal](https://portal.azure.com), vagy használja a következő Azure PowerShell-parancsfájlt.  Ha a Cloud shellben folyamatban, nem kell másolási/futtatásakor az első sort (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a Cloud shellben folyamatban, nem kell másolási/futtatásakor az első sort (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott Event Hubs-névtér és eseményközpont névtér. Események küldése (vagy) események fogadása az event hubs részletes utasításokért lásd: a **események küldéséhez és fogadásához** oktatóanyagok:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldése)](event-hubs-c-getstarted-send.md)
- [Az Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
