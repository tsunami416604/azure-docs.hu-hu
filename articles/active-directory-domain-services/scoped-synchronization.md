---
title: Hatókörrel kapcsolatos szinkronizálás az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Megtudhatja, hogy miként konfigurálható a hatókörrel történő szinkronizálás az Azure AD-ről egy Azure Active Directory tartományi szolgáltatások által felügyelt tartományra
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613040"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Hatókörrel történő szinkronizálás konfigurálása az Azure AD és az Azure Active Directory tartományi szolgáltatások között

Hitelesítési szolgáltatások biztosításához az Azure Active Directory tartományi szolgáltatások (Azure AD DS) szinkronizálja a felhasználók és csoportok az Azure AD. Hibrid környezetben a helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetből származó felhasználók és csoportok először szinkronizálhatók az Azure AD-vel az Azure AD Connect használatával, majd szinkronizálhatók az Azure AD DS-sel.

Alapértelmezés szerint az Azure AD-címtár összes felhasználója és csoportja szinkronizálódik egy Azure AD DS felügyelt tartománnyal. Ha speciális igényei vannak, ehelyett dönthet úgy, hogy csak egy meghatározott felhasználókészletet szinkronizál.

Ez a cikk bemutatja, hogyan hozhat létre egy Azure AD DS felügyelt tartományt, amely hatókörrel szinkronizált szinkronizálást használ, majd módosítsa vagy tiltsa le a hatókörrel kezelt felhasználók készletét.

## <a name="scoped-synchronization-overview"></a>Hatókörrel kapcsolatos szinkronizálás – áttekintés

Alapértelmezés szerint az Azure AD-címtár összes felhasználója és csoportja szinkronizálódik egy Azure AD DS felügyelt tartománnyal. Ha csak néhány felhasználónak kell hozzáférnie a felügyelt tartományhoz, csak ezeket a felhasználói fiókokat szinkronizálhatja. Ez a hatókörrel szinkronizált szinkronizálás csoportalapú. A csoportalapú hatókörrel történő szinkronizálás konfigurálásakor csak a megadott csoportokhoz tartozó felhasználói fiókok lesznek szinkronizálva az Azure AD DS felügyelt tartományával.

Az alábbi táblázat a hatókörrel szinkronizált szinkronizálás használatát ismerteti:

| Jelenlegi állapot | Kívánt állapot | Szükséges konfiguráció |
| --- | --- | --- |
| Egy meglévő felügyelt tartomány úgy van beállítva, hogy szinkronizálja az összes felhasználói fiókot és csoportot. | Csak az adott csoportokhoz tartozó felhasználói fiókokat szeretné szinkronizálni. | Nem módosíthatja az összes felhasználó szinkronizálását a hatókörrel szinkronizált szinkronizálásra. [Törölje a meglévő felügyelt tartományt,](delete-aadds.md)majd kövesse a cikkben leírt lépéseket az Azure AD DS által felügyelt tartomány újbóli létrehozásához a hatókörrel rendelkező szinkronizálás konfigurálva. |
| Nincs meglévő felügyelt tartomány. | Új felügyelt tartományt szeretne létrehozni, és csak bizonyos csoportokhoz tartozó felhasználói fiókokat szeretne szinkronizálni. | Kövesse a cikkben leírt lépéseket egy Azure AD DS felügyelt tartomány létrehozásához a hatókörrel szinkronizált szinkronizálás konfigurálva. |
| Egy meglévő felügyelt tartomány úgy van beállítva, hogy csak az adott csoportokhoz tartozó fiókokat szinkronizálja. | Módosítani szeretné azoknak a csoportoknak a listáját, amelyek felhasználóit szinkronizálni kell az Azure AD DS felügyelt tartományával. | A hatókörrel kapcsolatos szinkronizálás módosításához kövesse a jelen cikkben ismertetett lépéseket. |

Az Azure Portal on vagy a PowerShell segítségével konfigurálhatja a hatókörrel kapcsolatos szinkronizálási beállításokat:

