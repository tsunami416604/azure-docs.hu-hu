---
title: 'Az Azure Active Directory Domain Services: A szinkronizálási hatóköre |} A Microsoft Docs'
description: A felügyelt tartományok az Azure ad-ből hatókörön belüli szinkronizálás konfigurálása
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: maheshu
ms.openlocfilehash: ada6e7536ec91e5b1f35f1740177ae264fe68cf7
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063489"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>A felügyelt tartomány Azure AD-ből a hatókörön belüli szinkronizálás konfigurálása
Ez a cikk bemutatja, hogyan konfigurálása csak adott felhasználói fiókokat, az Azure AD-címtár szinkronizálható az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.


## <a name="group-based-scoped-synchronization"></a>Hatókörrel rendelkező szinkronizálási csoport
Alapértelmezés szerint minden felhasználó és a csoportokat az Azure AD-címtár szinkronizálása a felügyelt tartományra. Ha a felügyelt tartományra csak néhány felhasználót használja, előfordulhat, hogy inkább csak a felügyelt tartomány felhasználói fiók szinkronizálása. Hatókörrel rendelkező szinkronizálási Csoportalapú lehetővé teszi, hogy ehhez. Ha konfigurálva, akkor csak felhasználói fiókok megadott csoportba tartozó szinkronizálva vannak a felügyelt tartományhoz.


## <a name="get-started-install-the-required-powershell-modules"></a>Első lépések: a szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [Azure AD PowerShell-modul telepítéséhez és az Azure AD connect](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).



## <a name="enable-group-based-scoped-synchronization"></a>Csoport alapú hatókörrel rendelkező szinkronizálásának engedélyezése
A következő lépéseket a felügyelt tartományra a hatókörön belüli szinkronizálás Csoportalapú konfigurálása:

1. Válassza ki a csoportokat szeretne szinkronizálni, és szeretné szinkronizálni a felügyelt tartományra a csoportok megjelenített nevét.

2. Mentse a parancsfájlt a következő szakaszban nevű fájlba ```Select-GroupsToSync.ps1```. Hajtsa végre a parancsfájl alábbi módon:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @(“GroupName1”, “GroupName2”)
  ```

3. Most a Csoportalapú engedélyezése a felügyelt tartományhoz tartozó szinkronizálási hatókörét.

  ```powershell
  // Login to your Azure AD tenant
  Login-AzureRmAccount

  // Retrieve the Azure AD Domain Services resource.
  $DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

  // Enable group-based scoped synchronization.
  $enableScopedSync = @{"filteredSync" = "Enabled"}

  Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
  ```

## <a name="disable-group-based-scoped-synchronization"></a>Csoportalapú hatókörön belüli szinkronizálás letiltása
Csoportalapú letiltásához a következő PowerShell-parancsfájlt használja a felügyelt tartományhoz tartozó szinkronizálási hatóköre:

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Válassza ki a csoportokat a felügyelt tartományhoz (Select-GroupsToSync.ps1) szinkronizálása-szkript
A következő szkript mentése fájlba (```Select-GroupsToSync.ps1```). Ez a szkript konfigurálja a szinkronizálása a felügyelt tartományra a kiválasztott csoportok Azure AD tartományi szolgáltatásokat. A megadott csoportokhoz tartozó összes felhasználói fiók szinkronizálja a felügyelt tartományhoz.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occured assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="next-steps"></a>További lépések
* [Szinkronizálás az Azure AD tartományi szolgáltatások ismertetése](active-directory-ds-synchronization.md)
