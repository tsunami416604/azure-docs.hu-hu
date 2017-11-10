---
title: "Az Azure verem irányelvmodul használata |} Microsoft Docs"
description: "Útmutató: az Azure-előfizetéshez az Azure-verem előfizetői viselkednek megkötése"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: sngun
ms.openlocfilehash: 030cb837dd2082f472275bcc42dbd39b7f8b30c0
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Az Azure-verem irányelvmodul Azure házirend kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem irányelvmodul Azure-előfizetés konfigurálja a azonos versioning és a szolgáltatás rendelkezésre állása, Azure verem teszi lehetővé.  A modul használja a **New-AzureRMPolicyAssignment** parancsmaggal hozhat létre Azure házirendet, amely korlátozza az erőforrás típusát és az előfizetést az elérhető szolgáltatások.  Művelet befejeződése után használhatja az Azure-előfizetéshez Azure verem szánt alkalmazások fejlesztéséhez.  

## <a name="install-the-module"></a>A modul telepítése
1. Telepítse a szükséges verziót a AzureRM PowerShell modul, lásd az 1. lépés: [verem Azure PowerShell telepítése](azure-stack-powershell-install.md).   
2. [Töltse le az Azure-verem eszközök a Githubról](azure-stack-powershell-download.md)  
3. [A PowerShell konfigurálása az Azure Stack szolgáltatással való használathoz](azure-stack-powershell-configure-user.md)

4. A AzureStack.Policy.psm1 modul importálása:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>A házirend vonatkozik
Az alábbi parancs segítségével egy alapértelmezett Azure verem létrehozását az Azure-előfizetéshez ellen. Előtt fut, cserélje le a *Azure előfizetés neve* Azure-előfizetéséhez.

```PowerShell
Login-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>A házirend vonatkozik egy erőforráscsoportot
Érdemes lehet alkalmazni a házirendeket részletesebb metódusban.  Például előfordulhat, hogy más erőforrásaihoz, ugyanazt az előfizetést.  A házirend-alkalmazást egy adott erőforráscsoportban található, amely lehetővé teszi az alkalmazások használata az Azure-erőforrások Azure verem hatókörét megadhatja. Előtt fut, cserélje le a *Azure előfizetés neve* saját Azure-előfizetés nevét.

```PowerShell
Login-AzureRmAccount
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>A művelet házirend
Miután az Azure házirend telepítése után, hibaüzenetet kap, amikor megpróbál telepíteni egy erőforrást, hogy a házirend.  

![Erőforrás központi telepítési problémái miatt házirend megkötés eredménye](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Következő lépések
[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)

[Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)

[Sablonok a Visual Studio telepítése](azure-stack-deploy-template-visual-studio.md)
