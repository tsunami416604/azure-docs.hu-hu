---
title: Szerepköralapú hozzáférés-vezérlést (RBAC) az Azure PowerShell kezelése |} Microsoft Docs
description: Hogyan kezelheti az Azure PowerShell, beleértve a szerepköröket, a szerepkörök hozzárendelése és a szerepkör-hozzárendelések törlése RBAC.
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
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 00646187da1f93c01c3a57b50905239afd5e2bc8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266798"
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Az Azure PowerShell szerepköralapú hozzáférés-vezérlés kezelése
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

Szerepköralapú hozzáférés-vezérléssel (RBAC) a felhasználók, csoportok és szolgáltatásnevekről hozzáférési által egy adott hatókörhöz szerepkörök hozzárendelése definiálni. Ez a cikk ismerteti az Azure PowerShell-hozzáférés kezelése.

## <a name="prerequisites"></a>Előfeltételek

PowerShell kezeléséhez RBAC használata előtt kell a következők egyikét:

* [Azure-felhőbe rendszerhéj PowerShell](/azure/cloud-shell/overview)
* [Az Azure PowerShell 5.1.0 vagy újabb verzió](/powershell/azure/install-azurerm-ps)

## <a name="list-roles"></a>Lista szerepkörök

### <a name="list-all-available-roles"></a>Elérhető szerepkörök felsorolása

A lista RBAC szerepkörökhöz kiosztására használható, és a műveletek, amelyhez azokat hozzáférést, a vizsgálandó használó [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Egy adott szerepkör felsorolása

Kilistázhatja az egy adott szerepkör [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>Egy adott szerepkör JSON formátumban felsorolása

Kilistázhatja az egy adott szerepkör JSON formátumban [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>Egy szerepkör lista műveletek

Kilistázhatja az adott szerepkörhöz műveletek [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="see-who-has-access"></a>Lásd a kinek van hozzáférése:

A listában a Szerepalapú hozzáférés-hozzárendelések használja [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

### <a name="list-role-assignments-at-a-specific-scope"></a>Szerepkör-hozzárendelések listáját egy adott hatókörhöz

A megadott előfizetés, erőforráscsoporthoz vagy erőforrás szerepkör-hozzárendelések tekintheti meg. Például az összes aktív hozzárendelése egy erőforráscsoport, használja a [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>A felhasználóhoz rendelt lista szerepkörök

Kilistázhatja az adott felhasználóhoz rendelt összes szerepkör [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

Kilistázhatja az adott felhasználóhoz rendelt összes szerepkör és a szerepköröket, amelyek a csoportok, amelyhez a felhasználó tartozik [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Lista klasszikus szolgáltatás-rendszergazda és a szerepkör-hozzárendelések társfelügyeletű

A hozzáférés-hozzárendelések a hagyományos előfizetés rendszergazdai és társrendszergazdák listában használja [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment):

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Hozzáférés biztosítása

### <a name="search-for-object-ids"></a>Objektumazonosítók keresése

A szerepkör hozzárendeléséhez kell azonosítani az objektum (felhasználó, csoport vagy alkalmazás) és a hatókör.

Ha az előfizetés-azonosító nem tudja, megtalálja a a **előfizetések** panel az Azure-portálon, vagy használhat [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

Az objektum azonosítója az Azure AD-csoport, amelyet [Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup):

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

Az objektum azonosítója az Azure AD szolgáltatás egyszerű vagy az alkalmazás használatához [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal):

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Az alkalmazást az előfizetési hatókört szerepkör hozzárendelése

Az előfizetés hatókörből alkalmazáshoz való hozzáférés biztosításához használja [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>A szerepkör hozzárendelése egy felhasználóhoz a erőforrás csoport hatóköre:

Hozzáférés biztosítása a felhasználók a erőforrás hatókörét, használjon [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>A szerepkör hozzárendelése a erőforrás hatókörben csoporthoz

Hozzáférés biztosítása a erőforrás hatókörben egy csoportot, használjon [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

## <a name="remove-access"></a>Hozzáférés eltávolítása

A felhasználók, csoportok és alkalmazások elérésének eltávolításához használja a [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment):

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>Egyéni szerepkörök listája

A szerepkörök, amelyek rendelhető hozzá hatókör kilistázhatja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancsot.

Az alábbi példa felsorolja az összes szerepkör, amely a kijelölt előfizetés kiosztására használható.

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

### <a name="create-a-role-with-psroledefinition-object"></a>Hozzon létre egy szerepkör PSRoleDefinition objektum

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

A következő példa bemutatja, hozzon létre egy másik módja a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört. Hozzon létre egy új PSRoleDefinition objektum kezdődik. A művelet műveletek vannak megadva a `perms` változó és kell állítani a `Actions` tulajdonság. A `NotActions` tulajdonsága ehhez beolvassa a `NotActions` a a [virtuális gép közreműködő](built-in-roles.md#virtual-machine-contributor) beépített szerepkör. Mivel a [virtuális gép közreműködő](built-in-roles.md#virtual-machine-contributor) nem rendelkezik ilyennel `NotActions`, nincs szükség a sorban, de azt mutatja, hogyan információk lekérhetők egy másik szerepkört.

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

### <a name="create-role-with-json-template"></a>A JSON-sablon szerepkör létrehozása

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

## <a name="modify-a-custom-role"></a>Egyéni szerepkör módosítása

Hasonló egyéni szerepkör létrehozása, módosíthatja egy meglévő egyéni szerepkör használatával a `PSRoleDefinition` objektum vagy egy JSON-sablon.

### <a name="modify-role-with-psroledefinition-object"></a>PSRoleDefinition objektummal szerepkör módosítása

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

### <a name="modify-role-with-json-template"></a>Szerepkör JSON-sablon módosítása

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

## <a name="see-also"></a>Lásd még

* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
