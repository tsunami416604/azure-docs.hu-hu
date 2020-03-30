---
title: Felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat felhasználói engedélyeket a DevTest Labs adott laborszabályzataihoz az egyes felhasználók igényei alapján
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
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284212"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan használhatja a PowerShellt egy adott tesztkörnyezet-szabályzathoz adott engedélyek megadására. Így az engedélyek az egyes felhasználók igényei alapján alkalmazhatók. Előfordulhat például, hogy egy adott felhasználónak lehetővé szeretné tenni a virtuális gép házirend-beállításainak módosítását, de a költségházirendeket nem.

## <a name="policies-as-resources"></a>Politikák mint erőforrások
Az [Azure szerepköralapú hozzáférés-vezérlési](../role-based-access-control/role-assignments-portal.md) cikkben tárgyalt a RBAC lehetővé teszi az Azure-erőforrások részletes hozzáférés-kezelését. Az RBAC használatával elkülönítheti a feladatokat a DevOps-csapaton belül, és csak annyi hozzáférést biztosíthat a felhasználóknak, amelyeka feladataik elvégzéséhez szükségük van.

A DevTest Labs egy olyan erőforrástípus, amely engedélyezi a **Microsoft.DevTestLab/labs/policySets/policies/** rbac műveletet. Minden egyes laborházirend egy erőforrás a házirend erőforrás típusában, és lehet rendelni egy RBAC-szerepkör hatóköre.

Ha például olvasási/írási engedélyt szeretne adni a felhasználóknak az **Engedélyezett virtuálisgép-méretek** házirendhez, hozzon létre egy egyéni szerepkört, amely együttműködik a **Microsoft.DevTestLab/labs/policySets/policies/** művelettel, majd hozzárendeli a megfelelő felhasználókat ehhez az egyéni szerepkörhöz a **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**hatókörében.

Ha többet szeretne tudni az egyéni szerepkörökről az RBAC-ban, olvassa el az [Egyéni szerepkörök hozzáférés-vezérlését.](../role-based-access-control/custom-roles.md)

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tesztkörnyezet egyéni szerepkör létrehozása a PowerShell használatával
Az első lépésekhez telepítenie kell az [Azure PowerShellt.](/powershell/azure/install-az-ps) 

Miután beállította az Azure PowerShell-parancsmagokat, a következő feladatokat hajthatja végre:

* Erőforrás-szolgáltató összes műveletének/műveletének listázása
* Adott szerepkörben lévő műveletek felsorolása:
* Egyéni szerepkör létrehozása

Az alábbi PowerShell-parancsfájl példákat mutat be a feladatok végrehajtására:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Engedélyek hozzárendelése egy felhasználóhoz egy adott házirendhez egyéni szerepkörök használatával
Miután definiálta az egyéni szerepköröket, hozzárendelheti őket a felhasználókhoz. Ahhoz, hogy egyéni szerepkört rendeljen egy felhasználóhoz, először be kell szereznie az adott felhasználót képviselő **ObjectId azonosítót.** Ehhez használja a **Get-AzADUser** parancsmagát.

A következő példában a *SomeUser* felhasználó **ObjectId** azonosítója 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Miután rendelkezik a felhasználó **ObjectId** azonosítójával és egy egyéni szerepkörnevével, a szerepkört hozzárendelheti a felhasználóhoz az **Új-AzRoleAssignment** parancsmaggal:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Az előző példában az **AllowedVmSizesInLab-házirend** használatos. Az alábbi rendőri műveletek bármelyikét használhatja:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután megadta a felhasználói engedélyeket az adott tesztkörnyezet-házirendekhez, az alábbi lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborhoz](devtest-lab-add-devtest-user.md)
* [Laborházirendek beállítása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális gépekhez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)

