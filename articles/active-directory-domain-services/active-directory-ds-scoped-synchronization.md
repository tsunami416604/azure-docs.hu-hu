---
title: 'Az Azure Active Directory Domain Services: A szinkronizálási hatóköre |} A Microsoft Docs'
description: A felügyelt tartományok az Azure ad-ből hatókörön belüli szinkronizálás konfigurálása
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: cdf5403e78c8136c923c7d2ff95a337db810c7a9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155932"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>A felügyelt tartomány Azure AD-ből a hatókörön belüli szinkronizálás konfigurálása
Ez a cikk bemutatja, hogyan konfigurálása csak adott felhasználói fiókokat, az Azure AD-címtár szinkronizálható az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.


## <a name="group-based-scoped-synchronization"></a>Hatókörrel rendelkező szinkronizálási csoport
Alapértelmezés szerint minden felhasználó és a csoportokat az Azure AD-címtár szinkronizálása a felügyelt tartományra. Ha csak néhány felhasználó számára a felügyelt tartomány használatához, előfordulhat, hogy csak azok a felhasználói fiókok szinkronizálása. Hatókörrel rendelkező szinkronizálási Csoportalapú lehetővé teszi, hogy ehhez. Ha konfigurálva, akkor csak felhasználói fiókok megadott csoportba tartozó szinkronizálva vannak a felügyelt tartományhoz.

Az alábbi táblázat segítségével eldöntheti, hogyan használhatja a hatókörön belüli szinkronizálás:

| **Jelenlegi állapota** | **Kívánt állapot** | **Szükséges konfiguráció** |
| --- | --- | --- |
| Az összes felhasználói fiókok és csoportok szinkronizálása a meglévő felügyelt tartományt van konfigurálva. | Csak a felügyelt tartomány adott csoportokhoz tartozó felhasználói fiókok szinkronizálni szeretné. | [Törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md). Ezután kövesse az utasításokat ebben a cikkben hozza létre újból a hatókörön belüli szinkronizálás konfigurálva. |
| Nem rendelkezik egy meglévő felügyelt tartományt. | Azt szeretné, hozzon létre egy új felügyelt tartományt, és csak adott csoportokhoz tartozó felhasználói fiókok szinkronizálásához. | Kövesse az utasításokat ebben a cikkben létrehoz egy új felügyelt tartományt hatókörön belüli szinkronizálás be van konfigurálva. |
| A meglévő felügyelt tartományt csak adott csoportokhoz tartozó fiókok szinkronizálása van konfigurálva. | Szeretné módosítani azokat a csoportokat, amelynek a felhasználók szinkronizálásra kerüljenek a kezelés tartományhoz. | Kövesse az utasításokat, ez a cikk a hatókörrel rendelkező szinkronizálási módosítása. |

> [!WARNING]
> **A szinkronizálási hatókör módosítása hatására a haladhat végig az újraszinkronizálás a felügyelt tartományra.**
>
 * Amikor a szinkronizálási hatókör a felügyelt tartományhoz tartozó, egy teljes újraszinkronizálási következik be.
 * A felügyelt tartomány már nem szükséges objektumok törlődnek. Új objektumokat a felügyelt tartományban jönnek létre.
 * Az újraszinkronizálás függően objektumot (felhasználókat, csoportokat és csoporttagságokat) a felügyelt tartomány és az Azure AD-címtár hosszú időt vehet igénybe. Több ezer objektumot tartalmaz több száz nagyméretű címtárak esetén az újraszinkronizálás néhány napig is eltarthat.
>
>


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization"></a>Hozzon létre egy új felügyelt tartományt és a Csoportalapú hatókörrel rendelkező szinkronizálásának engedélyezése
PowerShell használatával hajtsa végre a lépéseket egy készlete. Tekintse meg az utasításokat követve [engedélyezése az Azure Active Directory Domain Services PowerShell-lel](active-directory-ds-enable-using-powershell.md). Néhány cikkben ismertetett lépések kissé módosultak, hogy a hatókörön belüli-szinkronizálás konfigurálásával.

A következő lépéseket a felügyelt tartományra a hatókörön belüli szinkronizálás Csoportalapú konfigurálása:

1. Hajtsa végre a következő feladatokat:
  * [1. feladat: A szükséges PowerShell-modulok telepítéséhez](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
  * [2. feladat: A szükséges szolgáltatásnév létrehozása az Azure AD-címtárban](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
  * [3. feladat: Hozzon létre és konfigurálja a "AAD DC rendszergazdák" csoportot](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
  * [4. feladat: Az Azure AD tartományi szolgáltatások erőforrás-szolgáltató regisztrálása](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
  * [5. feladat: Erőforráscsoport létrehozása](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
  * [6. feladat: Hozzon létre, és konfigurálja a virtuális hálózat](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Válassza ki a csoportokat szeretne szinkronizálni, és szeretné szinkronizálni a felügyelt tartományra a csoportok megjelenített nevét.

3. Mentse a [szkript a következő szakaszban](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) nevű fájlba ```Select-GroupsToSync.ps1```. Hajtsa végre a parancsfájl alábbi módon:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", “GroupName1”, “GroupName2”)
  ```

  > [!WARNING]
  > **Ne felejtse el tartalmazzák az "AAD DC rendszergazdák" csoportot.**
  >
  > Az "AAD DC rendszergazdák" csoportba szerepelnie kell a konfigurált hatókörbe tartozó szinkronizálási csoportok listája. Ha nem adja meg az ehhez a csoporthoz, a felügyelt tartomány használhatatlan lesz.
  >

4. Ezzel a felügyelt tartomány létrehozása és engedélyezése a felügyelt tartományhoz tartozó hatókörrel rendelkező szinkronizálási csoport-alapú. Tulajdonság ```"filteredSync" = "Enabled"``` a a ```Properties``` paraméter. Például lásd a következő parancsfájl-töredék, másolt [feladat 7: kiépítése az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

  ```powershell
  $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
  $ManagedDomainName = "contoso100.com"
  $ResourceGroupName = "ContosoAaddsRg"
  $VnetName = "DomainServicesVNet_WUS"
  $AzureLocation = "westus"

  # Enable Azure AD Domain Services for the directory.
  New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
  ```

  > [!TIP]
  > Ne felejtse el felvenni ```"filteredSync" = "Enabled"``` a a ```-Properties``` paramétert, így a hatókörön belüli szinkronizálás engedélyezve van a felügyelt tartományhoz.


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


## <a name="modify-group-based-scoped-synchronization"></a>Hatókörrel rendelkező szinkronizálási csoport módosítása
Azokat a csoportokat, amelynek a felhasználók szinkronizálásra kerüljenek a felügyelt tartomány módosításához futtassa újra a [PowerShell-parancsprogram](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) , és adja meg az új azokat a csoportokat. Fontos, hogy mindig a listában adja meg az "AAD DC rendszergazdák" csoportba.

> [!WARNING]
> **Ne felejtse el tartalmazzák az "AAD DC rendszergazdák" csoportot.**
>
> Az "AAD DC rendszergazdák" csoportba szerepelnie kell a konfigurált hatókörbe tartozó szinkronizálási csoportok listája. Ha nem adja meg az ehhez a csoporthoz, a felügyelt tartomány használhatatlan lesz.
>


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

## <a name="next-steps"></a>További lépések
* [Szinkronizálás az Azure AD tartományi szolgáltatások ismertetése](active-directory-ds-synchronization.md)
* [Engedélyezze az Azure Active Directory Domain Services PowerShell-lel](active-directory-ds-enable-using-powershell.md)
