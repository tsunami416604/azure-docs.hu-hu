---
title: Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára szerepköralapú hozzáférés-vezérlés (RBAC) és Azure PowerShell használatával. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4eaf59200295a25498d3c8b84196e73a703b055d
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995250"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure PowerShell használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ez a cikk azt ismerteti, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését a RBAC és a Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A hozzáférés kezeléséhez a következők egyikére van szükség:

* [PowerShell a Azure Cloud Shellban](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Szerepkörök felsorolása

### <a name="list-all-available-roles"></a>Az összes elérhető szerepkör listázása

A [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)használatával listázhatja a hozzárendeléshez rendelkezésre álló RBAC-szerepköröket, és ellenőrizheti azokat a műveleteket, amelyekhez hozzáférést biztosítanak.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
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

### <a name="list-a-specific-role"></a>Adott szerepkör listázása

Egy adott szerepkör listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

### <a name="list-a-role-definition-in-json-format"></a>Szerepkör-definíciók listázása JSON formátumban

Ha JSON formátumú szerepkör-definíciót szeretne listázni, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>Szerepkör műveleteinek listázása

Egy adott szerepkör műveleteinek listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

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

## <a name="list-access"></a>Hozzáférések felsorolása

A RBAC-ben a hozzáférés listázásához listázhatja a szerepkör-hozzárendeléseket.

### <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Az adott felhasználóhoz rendelt összes szerepkör listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)használatával listázhatja az adott felhasználóhoz rendelt összes szerepkört, valamint azokat a csoportokat, amelyekhez a felhasználó tartozik.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Szerepkör-hozzárendelések listázása erőforráscsoport-hatókörben

Egy erőforráscsoport-hatókör összes szerepkör-hozzárendelésének listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Szerepkör-hozzárendelések listázása előfizetési hatókörben

Az előfizetési hatókör összes szerepkör-hozzárendelésének listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Az előfizetés-azonosító lekéréséhez keresse meg a Azure Portal **előfizetések** paneljén, vagy használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Szerepkör-hozzárendelések listázása egy felügyeleti csoport hatókörében

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). A felügyeleti csoport AZONOSÍTÓjának beszerzéséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy a [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)is használható.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Szerepkör-hozzárendelések listázása a klasszikus szolgáltatás-rendszergazda és a társ-rendszergazdák számára

A klasszikus előfizetés-rendszergazda és a társ-rendszergazdák szerepkör-hozzárendeléseinek listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

### <a name="search-for-object-ids"></a>Objektum-azonosítók keresése

Szerepkör hozzárendeléséhez meg kell adnia az objektumot (felhasználó, csoport vagy alkalmazás) és a hatókört is.

Az előfizetés-azonosító lekéréséhez keresse meg a Azure Portal **előfizetések** paneljén, vagy használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

Az Azure AD-felhasználóhoz tartozó objektumazonosító beszerzéséhez használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Az Azure AD-csoporthoz tartozó objektumazonosító beszerzéséhez használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Egy Azure AD-szolgáltatásnév vagy-alkalmazás objektumazonosító beszerzéséhez használja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Szerepkör-hozzárendelés létrehozása egy felhasználóhoz erőforráscsoport-hatókörben

Ha egy erőforráscsoport hatókörében szeretne hozzáférést adni egy felhasználóhoz, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Szerepkör-hozzárendelés létrehozása az egyedi szerepkör-azonosító használatával

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

Ha szerepkör-hozzárendelést szeretne létrehozni a szerepkör neve helyett az egyedi szerepkör-azonosító használatával, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

A következő példa a [virtuális gépi közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört *alain@example.com* rendeli a felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében. Az egyedi szerepkör-azonosító beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy az [Azure-erőforrások beépített szerepköreit](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Szerepkör-hozzárendelés létrehozása egy erőforrás-hatókörhöz tartozó csoporthoz

Ha erőforrás-hatókörben szeretne hozzáférést adni egy csoporthoz, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

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

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Szerepkör-hozzárendelés létrehozása egy alkalmazáshoz előfizetési hatókörben

Ha egy alkalmazáshoz hozzáférést szeretne biztosítani egy előfizetési hatókörben, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

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

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Szerepkör-hozzárendelés létrehozása egy felhasználóhoz egy felügyeleti csoport hatókörében

Ha hozzáférést szeretne adni egy felhasználónak egy felügyeleti csoport hatókörében, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot. A felügyeleti csoport AZONOSÍTÓjának beszerzéséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy a [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)is használható.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-access"></a>Hozzáférés megszüntetése

A RBAC a hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)használatával.

A következő példa eltávolítja a *virtuálisgép-közreműködő* szerepkör-hozzárendelést az *Alain\@example.com* -felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Az alábbi példa eltávolítja a < role_name > szerepkört a < object_id > egy előfizetési hatókörben.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

A következő példa eltávolítja a < role_name > szerepkört a felügyeleti csoport hatókörének < object_id >.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Ha a következő hibaüzenet jelenik meg: "A megadott információ nem képezhető le szerepkör-hozzárendelésre", győződjön meg arról, hogy a vagy `-Scope` `-ResourceGroupName` a paramétereket is megadja. További információ: [Az Azure-erőforrások RBAC kapcsolatos hibák megoldása](troubleshooting.md#role-assignments-without-a-security-principal).

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure-erőforrásokhoz való hozzáférés biztosítása a RBAC és a Azure PowerShell használatával](tutorial-role-assignments-group-powershell.md)
- [Oktatóanyag: Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Erőforrások kezelése a Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
