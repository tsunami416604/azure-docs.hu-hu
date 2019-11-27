---
title: Hatókörön belüli szinkronizálás a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat hatókörön belüli szinkronizálást az Azure AD-ből egy Azure Active Directory Domain Services felügyelt tartományra
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
ms.openlocfilehash: 525ea421eb0fa0131fa91078b0619b8463f6fbb0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546248"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Hatókörön belüli szinkronizálás konfigurálása az Azure AD-ből Azure Active Directory Domain Services

A hitelesítési szolgáltatások biztosításához Azure Active Directory Domain Services (Azure AD DS) szinkronizálja a felhasználókat és a csoportokat az Azure AD-ből. Hibrid környezetben a helyszíni Active Directory tartományi szolgáltatások (AD DS) környezet felhasználóit és csoportjait először szinkronizálhatja az Azure AD-vel az Azure AD Connect használatával, majd szinkronizálhatja az Azure AD DSával.

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport szinkronizálva van egy Azure AD DS felügyelt tartományba. Ha konkrét igényei vannak, ehelyett csak a megadott felhasználók szinkronizálását lehet választani.

Ez a cikk bemutatja, hogyan hozhat létre egy hatókörön belüli szinkronizálást használó Azure AD DS felügyelt tartományt, majd módosíthatja vagy letilthatja a hatókörrel rendelkező felhasználók készletét.

## <a name="scoped-synchronization-overview"></a>Hatókörön belüli szinkronizálás – áttekintés

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport szinkronizálva van egy Azure AD DS felügyelt tartományba. Ha csak néhány felhasználónak kell hozzáférnie a felügyelt tartományhoz, csak ezeket a felhasználói fiókokat lehet szinkronizálni. Ez a hatókörön belüli szinkronizálás csoport alapú. A csoport alapú hatókörű szinkronizálás konfigurálásakor a rendszer csak a megadott csoportokhoz tartozó felhasználói fiókokat szinkronizálja az Azure AD DS felügyelt tartományba.

A következő táblázat a hatókörrel rendelkező szinkronizálás használatát ismerteti:

| Aktuális állapot | Kívánt állapot | Szükséges konfiguráció |
| --- | --- | --- |
| Egy meglévő felügyelt tartomány úgy van konfigurálva, hogy szinkronizálja az összes felhasználói fiókot és csoportot. | Csak adott csoportokhoz tartozó felhasználói fiókokat szeretne szinkronizálni. | Nem módosítható az összes felhasználó szinkronizálása a hatókörön belüli szinkronizálás használatával. [Törölje a meglévő felügyelt tartományt](delete-aadds.md), majd kövesse az ebben a cikkben ismertetett lépéseket egy Azure AD DS felügyelt tartomány újbóli létrehozásához a hatókörön belüli szinkronizálás beállításával. |
| Nincs meglévő felügyelt tartomány. | Új felügyelt tartományt kíván létrehozni, és csak adott csoportokhoz tartozó felhasználói fiókokat szinkronizálni. | A cikk lépéseit követve hozzon létre egy Azure AD DS felügyelt tartományt a hatókörön belüli szinkronizálással konfigurálva. |
| Egy meglévő felügyelt tartomány úgy van konfigurálva, hogy csak az adott csoportokhoz tartozó fiókokat szinkronizálja. | Szeretné módosítani azoknak a csoportoknak a listáját, amelyek felhasználóit szinkronizálni szeretné az Azure AD DS felügyelt tartományával. | A hatókörön belüli szinkronizálás módosításához kövesse a cikkben ismertetett lépéseket. |

A hatókörrel rendelkező szinkronizálási beállítások konfigurálásához használja a Azure Portal vagy a PowerShellt:

| Műveletek | | |
|--|--|--|
| Azure AD DS felügyelt tartomány létrehozása és hatókörön belüli szinkronizálás konfigurálása | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Hatókörön belüli szinkronizálás módosítása | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Hatókörön belüli szinkronizálás letiltása | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> A szinkronizálás hatókörének módosítása miatt az Azure AD DS felügyelt tartomány az összes adatot újraszinkronizálja.
> 
>  * Ha módosítja egy Azure AD DS felügyelt tartomány szinkronizálási hatókörét, a rendszer teljes újraszinkronizálást hajt végre.
>  * Az Azure AD DS felügyelt tartományban már nem szükséges objektumok törlődnek. Az új objektumok a felügyelt tartományban jönnek létre.
>  * Az Újraszinkronizálás hosszú időt is igénybe vehet. A szinkronizálási idő az Azure AD DS felügyelt tartományban és az Azure AD-címtárban lévő objektumok, például felhasználók, csoportok és csoporttagságok számától függ. Több százezer objektumot tartalmazó nagyméretű könyvtárak esetében az újraszinkronizálás néhány napig is eltarthat.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Hatókörön belüli szinkronizálás engedélyezése a Azure Portal használatával

