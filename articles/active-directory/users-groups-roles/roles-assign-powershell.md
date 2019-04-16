---
title: Rendelje hozzá, és távolítsa el a rendszergazdai szerepkörök hozzárendelése az Azure PowerShell használatával – az Azure Active Directory |} A Microsoft Docs
description: Azok számára, akik gyakran a szerepkör-hozzárendelések kezeléséhez kezelheti az Azure PowerShell-lel az Azure AD rendszergazdai szerepkör tagjai.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d982ea49bdc24ccd9894c98435f1e3012d9dd156
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59581048"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>PowerShell-lel az Azure Active Directory-rendszergazdai szerepkörök hozzárendelése

Szerepkörök hozzárendelése felhasználói fiókokhoz az Azure PowerShell használatával automatizálható. Ez a cikk a [Azure Active Directory PowerShell 2-es verzió](https:/docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modul.

## <a name="prepare-powershell"></a>PowerShell előkészítése

Először be kell [töltse le az Azure AD PowerShell modul](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

```powershell
install-module azuread
import-module azuread
```

Győződjön meg arról, hogy a modul készen áll a használatra, használja a következő parancsot:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Most már megkezdheti a parancsmagokat a modulban. A parancsmagok az Azure AD-modul a teljes leírást, tekintse meg az online dokumentáció a [Azure Active Directory PowerShell 2-es verzió](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

## <a name="permissions-required"></a>Szükséges engedélyek

Csatlakozás az Azure AD-bérlőhöz való hozzárendelése és eltávolítása a szerepkörök egy globális rendszergazdai fiók használatával.

## <a name="assign-a-single-role"></a>Egyetlen szerepkör hozzárendelése

Szerepkör hozzárendelése, először szerezze be a megjelenítendő neve és a hozzárendelés, hogy a szerepkör nevét. Ha a fiók megjelenített neve és a szerepkör nevét, a szerepkör hozzárendeléséhez a felhasználóhoz a következő parancsmag használatával.

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

## <a name="assign-a-role-to-a-service-principal"></a>Szerepkör hozzárendelése a szolgáltatásnév

Példa egy egyszerű szolgáltatás hozzárendelése egy szerepkört.

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

## <a name="multiple-role-assignments"></a>Több szerepkör-hozzárendelések

Példák hozzárendelése és eltávolítása a több szerepkört egyszerre.

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

Ebben a példában az adott felhasználó szerepkör-hozzárendelés eltávolítása.

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

* Nyugodtan megoszthatja velünk a [az Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Szerepkörök és a rendszergazda szerepkör-hozzárendelés kapcsolatos további információkért lásd: [rendelhet hozzá rendszergazdai szerepköröket](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek, tekintse meg a [Vendég és tag alapértelmezett felhasználói engedélyek összehasonlítása](../fundamentals/users-default-permissions.md).
