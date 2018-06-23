---
title: Hozzon létre egyéni szerepkörök az Azure PowerShell |} Microsoft Docs
description: Útmutató egyéni szerepkörök az Azure PowerShell szerepköralapú hozzáférés-vezérlést (RBAC) létrehozása. Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6205198d3f9c4ec5bfa2311014cf9b90dcade6dc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320519"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Hozzon létre egyéni szerepkörök az Azure PowerShell

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk ismerteti az Azure PowerShell egyéni szerepkörök létrehozására és kezelésére.

## <a name="prerequisites"></a>Előfeltételek

Egyedi szerepkörök létrehozását, az alábbiak szükségesek:

- Egyéni szerepkörök létrehozásához szükséges engedélyek [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator)
- [Az Azure PowerShell](/powershell/azure/install-azurerm-ps) helyben telepítve

## <a name="list-custom-roles"></a>Egyéni szerepkörök listája

A szerepkörök, amelyek rendelhető hozzá hatókör kilistázhatja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancsot. Az alábbi példa felsorolja az összes szerepkör, amely a kijelölt előfizetés kiosztására használható.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
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

Az alábbi példában csak a kijelölt előfizetés kiosztására használható egyéni szerepköröket sorolja fel.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Ha a kiválasztott előfizetés nem szerepel a `AssignableScopes` a szerepkör az egyéni szerepkör nem jelenik meg.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört hozhat létre a [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) parancsot. A szerepkör szerkezetének kialakítása a két módszer használatával `PSRoleDefinition` objektum vagy egy JSON-sablon. 

### <a name="get-operations-for-a-resource-provider"></a>Erőforrás-szolgáltató műveleteinek beolvasása

Egyéni szerepkörök létrehozásakor fontos tudni, hogy az erőforrás-szolgáltató az összes lehetséges műveletet.
Megtekintheti a listája [erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md) vagy használhatja a [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) parancs használatával beszerezheti az ezeket az információkat.
Például ha a virtuális gépek számára elérhető műveletek ellenőrizni kívánja, használja ezt a parancsot:

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Hozzon létre egy egyéni biztonsági szerepkört a PSRoleDefinition objektum

Amikor a PowerShell segítségével hozzon létre egy egyéni biztonsági szerepkört, használhatja a a [beépített szerepkörök](built-in-roles.md) a kiindulási pont, vagy teljesen új kezdési is. Az első példában ebben a szakaszban egy beépített szerepkör kezdődik, és majd testreszabása további engedélyekkel. Szerkessze a attribútumokat kíván hozzáadni a `Actions`, `NotActions`, vagy `AssignableScopes` , és mentse a módosításokat egy új szerepkörként.

Az alábbi példa kezdődik-e a [virtuális gép közreműködő](built-in-roles.md#virtual-machine-contributor) létrehozni egy egyéni biztonsági szerepkört nevű beépített szerepkör *virtuális gépet üzemeltető*. Az új szerepkör hozzáférést biztosít az összes olvasási műveletek a *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás szolgáltatók és biztosít hozzáférést indítása , indítsa újra, és a virtuális gépek figyelése. Az egyéni biztonsági szerepkört is használható két előfizetésekhez.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
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
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

A következő példa bemutatja, hozzon létre egy másik módja a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört. Hozzon létre egy új kezdődik `PSRoleDefinition` objektum. A művelet műveletek vannak megadva a `perms` változó és kell állítani a `Actions` tulajdonság. A `NotActions` tulajdonsága ehhez beolvassa a `NotActions` a a [virtuális gép közreműködő](built-in-roles.md#virtual-machine-contributor) beépített szerepkör. Mivel a [virtuális gép közreműködő](built-in-roles.md#virtual-machine-contributor) nem rendelkezik ilyennel `NotActions`, nincs szükség a sorban, de azt mutatja, hogyan információk lekérhetők egy másik szerepkört.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Hozzon létre egy egyéni biztonsági szerepkört JSON-sablon

A JSON-sablon az egyéni szerepkör használható legyen a forrás-definíció. Az alábbi példa létrehoz egy egyéni biztonsági szerepkört, amely olvasási hozzáférést biztosít a tárolási és számítási erőforrásokat, hozzáférés támogatásához, és adja a szerepkört két előfizetések. Hozzon létre egy új fájlt `C:\CustomRoles\customrole1.json` a következő példa a. A azonosítóját kell beállítani. `null` a kezdeti szerepkör létrehozásakor egy új ID automatikusan létrejön. 

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Adja hozzá a szerepkört az előfizetés, futtassa a következő PowerShell-parancsot:

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Frissítés egy egyéni biztonsági szerepkört

Hasonló egyéni szerepkör létrehozása, módosíthatja egy meglévő egyéni szerepkör használatával a `PSRoleDefinition` objektum vagy egy JSON-sablon.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Frissítés egy egyéni biztonsági szerepkört a PSRoleDefinition objektummal

Szeretné módosítani egy egyéni biztonsági szerepkört, először használja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancs beolvasása a szerepkör-definíció. A szerepkör-definíció, végezze el a szükséges módosításokat. Végül a [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) parancs menteni a módosított szerepkör-definíció.

A következő példakóddal felveheti a `Microsoft.Insights/diagnosticSettings/*` művelet a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

A következő példa egy Azure-előfizetés hozzáadása a hozzárendelhető hatókörök a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

### <a name="update-a-custom-role-with-a-json-template"></a>Frissítés egy egyéni biztonsági szerepkört egy JSON-sablonhoz

Az előző JSON-sablon használatával könnyen módosíthatja egy meglévő egyéni szerepkör hozzáadásához vagy eltávolításához műveletek. A JSON-sablont módosítani, és adja hozzá az olvasási művelet a hálózatkezeléshez, a következő példában látható módon. A sablon szerepel a definíciók összesítve nem érvényesek egy meglévő definíciójához, ami azt jelenti, hogy a szerepkör pontosan úgy adja meg, ha a sablon megjelenik-e. Is módosítania az azonosítót tartalmazó mezőt, azonosító: a szerepkör. Ha nem tudja biztosan az értéket nem, akkor használhatja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancsmagot, hogy megkapja ezt az információt.

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

A meglévő szerepkör frissítéséhez futtassa a következő PowerShell-parancsot:

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez

Egyéni szerepkör törléséhez használja a [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) parancsot.

A következő példában eltávolítjuk a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni biztonsági szerepkört Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Egyéni szerepkörök az Azure-ban](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md)
