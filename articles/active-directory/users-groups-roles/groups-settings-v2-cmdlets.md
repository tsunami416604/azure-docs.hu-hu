---
title: Csoportkezelés az Azure Active Directory PowerShell-példák |} A Microsoft Docs
description: Ez az oldal nyújt segítséget az Azure Active Directoryban csoportok kezelése PowerShell-példák
keywords: Az Azure Active Directory, Azure Active Directory PowerShell, csoportok, csoportok kezelése
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: d6fb5a97ef573a35f335875beddc7752f580bec1
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296641"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Az Azure Active Directory 2. verzió parancsmagjainak csoportfelügyelet
> [!div class="op_single_selector"]
> * [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Ez a cikk bemutatja, hogyan kezelheti a csoportokat az Azure Active Directoryban (Azure AD) a PowerShell használatával történő példák tartalmazza.  Azt is bemutatja, hogyan lehet az Azure AD PowerShell-modullal beállításához. Először be kell [töltse le az Azure AD PowerShell modul](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése
Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread

Győződjön meg arról, hogy a modul készen áll a használatra, használja a következő parancsot:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Most már megkezdheti a parancsmagokat a modulban. A parancsmagok az Azure AD-modul a teljes leírást, tekintse meg az online dokumentáció a [Azure Active Directory PowerShell 2-es verzió](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Csatlakozás a címtárhoz
Csoportkezelés az Azure AD PowerShell-parancsmagok használata előtt el kell kapcsolódnia a PowerShell-munkamenetet a címtár kezeléséhez. Használja az alábbi parancsot:

    PS C:\Windows\system32> Connect-AzureAD

A parancsmag kéri a könyvtár eléréséhez használni kívánt hitelesítő adatokat. Ebben a példában használjuk karen@drumkit.onmicrosoft.com a bemutató könyvtár eléréséhez. A parancsmag megerősítést a munkamenet sikeresen csatlakoztatva lett a címtárhoz megjelenítéséhez adja vissza:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Most már megkezdheti az Azure ad-parancsmagok segítségével kezeli a csoportokat a címtárban.

## <a name="retrieve-groups"></a>Csoportok lekérése
A meglévő csoportokat a címtárban lévő lekéréséhez használja a Get-AzureADGroups parancsmagot. 

Minden csoport a címtárban lekéréséhez használja a parancsmagot paraméterek nélkül:

    PS C:\Windows\system32> get-azureadgroup

A parancsmag visszaadja az összes csoport a csatlakoztatott címtárban.

Használhatja a - objectID paramétere, amelynek a csoport objectID megadása egy adott csoport beolvasásához:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

A parancsmag mostantól a csoportot, amelynek objectID megegyezik a paraméter megadott értékét adja vissza:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Egy adott csoport használatával kereshet a - filter paraméter. Ez a paraméter vesz igénybe egy ODATA szűrési záradékot, és adja vissza az összes csoportot, amelyek megfelelnek a szűrő, az alábbi példában látható módon:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Az Azure AD PowerShell-parancsmagok végrehajtása lekérdezés OData-szabványt. További információkért lásd: **$filter** a [OData rendszerlekérdezési beállításait az OData-végpont használatával](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Csoportok létrehozása
Hozzon létre egy új csoportot a címtárban, a New-AzureADGroup parancsmag használható. Ez a parancsmag egy új, "Marketing" nevű biztonsági csoportot hoz létre:

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Frissítési csoportok
Frissíti egy meglévő csoportot, a Set-AzureADGroup parancsmag használható. Ebben a példában azt Ha módosítja a DisplayName tulajdonság a következő csoporthoz: "Az Intune-rendszergazdák." Először azt fogjuk megtalálni a csoport a Get-AzureADGroup parancsmag használatával, és szűrheti a DisplayName attribútum használatával:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Ezután azt Ön módosítása Description tulajdonságában az "Intune Device Administrators" új érték:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Most, ha a csoport újból találunk, láthatjuk, Description tulajdonságában frissül, hogy tükrözzék az új érték:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Csoportok törlése
Csoportok a címtárban lévő törléséhez használja a Remove-AzureADGroup parancsmag a következő:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Csoporttagság kezelése 
### <a name="add-members"></a>Tagok hozzáadása
Új tag hozzáadása csoporthoz, az Add-AzureADGroupMember parancsmaggal végezhető el. Ez a parancs az Intune-rendszergazdák csoport az előző példában használt ad hozzá a tagja:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

A - ObjectId paramétere objectid azonosítóját, a szeretnénk a tag hozzáadása csoporthoz, és - RefObjectId objectid azonosítóját, a felhasználó a csoportba tagként felvenni kívánt.

### <a name="get-members"></a>A tagok lekérése
Egy csoportot a meglévő csoporttagok, amelyet a Get-AzureADGroupMember parancsmag ebben a példában látható módon:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Tagok eltávolítása
A korábban a csoporthoz hozzáadott tagok eltávolításához használja a Remove-AzureADGroupMember parancsmag, az itt látható módon:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Ellenőrizze a tagok
Ha ellenőrizni szeretné egy felhasználó csoporttagságát, a Select-AzureADGroupIdsUserIsMemberOf parancsmaggal. Ennél a parancsmagnál a paraméterekként objectid azonosítóját, a felhasználót, amelynek ellenőrizni a csoporttagságot és a csoportok, amelyek esetében szeretne ellenőrizni a tagságok listája. A csoportok listáját a megadott "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck" típusú összetett változó formájában, ezért először kell létrehozunk egy változót az adott típusú kell lennie:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Ezután ellenőrizze a komplex változó "csoportazonosítókat" attribútumban a csoportazonosítókat értékeit kínálunk:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Most ha azt szeretné ellenőrizni a elleni $g a csoportok ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea rendelkező felhasználó csoporttagságát, azt kell használnia:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


A visszaadott érték, amelynek tagja a felhasználói csoportok listája. A névjegyek, csoportok vagy szolgáltatásnevek tagsági csoportok használatával válassza AzureADGroupIdsContactIsMemberOf, válassza ki – AzureADGroupIdsGroupIsMemberOf megadott listája ellenőrzési módszert is alkalmazhat, vagy SELECT-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Tiltsa le a felhasználók által a csoport létrehozása
Megakadályozhatja, hogy a nem rendszergazda felhasználók biztonsági csoportok létrehozása. A Microsoft Online Directory Services (MSODS) alapértelmezett viselkedését, hogy engedélyezése nem rendszergazda jogosultságú felhasználók számára csoportok létrehozását, önkiszolgáló csoportfelügyelet (SSGM) is engedélyezve van-e. A SSGM beállítás csak a saját alkalmazások hozzáférési panelen viselkedését szabályozza. 

A nem rendszergazdai felhasználók számára csoportok létrehozását letiltása:

1. Győződjön meg arról, hogy a nem rendszergazda felhasználók hozhatnak létre csoportokat:
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Ha a visszaadott érték `UsersPermissionToCreateGroupsEnabled : True`, akkor a nem rendszergazda jogosultságú felhasználók létrehozhatnak csoportokat. Ez a funkció letiltása:
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>A csoportok tulajdonosainak kezelése
Tulajdonosok hozzáadása egy csoporthoz, az Add-AzureADGroupOwner parancsmaggal:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

-ObjectId paraméter objectid azonosítóját, a csoport, amelyhez szeretnénk hozzáadni egy olyan tulajdonost, és - RefObjectId objectid azonosítóját, a felhasználó szeretnénk hozzáadni a csoport tulajdonosai.

A csoport tulajdonosainak lekéréséhez használja a Get-AzureADGroupOwner parancsmagot:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

A parancsmag a megadott csoport tulajdonosainak listáját adja vissza:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Ha azt szeretné, tulajdonos eltávolítása a csoportból, használja a Remove-AzureADGroupOwner parancsmagot:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Fenntartott aliasok 
Egy csoport létrehozásakor bizonyos végpontok lehetővé teszik a végfelhasználó számára adjon meg egy mailNickname vagy a csoport e-mail-címe részeként használt alias. Az alábbi magas szintű jogosultsággal rendelkező e-mail-aliasokat a csoportok csak egy Azure AD globális rendszergazda is létrehozható. 
  
* visszaélés 
* rendszergazda 
* Rendszergazda 
* hostmaster 
* majordomo 
* postamester 
* legfelső szintű 
* biztonságos 
* biztonság 
* SSL-rendszergazda 
* gazdáját 

## <a name="next-steps"></a>További lépések
További Azure Active Directory PowerShell-dokumentáció, annak [Azure Active Directory-parancsmagok](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
