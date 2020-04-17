---
title: Az Azure-szabályzat üzembe helyezése delegált előfizetésekhez nagy méretekben
description: Ismerje meg, hogy az Azure delegált erőforrás-kezelése hogyan teszi lehetővé egy szabályzat-definíció és házirend-hozzárendelés üzembe helyezését több bérlőközött.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481825"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Az Azure-szabályzat üzembe helyezése delegált előfizetésekhez nagy méretekben

Szolgáltatóként előfordulhat, hogy több ügyfél-bérlőt is beszervezett az Azure delegált erőforrás-kezeléséhez. [Az Azure Lighthouse](../overview.md) lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn keresztül hajtsanak végre műveleteket, hatékonyabbá téve a felügyeleti feladatokat.

Ez a témakör bemutatja, hogyan használhatja az [Azure Policy-t](../../governance/policy/index.yml) egy szabályzatdefiníció és a szabályzat-hozzárendelés üzembe helyezéséhez több bérlőn a PowerShell-parancsok használatával. Ebben a példában a szabályzat definíciója biztosítja, hogy a tárfiókok csak HTTPS-forgalom engedélyezésével védett.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Az Azure Resource Graph használata az ügyfelek bérlői közötti lekérdezéshez

Az [Azure Resource Graph](../../governance/resource-graph/index.yml) segítségével lekérdezheti az összes előfizetést az ügyfél-bérlők, amely kezeli. Ebben a példában azonosítjuk azokat az előfizetéseket, amelyek jelenleg nem igényelnek HTTPS-forgalmat, azonosítjuk azokat az előfizetéseket.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Házirend üzembe helyezése több ügyfél-bérlő között

Az alábbi példa bemutatja, hogyan használhatja az [Azure Resource Manager-sablont](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) egy szabályzatdefiníció és házirend-hozzárendelés üzembe helyezéséhez delegált előfizetések között több ügyfél-bérlőben. Ez a házirend-definíció megköveteli, hogy az összes tárfiókok HTTPS-forgalmat használjanak, megakadályozva az új tárfiókok létrehozását, amelyek nem felelnek meg, és a meglévő tárfiókok megjelölése a beállítás nem megfelelőként való megjelölése nélkül.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>A házirend központi telepítésének ellenőrzése

Miután telepítette az Azure Resource Manager sablont, meggyőződhet arról, hogy a szabályzatdefiníció sikeresen alkalmazva lett-e, ha megpróbál létrehozni egy tárfiókot **az EnableHttpsTrafficOnly** beállítással, **amely hamis** az egyik delegált előfizetésében. A házirend-hozzárendelés miatt nem lehet létrehozni ezt a tárfiókot.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, távolítsa el a központi telepítés által létrehozott házirend-definíciót és hozzárendelést.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szabályzatról.](../../governance/policy/index.yml)
- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