| Műveletek | | |
|--|--|--|
| Azure AD DS felügyelt tartomány létrehozása és hatókörrel kapcsolatos szinkronizálás konfigurálása | [Azure-portál](#enable-scoped-synchronization-using-the-azure-portal) | [Powershell](#enable-scoped-synchronization-using-powershell) |
| Hatókörrel történő szinkronizálás módosítása | [Azure-portál](#modify-scoped-synchronization-using-the-azure-portal) | [Powershell](#modify-scoped-synchronization-using-powershell) |
| Hatókörrel szinkronizált szinkronizálás letiltása | [Azure-portál](#disable-scoped-synchronization-using-the-azure-portal) | [Powershell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> A szinkronizálás hatókörének módosítása azt eredményezi, hogy az Azure AD DS felügyelt tartománya újraszinkronizálja az összes adatot.
> 
>  * Ha módosítja egy Azure AD DS felügyelt tartomány szinkronizálási hatókörét, teljes újraszinkronizálás történik.
>  * Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek. Új objektumok jönnek létre a felügyelt tartományban.
>  * Az újraszinkronizálás hosszú időt vehet igénybe. A szinkronizálási idő az Azure AD DS felügyelt tartományban és az Azure AD-címtárban lévő objektumok, például a felhasználók, csoportok és csoporttagságok számától függ. A több százezer objektumot felvonultaló nagy könyvtárak esetében az újraszinkronizálás néhány napot is igénybe vehet.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Hatókörrel történő szinkronizálás engedélyezése az Azure Portalon

1. Kövesse az [oktatóanyagot egy Azure AD DS-példány létrehozásához és konfigurálásához.](tutorial-create-instance-advanced.md) A szinkronizálási hatókörön kívüli összes előfeltétel és telepítési lépés befejezése.
1. Válassza **a Scoped** a szinkronizálási lépés, majd válassza ki az Azure AD-csoportok szinkronizálni az Azure AD DS-példány.

Az Azure AD DS felügyelt tartomány a központi telepítés befejezéséhez akár egy órát is igénybe vehet. Az Azure Portalon az Azure AD DS felügyelt tartomány **áttekintése** lap mutatja az aktuális állapotot ebben a telepítési szakaszban.

Ha az Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartomány a kiépítés befejeződött, a következő feladatokat kell végrehajtani:

* Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek megtalálják a tartományhoz való csatlakozáshoz vagy hitelesítéshez szükséges felügyelt tartományt.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD tartományi szolgáltatásokkal,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) hogy a végfelhasználók vállalati hitelesítő adataikkal bejelentkezhessenek a felügyelt tartományba.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Hatókörrel történő szinkronizálás módosítása az Azure Portal használatával

Az Ond DS felügyelt tartományával szinkronizálandó felhasználók listájának módosításához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.** Válassza ki a példát, például *a aaddscontoso.com.*
1. Válassza a bal oldali menü **Szinkronizálás parancsát.**
1. Csoport hozzáadásához válassza a **+ Csoportok kijelölése** a tetején lehetőséget, majd válassza ki a hozzáadni kívánt csoportokat.
1. Ha el szeretne távolítani egy csoportot a szinkronizálási hatókörből, jelölje ki a jelenleg szinkronizált csoportok listájából, és válassza a **Csoportok eltávolítása lehetőséget.**
1. Az összes módosítás végrehajtásakor válassza **a Szinkronizálási hatókör mentése**lehetőséget.

A szinkronizálás hatókörének módosítása azt eredményezi, hogy az Azure AD DS felügyelt tartománya újraszinkronizálja az összes adatot. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt vehet igénybe.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Hatókörrel történő szinkronizálás letiltása az Azure Portalhasználatával

Az Azure AD DS felügyelt tartománycsoport csoportalapú hatókörszinkronizálásának letiltásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.** Válassza ki a példát, például *a aaddscontoso.com.*
1. Válassza a bal oldali menü **Szinkronizálás parancsát.**
1. Állítsa a szinkronizálási hatókört **hatókörről** **mindre,** majd válassza **a Szinkronizálási hatókör mentése lehetőséget.**

A szinkronizálás hatókörének módosítása azt eredményezi, hogy az Azure AD DS felügyelt tartománya újraszinkronizálja az összes adatot. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt vehet igénybe.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-parancsfájl hatókörrel szinkronizált szinkronizáláshoz

A hatókörrel történő szinkronizálás PowerShell használatával történő konfigurálásához `Select-GroupsToSync.ps1`először mentse a következő parancsfájlt egy nevű fájlba. Ez a parancsfájl konfigurálja az Azure AD DS a kiválasztott csoportok szinkronizálása az Azure AD-ből. A megadott csoportok részét használó összes felhasználói fiók szinkronizálva van az Azure AD DS felügyelt tartományával.

Ez a parancsfájl a cikk további lépéseiben használatos.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Hatókörrel történő szinkronizálás engedélyezése a PowerShell használatával

A PowerShell segítségével hajtsa végre ezt a lépéseket. Az [Azure Active Directory tartományi szolgáltatások PowerShell használatával történő engedélyezéséhez](powershell-create-instance.md)olvassa el az utasításokat. A cikk néhány lépése kissé módosul a hatókörrel történő szinkronizálás konfigurálásához.

1. Hajtsa végre a következő feladatokat a cikkből az Azure AD DS PowerShell használatával való engedélyezéséhez. Álljon meg a lépésnél a felügyelt tartomány tényleges létrehozásához. Konfigurálja az Azure AD DS felügyelt tartományt létrehozott hatókör-szinkronizálást.

   * [Telepítse a szükséges PowerShell-modulokat.](powershell-create-instance.md#prerequisites)
   * [Hozza létre a szükséges egyszerű szolgáltatás és az Azure AD-csoport a felügyeleti hozzáféréshez.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [Hozzon létre támogató Azure-erőforrásokat, például virtuális hálózatot és alhálózatokat.](powershell-create-instance.md#create-supporting-azure-resources)

1. Határozza meg azokat a csoportokat és felhasználókat, amelyeket szinkronizálni szeretne az Azure AD-ből. Készítsen listát az Azure AD DS-hez szinkronizálandó csoportok megjelenítendő nevéről.

1. Futtassa a [parancsfájlt az előző szakaszból,](#powershell-script-for-scoped-synchronization) és használja a *-groupsToAdd* paramétert a szinkronizálandó csoportok listájának átadására.

   > [!WARNING]
   > Az *AAD tartományvezérlő rendszergazdák csoportját* fel kell tüntetnie a hatókörrel szinkronizált szinkronizáláscsoportainak listájában. Ha nem tartalmazza ezt a csoportot, az Azure AD DS felügyelt tartomány használhatatlan.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Most hozza létre az Azure AD DS felügyelt tartományt, és engedélyezze a csoportalapú hatókörszinkronizálást. A *"filteredSync" = "Enabled"* szerepeljen a *-Properties* paraméterben.

    Állítsa be az Azure-előfizetés-azonosítóját, majd adja meg a felügyelt tartomány nevét, például *aaddscontoso.com.* Az előfizetés-azonosítóa a [Get-AzSubscription][Get-AzSubscription] parancsmag használatával szerezhető be. Állítsa be az erőforráscsoport nevét, a virtuális hálózat nevét és a régiót az előző lépésekben használt értékekre a támogató Azure-erőforrások létrehozásához:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Néhány percet vesz igénybe az erőforrás létrehozása, és adja vissza a vezérlést a PowerShell-parancssorba. Az Azure AD DS felügyelt tartomány továbbra is kivan építve a háttérben, és akár egy órát is igénybe vehet a központi telepítés befejezéséhez. Az Azure Portalon az Azure AD DS felügyelt tartomány **áttekintése** lap mutatja az aktuális állapotot ebben a telepítési szakaszban.

Ha az Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartomány a kiépítés befejeződött, a következő feladatokat kell végrehajtani:

* Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek megtalálják a tartományhoz való csatlakozáshoz vagy hitelesítéshez szükséges felügyelt tartományt.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha létrehozott egy Azure AD DS felügyelt tartományt egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, hozzon létre egy hálózati biztonsági csoportot az Azure AD DS felügyelt tartomány virtuális hálózatának forgalom korlátozására. Létrejön egy Azure-szabvány terheléselosztó, amely megköveteli, hogy ezeket a szabályokat kell elhelyezni. Ez a hálózati biztonsági csoport biztonságossá teszi az Azure AD DS-t, és a felügyelt tartomány megfelelő működéséhez szükséges.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan hozza létre és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD tartományi szolgáltatásokkal,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) hogy a végfelhasználók vállalati hitelesítő adataikkal bejelentkezhessenek a felügyelt tartományba.

## <a name="modify-scoped-synchronization-using-powershell"></a>Hatókörrel történő szinkronizálás módosítása a PowerShell használatával

Az Ond a dS felügyelt tartományával szinkronizálandó felhasználók listájának módosításához futtassa újra a [PowerShell-parancsfájlt,](#powershell-script-for-scoped-synchronization) és adja meg a csoportok új listáját. A következő példában a szinkronizálandó csoportok már nem tartalmazzák a *GroupName2 csoportot,* és most már tartalmazzák *a GroupName3 csoportot is.*

> [!WARNING]
> Az *AAD tartományvezérlő rendszergazdák csoportját* fel kell tüntetnie a hatókörrel szinkronizált szinkronizáláscsoportainak listájában. Ha nem tartalmazza ezt a csoportot, az Azure AD DS felügyelt tartomány használhatatlan.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

A szinkronizálás hatókörének módosítása azt eredményezi, hogy az Azure AD DS felügyelt tartománya újraszinkronizálja az összes adatot. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt vehet igénybe.

## <a name="disable-scoped-synchronization-using-powershell"></a>Hatókörrel történő szinkronizálás letiltása a PowerShell használatával

Az Azure AD DS által kezelt tartomány csoportalapú hatókörszinkronizálásának letiltásához állítsa be a *"filteredSync" = "Disabled" (Letiltás)* lehetőséget az Azure AD DS-erőforráson, majd frissítse a felügyelt tartományt. Ha elkészült, az összes felhasználó és csoport van beállítva, hogy szinkronizálja az Azure AD.When complete, all users and groups are set to synchronize from Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

A szinkronizálás hatókörének módosítása azt eredményezi, hogy az Azure AD DS felügyelt tartománya újraszinkronizálja az összes adatot. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt vehet igénybe.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a szinkronizálási folyamatról, [olvassa el a Szinkronizálás ismertetése az Azure AD tartományi szolgáltatásokban című témakört.](synchronization.md)

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
