---
title: Felhasználói engedélyek adott labor szabályzatokkal |} Microsoft Docs
description: 'Útmutató: a DevTest Labs szolgáltatásban minden egyes felhasználói igények alapján adott labor házirendek felhasználói engedélyeket'
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2b81c23b5cf9ea5d4bfc47d36ae251f762ffad11
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787457"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>A konkrét labor házirendek felhasználói engedélyek
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan lehet egy adott labor házirend felhasználók engedélyeket a PowerShell használatával. Ily módon engedélyek alkalmazhatók minden felhasználói igények alapján. Például érdemes egy adott felhasználó megváltoztathatja a virtuális gép házirend-beállításokat, de nem a költségek házirendek megadását.

## <a name="policies-as-resources"></a>Erőforrásként házirendek
A bemutatott a [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) RBAC a cikkben lehetővé teszi, hogy az Azure-erőforrások részletes hozzáféréskezelést. Az RBAC használata, feladatokat elkülönítse a DevOps munkacsoporton belül, és csak olyan mértékű hozzáférést biztosítania a felhasználók számára, hogy be kell elvégezni a munkájukat.

A DevTest Labs szolgáltatásban, a házirend, amely lehetővé teszi a Szerepalapú művelet erőforrástípus **Microsoft.DevTestLab/labs/policySets/policies/**. Minden tesztkörnyezeti házirend házirend erőforrástípus az erőforráshoz, és hatóköreként RBAC szerepkörhöz is hozzárendelhető.

Például ahhoz, hogy a felhasználók olvasási/írási engedélyeket a **engedélyezett Virtuálisgép-méretek** házirend, akkor létre egy egyéni biztonsági szerepkört, amely kompatibilis a **Microsoft.DevTestLab/labs/policySets/policies/*** a művelet, majd rendelje hozzá a megfelelő felhasználók az egyéni szerepkör hatókörébe tartozó **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Egyéni szerepkörök az RBAC kapcsolatos további tudnivalókért tekintse meg a [egyéni szerepkörök hozzáférés-vezérlés](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell-lel labor egyéni szerepkör létrehozása
Ahhoz, hogy az első lépések, olvassa el az alábbi cikket, amely fogja azt ismertetik, hogyan telepítse és konfigurálja az Azure PowerShell-parancsmagok szüksége: [ https://azure.microsoft.com/blog/azps-1-0-pre ](https://azure.microsoft.com/blog/azps-1-0-pre).

Miután beállította az Azure PowerShell-parancsmagok, a következő feladatokat végezheti el:

* A műveletek/műveleteket egy erőforrás-szolgáltató listázása
* Egy adott szerepkörben műveletek listája:
* Egyéni szerepkör létrehozása

A következő PowerShell-parancsfájl a feladatok végrehajtásához példákat szemlélteti:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Engedélyek hozzárendelése a felhasználó egy adott házirend segítségével egyéni szerepkörök
Miután meghatározta a egyéni szerepkörök, hozzárendelheti azokat a felhasználókat. Ahhoz, hogy egy egyéni biztonsági szerepkört rendel egy felhasználói, be kell szereznie a **ObjectId** képviselő, hogy a felhasználó. Ehhez használja a **Get-AzureRmADUser** parancsmag.

A következő példában a **ObjectId** , a *SomeUser* felhasználói 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Ha elvégezte a **ObjectId** a felhasználó és egy egyéni szerepkör nevét, a felhasználó számára, hogy szerepkört is rendelhet a **New-AzureRmRoleAssignment** parancsmagot:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Az előző példában a **AllowedVmSizesInLab** házirendet használja. Használhatja a következő házirendhez:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Egyszer már engedélyeket felhasználó adott labor házirendek, az alábbiakban néhány lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md)
* [Laborszabályzatok megadása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)

