---
title: Egyéni szerepkörök társítása Azure PowerShell használatával – Azure AD | Microsoft Docs
description: Az Azure AD-rendszergazda egyéni szerepkör tagjainak kezelése Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 243bc48f7e84951c78ecab3e7f5ee47a85a538bf
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798822"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Egyéni szerepkörök társítása erőforrás-hatókörrel a PowerShell használatával Azure Active Directory

Ez a cikk azt ismerteti, hogyan lehet szerepkör-hozzárendelést létrehozni a szervezeti szintű hatókörben Azure Active Directory (Azure AD). Az Azure AD-szervezeten belül az egész szervezetre kiterjedő hatókörrel rendelkező szerepkör kiosztása biztosít hozzáférést. Ha egy szerepkör-hozzárendelést egyetlen Azure AD-erőforrás hatókörével szeretne létrehozni, tekintse [meg az egyéni szerepkör létrehozása és hozzárendelése az erőforrás-hatókörben](roles-create-custom.md)című témakört. Ez a cikk a [Azure Active Directory PowerShell 2-es verziójának](/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modulját használja.

További információ az Azure AD rendszergazdai szerepköreiről: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Szükséges engedélyek

A szerepkörök hozzárendeléséhez vagy eltávolításához globális rendszergazdai fiókkal csatlakozhat az Azure AD-szervezethez.

## <a name="prepare-powershell"></a>A PowerShell előkészítése

Telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Ezután importálja az Azure AD PowerShell előzetes verzióját a következő parancs használatával:

``` PowerShell
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, a következő parancs által visszaadott verziónak kell megfelelnie az itt felsoroltak közül:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Most már elkezdheti használni a parancsmagokat a modulban. Az Azure AD-modulban található parancsmagok teljes leírását az [Azure ad Preview-modul](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)online dokumentációjában találja.

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Szerepkör társítása egy felhasználóhoz vagy egy egyszerű szolgáltatáshoz erőforrás-hatókörrel

1. Nyissa meg az Azure AD Preview PowerShell-modult.
1. Jelentkezzen be a parancs végrehajtásával `Connect-AzureAD` .
1. Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Ha a szerepkört felhasználó helyett egy egyszerű szolgáltatáshoz szeretné rendelni, használja a [Get-AzureADMSServicePrincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Definíciós-műveletek

A szerepkör-definíciós objektumok tartalmazzák a beépített vagy az egyéni szerepkör definícióját, valamint az adott szerepkör-hozzárendelés által biztosított engedélyeket. Ez az erőforrás az egyéni szerepkör-definíciókat és a beépített directoryRoles jeleníti meg (amelyek definíciós egyenértékű formában jelennek meg). Napjainkban az Azure AD-szervezeteknek legfeljebb 30 egyedi egyéni RoleDefinitions lehet definiálni.

### <a name="create-operations-on-roledefinition"></a>Műveletek létrehozása a definíciós-on

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Olvasási műveletek a definíciós-on

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Frissítési műveletek a definíciós

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Műveletek törlése a definíciós

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>RoleAssignment-műveletek

A szerepkör-hozzárendelések olyan információkat tartalmaznak, amelyek egy adott rendszerbiztonsági tag (felhasználó vagy alkalmazás-szolgáltatásnév) egy szerepkör-definícióhoz való csatolásával kapcsolatosak. Szükség esetén egyetlen Azure AD-erőforrás hatókörét is hozzáadhatja a hozzárendelt engedélyekhez.  Az engedélyek hatókörének korlátozása beépített és egyéni szerepkörök esetén támogatott.

### <a name="create-operations-on-roleassignment"></a>Műveletek létrehozása a RoleAssignment-on

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Olvasási műveletek a RoleAssignment-on

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Műveletek törlése a RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Következő lépések

- Ossza meg velünk az [Azure ad rendszergazdai szerepköreit tartalmazó fórumot](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- A szerepkörökről és az Azure AD rendszergazdai szerepkör-hozzárendeléseiről a [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md)című témakörben olvashat bővebben.
- Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).