---
title: "Azure Event Hubs-erőforrások kezelése a PowerShell használatával |} Microsoft Docs"
description: "PowerShell-modul segítségével létrehozása és kezelése az Event Hubs"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Az Event Hubs-erőforrások kezelése a PowerShell használatával

A Microsoft Azure PowerShell egy parancsfájl-kezelési környezet, melyekkel automatizálhatja a központi telepítési és felügyeleti Azure-szolgáltatáshoz, és szabályozhatja. Ez a cikk ismerteti, hogyan használható a [Event Hubs Resource Manager PowerShell-modul](/powershell/module/azurerm.eventhub) szeretnék telepíteni és kezelni az Event Hubs entitások (a névterek, a különálló eseményközpontokhoz és a fogyasztói csoportok) a helyi Azure PowerShell-konzolt vagy parancsfájl segítségével.

Az Event Hubs-erőforrások Azure Resource Manager-sablonok segítségével is kezelheti. További információkért lásd: a cikk [az Event Hubs-névtér létrehozása Azure Resource Manager-sablonnal event hub és fogyasztói csoport](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkre lesz szüksége:

* Azure-előfizetés. Előfizetés beszerzésével kapcsolatos további információkért lásd: [beszerzési lehetőségek][purchase options], [ajánlatok][member offers], vagy [ingyenes fiókot][free account].
* Az Azure PowerShell számítógép. Útmutatásért lásd: [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps).
* A PowerShell-parancsfájlok, NuGet-csomagok és a .NET-keretrendszer általános ismertetése.

## <a name="get-started"></a>Bevezetés

Az első lépés-e jelentkezni az Azure-fiókot és az Azure-előfizetés a PowerShell használatával. Kövesse az utasításokat a [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps) próbál bejelentkezni az Azure-fiókjával, majd beolvasása és hozzáférés az erőforrásokhoz az Azure-előfizetéshez.

## <a name="provision-an-event-hubs-namespace"></a>Az Event Hubs névtér kiépítése

Az Event Hubs névterek használatakor is használhatja a [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace), és [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) parancsmagok.

Ez a példa néhány helyi változók hoz létre a parancsfájl; `$Namespace` és `$Location`.

* `$Namespace`a használt működéséhez szeretnénk az Event Hubs névtér esetén.
* `$Location`Az Adatközpont, amelyen azonosítja kiépíti azt a névteret.
* `$CurrentNamespace`a referencia-névtér, amely azt lekérése (vagy hozzon létre) tárolja.

Egy tényleges parancsfájlban `$Namespace` és `$Location` paraméterként kell.

Ez a kijelző, a parancsfájl a következőket teszi:

1. Megkísérli lekérni az Event Hubs névtér a megadott névvel.
2. Ha a névtérben található, mi található jelenti.
3. Ha a névtér nem található, akkor hoz létre a névteret, és az újonnan létrehozott névtér ezután lekéri.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Az eseményközpontok létrehozásához hajtsa végre a névtér-ellenőrzés az előző szakaszban a parancsfájl használatával. Ezt követően a [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) parancsmaggal hozhat létre az event hubs:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Egy felhasználói csoport létrehozása

Az egy fogyasztói csoporton belül az eseményközpontok létrehozásához hajtsa végre a névtér és az event hub a parancsfájlok segítségével az előző szakaszban ellenőrzi. Ezt követően a [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) parancsmaggal hozhat létre a fogyasztói csoporton belül az eseményközpontba. Példa:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Készlet felhasználói metaadatok

A parancsfájlok végrehajtása az előző szakaszokban, után is használhatja a [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) parancsmagot, hogy egy felhasználói csoport, az alábbi példában látható módon tulajdonságainak frissítéséhez:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Távolítsa el az event hubs

Az event hubs létrehozott eltávolításához használja a `Remove-*` parancsmagok, az alábbi példában látható módon:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Következő lépések

- A teljes Event Hubs Resource Manager PowerShell modul dokumentációjában [Itt](/powershell/module/azurerm.eventhub). Ezen a lapon az összes elérhető parancsmagok sorolja fel.
- Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: a cikk [az Event Hubs-névtér létrehozása Azure Resource Manager-sablonnal event hub és fogyasztói csoport](event-hubs-resource-manager-namespace-event-hub.md).
- Információ a [Event Hubs .NET kezelési kódtárakat](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
