---
title: Az Azure verem irányelvmodul használata |} Microsoft Docs
description: 'Útmutató: az Azure-előfizetéshez az Azure-verem előfizetői viselkednek megkötése'
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Az Azure-verem irányelvmodul Azure házirend kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem irányelvmodul Azure-előfizetés konfigurálja a azonos versioning és a szolgáltatás rendelkezésre állása, Azure verem teszi lehetővé.  A modul használja a **New-AzureRMPolicyAssignment** parancsmaggal hozhat létre Azure házirendet, amely korlátozza az erőforrás típusát és az előfizetést az elérhető szolgáltatások.  Miután a házirendet, használhatja az Azure-előfizetéshez Azure verem szánt alkalmazások fejlesztéséhez.

## <a name="install-the-module"></a>A modul telepítése

1. Telepítse a szükséges verziót a AzureRM PowerShell modul, lásd az 1. lépés: [verem Azure PowerShell telepítése](azure-stack-powershell-install.md).
2. [Töltse le az Azure-verem eszközök a Githubról](azure-stack-powershell-download.md)
3. [A PowerShell konfigurálása az Azure Stack szolgáltatással való használathoz](azure-stack-powershell-configure-user.md)

4. A AzureStack.Policy.psm1 modul importálása:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Azure-előfizetés házirend alkalmazása

A következő paranccsal egy alapértelmezett Azure verem házirend alkalmazása az Azure-előfizetéshez ellen. A parancs futtatása előtt cserélje le a *Azure előfizetés neve* Azure-előfizetéséhez.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>A házirend vonatkozik egy erőforráscsoportot

Érdemes lehet alkalmazni a házirendeket, amelyek részletesebb. Például lehetséges, hogy más erőforrásaihoz, ugyanazt az előfizetést. A házirend-alkalmazást egy adott erőforráscsoportban található, amely lehetővé teszi az alkalmazások használata az Azure-erőforrások Azure verem hatókörét megadhatja. A következő parancs futtatása előtt cserélje le a *Azure előfizetés neve* saját Azure-előfizetés nevét.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>A művelet házirend

Miután az Azure házirend telepítése után, hibaüzenetet kap, amikor megpróbál telepíteni egy erőforrást, hogy a házirend.

![Erőforrás központi telepítési problémái miatt házirend megkötés eredménye](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)
* [Sablonok a Visual Studio telepítése](azure-stack-deploy-template-visual-studio.md)
