---
title: Egyéni szerepkörök hozzárendelése az Azure PowerShell használatával – Azure AD | Microsoft dokumentumok
description: Az Azure PowerShell segítségével kezelheti az Azure AD-rendszergazdaegyéni szerepkörének tagjait.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025315"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Egyéni szerepkörök hozzárendelése erőforráshatókörrel az Azure Active Directoryban a PowerShell használatával

Ez a cikk ismerteti, hogyan hozhat létre egy szerepkör-hozzárendelés szervezeti szintű hatóköraz Azure Active Directoryban (Azure AD). Szerepkör hozzárendelése a szervezet egészére kiterjedő hatókör hozzáférést biztosít az Azure AD-szervezet számára. Ha egyetlen Azure AD-erőforrás hatókörével szeretne szerepkör-hozzárendelést létrehozni, olvassa el [az Egyéni szerepkör létrehozása és hozzárendelése az erőforráshatókörben című témakört.](roles-create-custom.md) Ez a cikk az [Azure Active Directory PowerShell 2-es verziómodulját](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) használja.

Az Azure AD-rendszergazdai szerepkörökről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](directory-assign-admin-roles.md)

## <a name="required-permissions"></a>Szükséges engedélyek

Csatlakozzon az Azure AD-bérlőhöz egy globális rendszergazdai fiók használatával szerepkörök hozzárendeléséhez vagy eltávolításához.

## <a name="prepare-powershell"></a>PowerShell előkészítése

Telepítse az Azure AD PowerShell-modult a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Ezután importálja az Azure AD PowerShell előzetes modulját a következő paranccsal:

``` PowerShell
import-module azureadpreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, egyezzen meg a következő parancs által visszaadott verzióval az itt felsoroltakkal:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Most már elkezdheti használni a modul parancsmagjait. Az Azure AD modul parancsmagjainak teljes leírását az [Azure AD előzetes verziómodul online referenciadokumentációjában találja.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Szerepkör hozzárendelése erőforráshatókörrel rendelkező felhasználóhoz vagy egyszerű szolgáltatáshoz

1. Nyissa meg az Azure AD előzetes PowerShell-modul.
1. Jelentkezzen be a parancs `Connect-AzureAD`végrehajtásával .
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

A szerepkör hozzárendelése egy egyszerű szolgáltatás helyett egy felhasználó, használja a [Get-AzureADMSServicePrincipal parancsmag.](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)

## <a name="operations-on-roledefinition"></a>Műveletek a RoleDefinition-en

A szerepkör-definíciós objektumok tartalmazzák a beépített vagy egyéni szerepkör definícióját, valamint a szerepkör-hozzárendelés által megadott engedélyeket. Ez az erőforrás az egyéni szerepkör-definíciókat és a beépített directoryRole-ket is megjeleníti (amelyek a roleDefinition egyenértékű képernyőn jelennek meg). Ma egy Azure AD-szervezet legfeljebb 30 egyedi egyéni RoleDefinition-ek definiálva.

### <a name="create-operations-on-roledefinition"></a>Műveletek létrehozása a RoleDefinition-en

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

### <a name="read-operations-on-roledefinition"></a>Műveletek olvasása a RoleDefinition-en

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Műveletek frissítése a RoleDefinition-en

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Műveletek törlése a RoleDefinition-en

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Műveletek a Szerepkör-hozzárendelésen

A szerepkör-hozzárendelések olyan információkat tartalmaznak, amelyek egy adott rendszerbiztonsági tagot (felhasználó- vagy alkalmazásszolgáltatás-egyszerű szolgáltatást) egy szerepkör-definícióhoz kapcsolnak. Ha szükséges, hozzáadhat egy azure-beli AD-erőforrás hatókörét a hozzárendelt engedélyekhez.  Az engedélyek hatókörének korlátozása támogatott a beépített és az egyéni szerepkörök számára.

### <a name="create-operations-on-roleassignment"></a>Műveletek létrehozása a RoleAssignment-en

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

### <a name="read-operations-on-roleassignment"></a>Műveletek olvasása a RoleAssignment-n

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Műveletek törlése a RoleAssignment-n

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>További lépések

- Ossza meg velünk az [Azure AD felügyeleti szerepkörök fórumon.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- A szerepkörökről és az Azure AD rendszergazdai szerepkör-hozzárendelésekről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van.
- Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.
