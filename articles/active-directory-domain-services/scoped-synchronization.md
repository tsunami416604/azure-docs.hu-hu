---
title: 'Azure Active Directory Domain Services: Hatókörön belüli szinkronizálás | Microsoft Docs'
description: Hatókörön belüli szinkronizálás konfigurálása az Azure AD-ből a felügyelt tartományokra
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617112"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Hatókörön belüli szinkronizálás konfigurálása az Azure AD-ből a felügyelt tartományba
Ebből a cikkből megtudhatja, hogyan konfigurálhat csak meghatározott felhasználói fiókokat az Azure AD-címtárból a Azure AD Domain Services felügyelt tartományba.


## <a name="group-based-scoped-synchronization"></a>Csoport alapú hatókörű szinkronizálás
Alapértelmezés szerint az Azure AD-címtárban lévő összes felhasználó és csoport szinkronizálva van a felügyelt tartományával. Ha csak néhány felhasználó használja a felügyelt tartományt, akkor csak ezeket a felhasználói fiókokat szinkronizálhatja. A csoport alapú hatókörű szinkronizálás lehetővé teszi a művelet elvégzését. Ha be van állítva, a rendszer csak a megadott csoportokhoz tartozó felhasználói fiókokat szinkronizálja a felügyelt tartományba.

A következő táblázat segít meghatározni a hatókörön belüli szinkronizálás használatát:

| **Aktuális állapot** | **Kívánt állapot** | **Szükséges konfiguráció** |
| --- | --- | --- |
| A meglévő felügyelt tartomány úgy van konfigurálva, hogy szinkronizálja az összes felhasználói fiókot és csoportot. | Csak a megadott csoportokba tartozó felhasználói fiókokat szeretné szinkronizálni a felügyelt tartományba. | [Törölje a meglévő felügyelt tartományt](delete-aadds.md). Ezután kövesse az ebben a cikkben található utasításokat, hogy újra létrehozza azt hatókörön belüli szinkronizálással. |
| Nem rendelkezik meglévő felügyelt tartománnyal. | Új felügyelt tartományt kíván létrehozni, és csak adott csoportokhoz tartozó felhasználói fiókokat szinkronizálni. | A cikk utasításait követve hozzon létre egy új felügyelt tartományt, amelynek hatókörön belüli szinkronizálása konfigurálva van. |
| A meglévő felügyelt tartomány úgy van konfigurálva, hogy csak az adott csoportokhoz tartozó fiókokat szinkronizálja. | Szeretné módosítani azoknak a csoportoknak a listáját, amelyek felhasználóit szinkronizálni kell a tartomány kezelése lehetőséggel. | A hatókörön belüli szinkronizálás módosításához kövesse a cikk utasításait. |

> [!WARNING]
> **A szinkronizálás hatókörének módosítása hatására a felügyelt tartomány újraszinkronizálást hajt végre.**
> 
>  * Egy felügyelt tartomány szinkronizálási hatókörének módosításakor teljes Újraszinkronizálás történik.
>  * A felügyelt tartományban már nem szükséges objektumok törlődnek. Az új objektumok a felügyelt tartományban jönnek létre.
>  * Az Újraszinkronizálás hosszú időt is igénybe vehet, attól függően, hogy a felügyelt tartományban és az Azure AD-címtárban lévő objektumok (felhasználók, csoportok és csoporttagságok) száma milyen. Több százezer objektumot tartalmazó nagyméretű könyvtárak esetében az újraszinkronizálás néhány napig is eltarthat.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Új felügyelt tartomány létrehozása és a csoport alapú hatókörű szinkronizálás engedélyezése Azure Portal használatával

1. Felügyelt tartomány létrehozásához kövesse az [első lépések útmutatót](tutorial-create-instance.md) .
2. Válassza ki a hatókört a szinkronizálási stílus kiválasztása során a Azure ad Domain Services létrehozási varázslóban.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Új felügyelt tartomány létrehozása és csoport alapú hatókörű szinkronizálás engedélyezése a PowerShell használatával
A lépések végrehajtásához használja a PowerShellt. Tekintse át a [Azure Active Directory Domain Services a PowerShell használatával](powershell-create-instance.md)történő engedélyezésének utasításait. A cikkben néhány lépést némileg módosítanak a hatókörön belüli szinkronizálás konfigurálásához.

