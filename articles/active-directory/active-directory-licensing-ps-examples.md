---
title: "PowerShell-példák az Azure Active Directory biztonságicsoport-alapú licenckezeléshez |} Microsoft Docs"
description: "Az Azure Active Directory biztonságicsoport-alapú licencelési PowerShell-forgatókönyvek"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.openlocfilehash: 6ee08bb48b8612b16a4609570c12363664788f63
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-examples-for-group-based-licensing-in-azure-ad"></a>PowerShell-példák az Azure Active Directory biztonságicsoport-alapú licenckezeléshez

Csoportalapú licenckezelési teljes funkciók is elérhetővé a [Azure-portálon](https://portal.azure.com), és a PowerShell-támogatás jelenleg korlátozott. Van azonban néhány hasznos feladat, végezhető el a meglévő [MSOnline PowerShell-parancsmagok](https://docs.microsoft.com/powershell/msonline/v1/azureactivedirectory). Ez a dokumentum lehetőségekről példákat.

> [!NOTE]
> Parancsmagok futtatása előtt győződjön meg arról, hogy csatlakozik a bérlő először futtassa a `Connect-MsolService` parancsmag.

## <a name="view-product-licenses-assigned-to-a-group"></a>A csoporthoz rendelt termék licencek megtekintése
A [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) parancsmag segítségével kérje le a csoportházirend-objektum, majd ellenőrizze a *licencek* tulajdonság: felsorolja az összes termék licenccel jelenleg a csoport.
```
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
Kimenet:
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> Az adatok (SKU) termékinformációk korlátozódik. Nincs lehetőség le van tiltva, a licenc service-csomagok listáját.

## <a name="get-all-groups-with-licenses"></a>-Licenccel rendelkező összes csoport beolvasása

A következő parancs futtatásával bármely licenccel rendelkező összes csoport található:
```
Get-MsolGroup | Where {$_.Licenses}
```
További részletek megjelenítése kapcsolatos milyen termékek vannak hozzárendelve:
```
Get-MsolGroup | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

Kimenet:
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>Megtekintheti a statisztikákat a licenccel rendelkező csoportok
A licenccel rendelkező csoportok alapszintű statisztikákat is tud jelentéseket. Az alábbi példa azt felsorolja a teljes felhasználói, a csoport már hozzárendelt licenccel rendelkező felhasználók száma, valamint a felhasználók, akiknek licencek hozzárendelése nem sikerült a csoport száma.

```
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |
    Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


Kimenet:
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>Az összes csoport első licenc hibákkal
Néhány felhasználó, akinek licencek nem rendelhető tartalmazó csoportok kereséséhez:
```
Get-MsolGroup -HasLicenseErrorsOnly $true
```
Kimenet:
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
## <a name="get-all-users-with-license-errors-in-a-group"></a>Minden felhasználó licenc hibákkal csoport lekérése

A megadott egy csoportot, amely tartalmazza az egyes kapcsolódó hibák licenceléséhez most listázhatja azokat a hibák által érintett összes felhasználó. Egy jser túl lehet más csoportokhoz, a hibák. Azonban a következő példa azt szűkíteni a csak az adott csoportra vonatkozó hibák ellenőrzésével a **ReferencedObjectId** tulajdonság az egyes **IndirectLicenseError** bejegyzés a felhasználó.

```
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with licenense errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

Kimenet:
```
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```
## <a name="get-all-users-with-license-errors-in-the-entire-tenant"></a>Minden felhasználó licenc hibákkal a teljes bérlő lekérése

Egy vagy több csoportjára licenc hibák rendelkező felhasználók listáját, az alábbi parancsfájlt is használható. Ez a parancsfájl felsorolja soronként egy felhasználónként, Licenchiba, amely lehetővé teszi az összes hiba a forrás egyértelműen azonosításához.

> [!NOTE]
> Ez a parancsfájl a bérlő számára, amely nem feltétlenül optimális nagy bérlők lévő összes felhasználó számára.

```
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

Kimenet:

```
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

Ez a parancsfájl, amely csak a csoportok licenc hibás keresésre egy másik verziója. Akkor lehet, hogy több optimalizálni forgatókönyvek, ahol várhatóan több csoport gond van.

```
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }
```

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>Ha felhasználói licenc hozzárendelése közvetlenül vagy öröklés útján egy csoporttól származik ellenőrzése

Olyan felhasználói objektum esetén ha egy adott termék licence hozzá van rendelve egy csoportból, vagy ha közvetlenül van hozzárendelve.

Az alábbi két minta funkciók segítségével elemezheti az adott felhasználó a címhozzárendelés típusát:
```
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

Ez a parancsfájl végrehajtása olyan feladatokat a minden felhasználó a bérlőt, az SKU-ID bemenetként – ebben a példában azt is licence *Enterprise Mobility + Security*, amely a bérlői azonosítóval jelölt *contoso:EMS*:
```
#the license SKU we are interested in. use Msol-GetAccountSku to see a list of all identifiers in your tenant
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

Kimenet:
```
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>Távolítsa el a csoport-licenccel rendelkező felhasználók közvetlen licenceinek
A parancsfájl célja, hogy távolítsa el a felesleges közvetlen licencek a felhasználók, akik már öröklik a azonos licenc egy csoportból; például részeként egy [Csoportalapú licencelési való váltás](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-migration-azure-portal).
> [!NOTE]
> Fontos először ellenőrzi, hogy a közvetlen licenceket távolítható el, nem engedélyezi több szolgáltatást, mint az örökölt licencek. Ellenkező esetben a közvetlen licenc eltávolítása bármikor letilthatja a felhasználók számára a szolgáltatások és az adatok elérésére. Jelenleg nincs lehetőség a PowerShell segítségével ellenőrizze, melyik szolgáltatás be legyen kapcsolva keresztül örökölt licencek vs közvetlen. A parancsfájl azt határozza meg tudjuk szolgáltatások minimális szintű csoportokból öröklődtek és ellenőrizzük ezzel szemben.

```
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |
    Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

Kimenet:
```
UserId                               OperationResult                                                                                
------                               ---------------                                                                                
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.                                                              
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.                                                
```

## <a name="next-steps"></a>Következő lépések

A licenc-kezelő csoportokon keresztül beállítása szolgáltatással kapcsolatos további tudnivalókért olvassa el a következőket:

* [Mi az a csoport-alapú licencelése az Azure Active Directoryban?](active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése az Azure Active Directory csoport](active-directory-licensing-group-assignment-azure-portal.md)
* [Majd azonosítani és megoldani az Azure Active Directory csoport licenc problémák](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési egyedi licenccel rendelkező felhasználók áttelepítése](active-directory-licensing-group-migration-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési további helyzeteket is](active-directory-licensing-group-advanced.md)