1. Kövesse az [oktatóanyagot az Azure AD DS-példányok létrehozásához és konfigurálásához](tutorial-create-instance-advanced.md). Fejezze be az összes előfeltételt és központi telepítési lépést, kivéve a szinkronizálás hatókörét.
1. Válassza a szinkronizálási lépés **hatóköre** lehetőséget, majd válassza ki az Azure AD DS-példánnyal szinkronizálni kívánt Azure ad-csoportokat.

Az Azure AD DS felügyelt tartomány akár egy órát is igénybe vehet a telepítés befejezéséhez. Az Azure Portal az Azure AD DS felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartománya befejeződött, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* [Engedélyezze a jelszó-szinkronizálást Azure ad Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) , hogy a végfelhasználók a vállalati hitelesítő adataik használatával bejelentkezhetnek a felügyelt tartományba.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Hatókörön belüli szinkronizálás módosítása a Azure Portal használatával

Azon csoportok listájának módosításához, amelyeknek a felhasználóit szinkronizálni szeretné az Azure AD DS felügyelt tartományával, hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services**lehetőséget. Válassza ki a példányt, például *contoso.com*.
1. Válassza a **szinkronizálás** lehetőséget a bal oldali menüben.
1. Csoport hozzáadásához válassza a felül a **+ csoportok kiválasztása** lehetőséget, majd válassza ki a hozzáadni kívánt csoportokat.
1. Ha el szeretne távolítani egy csoportot a szinkronizálási hatókörből, válassza ki a jelenleg szinkronizált csoportok listájából, majd válassza a **csoportok eltávolítása**lehetőséget.
1. Ha minden módosítás történik, válassza a **szinkronizálási hatókör mentése**lehetőséget.

A szinkronizálás hatókörének módosítása miatt az Azure AD DS felügyelt tartomány az összes adatot újraszinkronizálja. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Hatókörön belüli szinkronizálás letiltása a Azure Portal használatával

Ha le szeretné tiltani a csoport alapú hatókörű szinkronizálást egy Azure AD DS felügyelt tartományhoz, hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services**lehetőséget. Válassza ki a példányt, például *contoso.com*.
1. Válassza a **szinkronizálás** lehetőséget a bal oldali menüben.
1. Állítsa be a szinkronizálási hatókört a **hatókörből** az **összesre**, majd válassza a **szinkronizálási hatókör mentése**lehetőséget.

A szinkronizálás hatókörének módosítása miatt az Azure AD DS felügyelt tartomány az összes adatot újraszinkronizálja. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-parancsfájl hatókörön belüli szinkronizáláshoz

A hatókörrel rendelkező szinkronizálás PowerShell használatával történő konfigurálásához először mentse a következő parancsfájlt egy `Select-GroupsToSync.ps1`nevű fájlba. Ez a parancsfájl az Azure AD DSt konfigurálja a kiválasztott csoportok Azure AD-vel való szinkronizálásához. A megadott csoportok részét képező összes felhasználói fiók szinkronizálva van az Azure AD DS felügyelt tartományával.

Ez a szkript a cikk további lépéseiben található.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Hatókörön belüli szinkronizálás engedélyezése a PowerShell használatával

A lépések végrehajtásához használja a PowerShellt. Tekintse át a [Azure Active Directory Domain Services a PowerShell használatával történő engedélyezésének](powershell-create-instance.md)utasításait. A cikkben néhány lépést némileg módosítanak a hatókörön belüli szinkronizálás konfigurálásához.

