---
title: Egyéni szerepkörök létrehozása vagy frissítése az Azure PowerShell használatával az Azure PowerShell használatával
description: Megtudhatja, hogyan listázhatja, hozhat létre, frissítheti vagy törölheti az egyéni szerepköröket az Azure PowerShell használatával szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrásokhoz.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3c72e04ff7a08fecc2ef352a5879898c4c6d41c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062280"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-powershell"></a>Egyéni szerepkörök létrehozása vagy frissítése az Azure PowerShell használatával az Azure-erőforrásokhoz

> [!IMPORTANT]
> Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet egyedi igényeinek, létrehozhat saját egyéni szerepköröket. Ez a cikk ismerteti, hogyan listázheti, hozhat létre, frissítheti vagy törölheti az egyéni szerepköröket az Azure PowerShell használatával.

Az egyéni szerepkör létrehozásáról részletes oktatóanyagaz [Oktatóanyag: Egyéni szerepkör létrehozása az Azure PowerShell használatával című témakörben látható.](tutorial-custom-role-powershell.md)

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A hatókörhozzárendeléshez rendelkezésre álló szerepkörök listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot. A következő példa a kijelölt előfizetésben a hozzárendeléshez rendelkezésre álló összes szerepkört felsorolja.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

A következő példa csak azokat az egyéni szerepköröket sorolja fel, amelyek a kijelölt előfizetésben hozzárendeléshez érhetők el.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Ha a kijelölt előfizetés nem `AssignableScopes` szerepel a szerepkörben, az egyéni szerepkör nem jelenik meg.

## <a name="list-a-custom-role-definition"></a>Egyéni szerepkör-definíció felsorolása

Egyéni szerepkör-definíció listázásához használja a [Get-AzRoleDefinition programot.](/powershell/module/az.resources/get-azroledefinition) Ez ugyanaz a parancs, mint amit a beépített szerepkörhöz használ.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

A következő példa csak a szerepkör feladatait sorolja fel:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) parancsot. A szerepkör strukturálásának két `PSRoleDefinition` módja van, objektum vagy JSON sablon használatával. 

### <a name="get-operations-for-a-resource-provider"></a>Erőforrás-szolgáltató műveleteinek beszereznie

Egyéni szerepkörök létrehozásakor fontos tudni az összes lehetséges műveletet az erőforrás-szolgáltatóktól.
Megtekintheti az [erőforrás-szolgáltató i. műveletek](resource-provider-operations.md) listáját, vagy a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) paranccsal lejuthat ezekre az információkra.
Ha például ellenőrizni szeretné a virtuális gépek összes rendelkezésre álló műveletét, használja ezt a parancsot:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Egyéni szerepkör létrehozása a PSRoleDefinition objektummal

Ha a PowerShell használatával egyéni szerepkört hoz létre, használhatja a [beépített szerepkörök](built-in-roles.md) egyikét kiindulási pontként, vagy kezdheti a nulláról. Ebben a szakaszban az első példa egy beépített szerepkörrel kezdődik, majd további engedélyekkel szabja testre. Módosítsa az attribútumokat `Actions`a `NotActions`lehetőség `AssignableScopes` hozzáadásához, vagy a kívánt kívánt attribútumokat, majd mentse a módosításokat új szerepkörként.

A következő példa a [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) beépített szerepkörrel kezdődik, hogy hozzon létre egy *virtuálisgép-üzemeltető*nevű egyéni szerepkört. Az új szerepkör hozzáférést biztosít a *Microsoft.Compute,* *a Microsoft.Storage*és a *Microsoft.Network* erőforrás-szolgáltatók összes olvasási műveletéhez, és hozzáférést biztosít a virtuális gépek indításához, újraindításához és figyeléséhez. Az egyéni szerepkör két előfizetésben használható.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

A következő példa bemutatja a *virtuálisgép-üzemeltető* egyéni szerepkör létrehozásának egy másik módját. Egy új `PSRoleDefinition` objektum létrehozásával kezdődik. A műveletműveletek a `perms` változóban vannak megadva, és a `Actions` tulajdonságra vannak állítva. A `NotActions` tulajdonság úgy van `NotActions` beállítva, hogy olvassa be a [virtuális gép közreműködője](built-in-roles.md#virtual-machine-contributor) beépített szerepkörből. Mivel [a virtuálisgép közreműködője](built-in-roles.md#virtual-machine-contributor) nem rendelkezik ilyenvel, `NotActions`ez a sor nem kötelező, de megmutatja, hogyan lehet adatokat beolvasni egy másik szerepkörből.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Egyéni szerepkör létrehozása JSON-sablonnal

A JSON-sablon használható az egyéni szerepkör forrásdefiníciójaként. A következő példa létrehoz egy egyéni szerepkört, amely lehetővé teszi az olvasási hozzáférést a tárolási és számítási erőforrásokhoz, a támogatáshoz való hozzáférést, és hozzáadja ezt a szerepkört két előfizetéshez. Hozzon létre `C:\CustomRoles\customrole1.json` egy új fájlt a következő példával. Az azonosítót a `null` kezdeti szerepkör-létrehozáskor kell beállítani, mivel az új azonosító automatikusan jön létre. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

A szerepkör hozzáadása az előfizetésekhez, futtassa a következő PowerShell-parancsot:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Az egyéni szerepkör létrehozásához hasonlóan egy meglévő egyéni `PSRoleDefinition` szerepkört is módosíthat az objektum vagy egy JSON-sablon használatával.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Egyéni szerepkör frissítése a PSRoleDefinition objektummal

Egyéni szerepkör módosításához először a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) paranccsal olvassa be a szerepkör-definíciót. Másodszor, a kívánt módosításokat a szerepkör-definíció. Végül a [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) paranccsal mentse a módosított szerepkör-definíciót.

A következő példa `Microsoft.Insights/diagnosticSettings/*` hozzáadja a műveletet a *Virtuálisgép-üzemeltető* egyéni szerepkörhöz.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

A következő példa hozzáad egy Azure-előfizetést a *virtuálisgép-üzemeltető* egyéni szerepkör hozzárendelhető hatóköreihez.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

A következő példa hozzáad egy felügyeleti csoportot a `AssignableScopes` *Virtuálisgép-kezelő* egyéni szerepkörhöz. Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Egyéni szerepkör frissítése JSON-sablonnal

Az előző JSON-sablon használatával egyszerűen módosíthatja a meglévő egyéni szerepkört a műveletek hozzáadásához vagy eltávolításához. Frissítse a JSON-sablont, és adja hozzá a hálózati olvasási műveletet az alábbi példában látható módon. A sablonban felsorolt definíciók nem lesznek összesítve egy meglévő definícióra, ami azt jelenti, hogy a szerepkör pontosan úgy jelenik meg, ahogy a sablonban megadott. Az azonosítómezőt is frissítenie kell a szerepkör azonosítójával. Ha nem biztos abban, hogy mi ez az érték, a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsmag segítségével lejuthat erre az információra.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

A meglévő szerepkör frissítéséhez futtassa a következő PowerShell-parancsot:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja az [Eltávolítás-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) parancsot.

A következő példa eltávolítja a *Virtuálisgép-üzemeltető* egyéni szerepkört.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrásokhoz az Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatóműveletei](resource-provider-operations.md)
