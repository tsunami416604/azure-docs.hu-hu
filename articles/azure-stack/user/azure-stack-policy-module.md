---
title: Az Azure Stack-házirendmodul használata |} A Microsoft Docs
description: Ismerje meg, hogyan korlátozhatja a viselkednek, mint az Azure Stack-előfizetéshez az Azure-előfizetés
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678525"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Az Azure policy az Azure Stack irányelvmodul használata kezelheti

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack irányelvmodul Azure-előfizetés konfigurálása a azonos verziószámozása és szolgáltatás rendelkezésre állása, mint az Azure Stack teszi lehetővé. A modul használja a [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) parancsmaggal hozzon létre egy Azure szabályzatot, amely korlátozza az erőforrástípusok és a egy előfizetésben elérhető szolgáltatások. Ezután használatával hoz létre szabályzat-hozzárendelés, a megfelelő hatókörén belül a [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) parancsmagot. Az-házirend konfigurálása után az Azure Stack-alkalmazások fejlesztéséhez használhatja az Azure-előfizetés.

## <a name="install-the-module"></a>A modul telepítése

1. Az AzureRM PowerShell-modult a szükséges verzió telepítéséhez, az 1. lépésben leírtak szerint [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).
2. [Az Azure Stack-eszközök letöltése a githubról](azure-stack-powershell-download.md).
3. [A PowerShell konfigurálása az Azure Stackkel való használathoz](azure-stack-powershell-configure-user.md).
4. A AzureStack.Policy.psm1 modul importálása:

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>Alkalmazza a szabályzatot az Azure-előfizetés

A következő parancsot használhatja az Azure Stack alapértelmezett szabályzat alkalmazásához ellen az Azure-előfizetés. A parancs futtatása előtt cserélje le a `Azure Subscription Name` az Azure-előfizetése nevére.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Alkalmazza a szabályzatot egy erőforráscsoportot

Érdemes a alkalmazni a házirendeket, amelyek részletesebben. Tegyük fel lehetséges, hogy ugyanahhoz az előfizetéshez futtatott egyéb erőforrásokat. A házirend-alkalmazást egy adott erőforráscsoportban, amellyel tesztelheti az alkalmazásokat az Azure Stack Azure-erőforrások használatával korlátozhatja körét. A következő parancs futtatása előtt cserélje le a `Azure Subscription Name` az Azure-előfizetése nevére.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>A művelet házirend

Miután üzembe helyezte az Azure policy, hibaüzenetet kap meg olyan erőforrások, amelyek tiltják a házirend telepítéséhez.

![Erőforrás-központitelepítési hiba miatt házirendkorlátozás eredménye](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése Visual studióval](azure-stack-deploy-template-visual-studio.md)