1. Az Azure AD DS PowerShell használatával történő engedélyezéséhez hajtsa végre a következő feladatokat a cikkből. Állítsa le a lépést a felügyelt tartomány tényleges létrehozásához. A hatókörön belüli szinkronizálást úgy konfigurálhatja, hogy létrehozza az Azure AD DS felügyelt tartományt.

   * [Telepítse a szükséges PowerShell-modulokat](powershell-create-instance.md#prerequisites).
   * [Hozza létre a szükséges egyszerű szolgáltatásnevet és az Azure ad-csoportot a rendszergazdai hozzáféréshez](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Hozzon létre támogató Azure-erőforrásokat, például egy virtuális hálózatot és alhálózatokat](powershell-create-instance.md#create-supporting-azure-resources).

1. Határozza meg azokat a csoportokat és felhasználókat, akiket az Azure AD-ből szeretne szinkronizálni. Sorolja fel az Azure-AD DS szinkronizálandó csoportok megjelenítendő neveit.

1. Futtassa a [szkriptet az előző szakaszban](#powershell-script-for-scoped-synchronization) , és használja a *-groupsToAdd* paramétert a szinkronizálandó csoportok listájának átadásához.

   > [!WARNING]
   > A hatókörön belüli szinkronizáláshoz a csoportok listájában szerepelnie kell a *HRE DC-rendszergazdák* csoportnak. Ha nem adja meg ezt a csoportot, az Azure AD DS felügyelt tartomány használhatatlan.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Most hozza létre az Azure AD DS felügyelt tartományt, és engedélyezze a csoport-alapú hatókörű szinkronizálást. Adja meg a *"filteredSync" = "enabled* " *tulajdonságot a-Properties* paraméterben.

    Állítsa be az Azure-előfizetés AZONOSÍTÓját, majd adja meg a felügyelt tartomány nevét (például *contoso.com*). Az előfizetés-azonosítót a [Get-AzSubscription][Get-AzSubscription] parancsmaggal kérheti le. Állítsa be az erőforráscsoport nevét, a virtuális hálózat nevét és a régiót az előző lépésekben használt értékekre a támogató Azure-erőforrások létrehozásához:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
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

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. Az Azure AD DS felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. Az Azure Portal az Azure AD DS felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartománya befejeződött, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha olyan régióban hozott létre Azure AD DS felügyelt tartományt, amely támogatja a Availability Zones, hozzon létre egy hálózati biztonsági csoportot az Azure AD DS felügyelt tartományhoz tartozó virtuális hálózat forgalmának korlátozására. Létrejön egy Azure standard Load Balancer, amely megköveteli a szabályok elhelyezését. Ez a hálózati biztonsági csoport biztosítja az Azure AD DSét, és szükséges a felügyelt tartomány megfelelő működéséhez.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan létrehozza és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást Azure ad Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) , hogy a végfelhasználók a vállalati hitelesítő adataik használatával bejelentkezhetnek a felügyelt tartományba.

## <a name="modify-scoped-synchronization-using-powershell"></a>Hatókörön belüli szinkronizálás módosítása a PowerShell használatával

Azon csoportok listájának módosításához, amelyek felhasználóit szinkronizálni kell az Azure AD DS felügyelt tartományba, futtassa újra a [PowerShell-parancsfájlt](#powershell-script-for-scoped-synchronization) , és adja meg a csoportok új listáját. A következő példában a szinkronizálandó csoportok már nem tartalmazzák a *GroupName2*, és most már tartalmazza a *GroupName3*.

> [!WARNING]
> A hatókörön belüli szinkronizáláshoz a csoportok listájában szerepelnie kell a *HRE DC-rendszergazdák* csoportnak. Ha nem adja meg ezt a csoportot, az Azure AD DS felügyelt tartomány használhatatlan.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

A szinkronizálás hatókörének módosítása miatt az Azure AD DS felügyelt tartomány az összes adatot újraszinkronizálja. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="disable-scoped-synchronization-using-powershell"></a>Hatókörön belüli szinkronizálás letiltása a PowerShell használatával

Ha le szeretné tiltani a csoport alapú hatókörű szinkronizálást egy Azure AD DS felügyelt tartományhoz, állítsa a *"filteredSync" = "Letiltva"* lehetőséget az Azure AD DS-erőforrásra, majd frissítse a felügyelt tartományt. Ha elkészült, az összes felhasználó és csoport szinkronizálásra van beállítva az Azure AD-ből.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

A szinkronizálás hatókörének módosítása miatt az Azure AD DS felügyelt tartomány az összes adatot újraszinkronizálja. Az Azure AD DS felügyelt tartományában már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszú időt is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

További információ a szinkronizálási folyamatról: a [Azure ad Domain Services szinkronizálásának megismerése](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
