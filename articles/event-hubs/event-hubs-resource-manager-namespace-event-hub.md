---
title: 'Rövid útmutató: Hozzon létre egy eseményközpontot fogyasztói csoporttal – Azure Event Hubs'
description: 'Rövid útmutató: Event Hubs névtér létrehozása eseményközponttal és fogyasztói csoporttal az Azure Resource Manager-sablonok használatával'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: subject-armqs
ms.openlocfilehash: c2221fe5b5ab38afbdde167e5bcbf6b47ed4f861
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79528081"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Rövid útmutató: Eseményközpont létrehozása Azure Resource Manager-sablon használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket. Ebben a rövid útmutatóban egy Azure [Resource Manager-sablon](../azure-resource-manager/management/overview.md)használatával hoz létre egy eseményközpontot. Egy Azure Resource Manager-sablont üzembe helyez egy [Event Hubs](event-hubs-what-is-event-hubs.md)típusú névtér létrehozásához, egyetlen eseményközponttal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Nincs.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/)

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

A sablonban meghatározott erőforrások a következők:

- [**Microsoft.EventHub/névterek**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/namespaces/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

További sablonmintákat az [Azure gyorsútmutató-sablonok](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)ban talál.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon telepítése:

1. Válassza **a Próbálja ki** a következő kódblokkból, majd kövesse az utasításokat az Azure Cloud Shellbe való bejelentkezéshez.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Az eseményközpont létrehozása néhány percet vesz igénybe.

1. Válassza **a Másolás lehetőséget** a PowerShell-parancsfájl másolásához.
1. Kattintson a jobb gombbal a rendszerhéj-konzolra, és válassza **a Beillesztés parancsot.**

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

A központi telepítés ellenőrzéséhez nyissa meg az erőforráscsoportot az [Azure Portalon,](https://portal.azure.com)vagy használja a következő Azure PowerShell-parancsfájlt.  Ha a Cloud Shell még nyitva van, nem kell másolni/futtatni az első sort (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ha a Cloud Shell még nyitva van, nem kell másolni/futtatni az első sort (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Event Hubs névteret, és egy eseményközpontot a névtérben. Az események eseményközpontból történő (vagy) fogadására vonatkozó részletes utasításokért tekintse meg az **Események küldése és fogadása** oktatóanyagokat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