A következő lépések végrehajtásával konfigurálhatja a csoport alapú hatókörű szinkronizálást a felügyelt tartományra:

1. Hajtsa végre a következő feladatokat:
   * [1. feladat: Telepítse a szükséges PowerShell-](powershell-create-instance.md#task-1-install-the-required-powershell-modules)modulokat.
   * [2. feladat: Hozza létre a szükséges szolgáltatásnevet az Azure AD-címtárban](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
   * [3. feladat: Hozza létre és konfigurálja az "HRE DC Administrators" csoportot] PowerShell-Create-instance. MD # feladat-3-Create-and-configure-The-HRE-DC-Administrators-Group).
   * [4. feladat: Regisztrálja a Azure AD Domain Services erőforrás](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)-szolgáltatót.
   * [5. feladat: Hozzon létre egy](powershell-create-instance.md#task-5-create-a-resource-group)erőforráscsoportot.
   * [6. feladat: Hozza létre és konfigurálja a virtuális](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)hálózatot.

2. Válassza ki a szinkronizálni kívánt csoportokat, és adja meg azoknak a csoportoknak a megjelenítendő nevét, amelyeket szinkronizálni szeretne a felügyelt tartományával.

3. Mentse a [parancsfájlt a következő szakaszban](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) egy nevű ```Select-GroupsToSync.ps1```fájlba. Hajtsa végre az alábbihoz hasonló parancsfájlt:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Ne felejtse el belefoglalni a "HRE DC Administrators" csoportot.**
   >
   > A hatókörön belüli szinkronizáláshoz konfigurált csoportok listájában szerepelnie kell a "HRE DC rendszergazdák" csoportnak. Ha nem adja meg ezt a csoportot, a felügyelt tartomány használhatatlan lesz.
   >

4. Most hozza létre a felügyelt tartományt, és engedélyezze a csoport alapú hatókörű szinkronizálást a felügyelt tartományhoz. Adja meg a ```"filteredSync" = "Enabled"``` tulajdonságot ```Properties``` a paraméterben. Például tekintse meg a következő, a 7. [feladatból másolt parancsfájl-kódrészletet: A Azure AD Domain Services felügyelt tartomány](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)kiépítése.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Ne felejtse el felvenni ```"filteredSync" = "Enabled"``` a ```-Properties``` paraméterbe, ezért a hatókörön belüli szinkronizálás engedélyezve van a felügyelt tartományon.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>A felügyelt tartományhoz (Select-GroupsToSync. ps1) szinkronizálandó csoportok kiválasztására szolgáló parancsfájl
Mentse a következő parancsfájlt egy fájlba (```Select-GroupsToSync.ps1```). Ez a szkript úgy konfigurálja a Azure AD Domain Services, hogy szinkronizálja a kijelölt csoportokat a felügyelt tartományon. A rendszer a megadott csoportokba tartozó összes felhasználói fiókot szinkronizálja a felügyelt tartományba.

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
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Csoport alapú hatókörű szinkronizálás módosítása
Azon csoportok listájának módosításához, amelyeknek a felhasználóit szinkronizálni kell a felügyelt tartományba, futtassa újra a [PowerShell](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) -parancsfájlt, és adja meg a csoportok új listáját. Ne feledje, hogy mindig megadja a "HRE DC rendszergazdák" csoportot a listában.

> [!WARNING]
> **Ne felejtse el belefoglalni a "HRE DC Administrators" csoportot.**
>
> A hatókörön belüli szinkronizáláshoz konfigurált csoportok listájában szerepelnie kell a "HRE DC rendszergazdák" csoportnak. Ha nem adja meg ezt a csoportot, a felügyelt tartomány használhatatlan lesz.
>


## <a name="disable-group-based-scoped-synchronization"></a>Csoport alapú hatókörű szinkronizálás letiltása
A következő PowerShell-parancsfájllal tilthatja le a kezelt tartomány csoport alapú hatókörű szinkronizálását:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>További lépések
* [A Azure AD Domain Services szinkronizálásának megismerése](synchronization.md)
* [Azure Active Directory Domain Services engedélyezése a PowerShell használatával](powershell-create-instance.md)
