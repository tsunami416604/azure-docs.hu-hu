---
title: Felhasználói engedélyek megadása meghatározott labor-házirendekhez | Microsoft Docs
description: Megtudhatja, hogyan adhat felhasználói engedélyeket a DevTest Labs adott labor-házirendjeihez az egyes felhasználói igények alapján
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cfacba2a7cdba20bd5a05c9ca5898194c31c2e68
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855784"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan lehet a PowerShell használatával engedélyeket adni a felhasználóknak egy adott tesztkörnyezet-házirendhez. Így az engedélyek az egyes felhasználók igényei alapján alkalmazhatók. Előfordulhat például, hogy egy adott felhasználó számára engedélyezni szeretné a virtuális gép házirend-beállításainak módosítását, de a költségeket nem.

## <a name="policies-as-resources"></a>Szabályzatok erőforrásként
Az [Azure szerepköralapú Access Control](../role-based-access-control/role-assignments-portal.md) cikkében leírtaknak megfelelően a RBAC lehetővé teszi az erőforrások részletes hozzáférés-kezelését az Azure-hoz. A RBAC segítségével elkülönítheti a feladatokat a DevOps-csapaton belül, és csak a felhasználók számára biztosíthatja a feladatok elvégzéséhez szükséges hozzáférést.

A DevTest Labs szolgáltatásban a szabályzat olyan erőforrástípus, amely engedélyezi a **Microsoft. segédösszetevője/Labs/policySets/policies/** RBAC műveletet. Az egyes tesztkörnyezet-házirendek a házirend-erőforrástípus egyik erőforrása, amely hatókörként is hozzárendelhető egy RBAC-szerepkörhöz.

Például ahhoz, hogy a felhasználók olvasási/írási engedélyt adjanak az engedélyezett virtuálisgép- **méretek** házirendjéhez, létre kell hoznia egy egyéni szerepkört, amely együttműködik a **Microsoft. segédösszetevője/Labs/policySets/** policys/Action szolgáltatással, majd hozzárendeli a megfelelő felhasználókat az egyéni szerepkörhöz a **Microsoft. segédösszetevője/Labs/policySets/policies/AllowedVmSizesInLab**hatókörében.

Ha többet szeretne megtudni a RBAC található egyéni szerepkörökről, tekintse meg az [Egyéni szerepkörök hozzáférés-vezérlését](../role-based-access-control/custom-roles.md)ismertető témakört.

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tesztkörnyezet egyéni szerepkörének létrehozása a PowerShell használatával
Az első lépésekhez [telepítenie kell Azure PowerShell](/powershell/azure/install-az-ps). 

A Azure PowerShell-parancsmagok beállítása után a következő feladatokat végezheti el:

* Erőforrás-szolgáltató összes műveletének és műveletének listázása
* Egy adott szerepkör műveleteinek listázása:
* Egyéni szerepkör létrehozása

A következő PowerShell-parancsfájl példákat mutat be ezeknek a feladatoknak a végrehajtásához:

```azurepowershell
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
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Engedélyek kiosztása egy felhasználóhoz egy adott szabályzathoz egyéni szerepkörök használatával
Miután definiálta az egyéni szerepköröket, hozzárendelheti azokat a felhasználókhoz. Ha egyéni szerepkört szeretne hozzárendelni egy felhasználóhoz, először be kell szereznie az adott felhasználót jelképező **ObjectId** . Ehhez használja a **Get-AzADUser** parancsmagot.

A következő példában a *SomeUser* felhasználó **ObjectId** 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

Miután megkapta a **ObjectId** a felhasználóhoz és egy egyéni szerepkör nevét, hozzárendelheti az adott szerepkört a felhasználóhoz a **New-AzRoleAssignment** parancsmag használatával:

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

Az előző példában a rendszer a **AllowedVmSizesInLab** szabályzatot használja. A következő házirendek bármelyike használható:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Ha meghatározott labor-házirendekhez adott meg felhasználói engedélyeket, a következő lépéseket érdemes figyelembe vennie:

* [Biztonságos hozzáférés a laborhoz](devtest-lab-add-devtest-user.md)
* [Tesztkörnyezet-házirendek beállítása](devtest-lab-set-lab-policy.md)
* [Tesztkörnyezet létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális gépekhez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)

