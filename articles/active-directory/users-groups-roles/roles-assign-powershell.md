---
title: Rendszergazdai szerepkörök hozzárendelésének hozzárendelése és eltávolítása Azure PowerShell-Azure Active Directorytal | Microsoft Docs
description: A szerepkör-hozzárendeléseket gyakran kezelő felhasználók mostantól a Azure PowerShell használatával felügyelhetik az Azure AD-rendszergazdai szerepkör tagjait.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4bddf84720265afe361dff665f10ff8184f6f6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706479"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>Azure Active Directory rendszergazdai szerepkörök kiosztása a PowerShell használatával

Automatizálhatja, hogyan rendeljen hozzá szerepköröket a felhasználói fiókokhoz Azure PowerShell használatával. Ez a cikk a [Azure Active Directory PowerShell 2-es verziójának](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modulját használja.

## <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureAD/)-modult.

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

```powershell
install-module azuread
import-module azuread
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Most már elkezdheti használni a parancsmagokat a modulban. Az Azure AD-modulban található parancsmagok teljes leírását a [Azure Active Directory PowerShell 2-es verziójának](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)online dokumentációjában találja.

## <a name="permissions-required"></a>Szükséges engedélyek

A szerepkörök hozzárendeléséhez vagy eltávolításához globális rendszergazdai fiókkal csatlakozhat az Azure AD-bérlőhöz.

## <a name="assign-a-single-role"></a>Egyetlen szerepkör kiosztása

Szerepkör hozzárendeléséhez először be kell szereznie a megjelenítendő nevét és a hozzárendelni kívánt szerepkör nevét. Ha a fiók megjelenített neve és a szerepkör neve, a következő parancsmagok használatával rendelje hozzá a szerepkört a felhasználóhoz.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>Szerepkör kiosztása egy egyszerű szolgáltatáshoz

Példa egyszerű szolgáltatásnév hozzárendelésére egy szerepkörhöz.

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>Több szerepkör-hozzárendelés

Példák több szerepkör hozzárendelésére és eltávolítására egyszerre.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Ez a példa egy szerepkör-hozzárendelést távolít el a megadott felhasználóhoz.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole

# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser

```

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)foglalkozó fórumát.
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
