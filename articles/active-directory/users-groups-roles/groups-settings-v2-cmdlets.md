---
title: Példák a PowerShell V2 csoportok kezelésére – Azure AD | Microsoft dokumentumok
description: Ez a lap PowerShell-példákat tartalmaz a csoportok Azure Active Directoryban való kezeléséhez
keywords: Azure AD, Azure Active Directory, PowerShell, Csoportok, Csoportkezelés
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233116"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Az Azure Active Directory 2-es verziójának parancsmagjai a csoportkezeléshez

> [!div class="op_single_selector"]
> - [Azure-portál](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [Powershell](groups-settings-v2-cmdlets.md)
>
>

Ez a cikk példákat tartalmaz arra, hogyan használhatja a PowerShellt a csoportok kezelésére az Azure Active Directoryban (Azure AD).  Azt is bemutatja, hogyan lehet beállítani az Azure AD PowerShell-modul. Először le kell [töltenie az Azure AD PowerShell-modult.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell-modul telepítése

Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, használja a következő parancsot:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Most már elkezdheti használni a modul parancsmagjait. Az Azure AD modul parancsmagjainak teljes leírását az [Azure Active Directory PowerShell 2-es verziójának](/powershell/azure/install-adv2?view=azureadps-2.0)online referenciadokumentációjában találja.

## <a name="connect-to-the-directory"></a>Csatlakozás a könyvtárhoz

Mielőtt elkezdheti a csoportok kezelését az Azure AD PowerShell-parancsmagokkal, csatlakoztatnia kell a PowerShell-munkamenetet a kezelni kívánt könyvtárhoz. Használja az alábbi parancsot:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

A parancsmag a címtár eléréséhez használni kívánt hitelesítő adatok megadását kéri. Ebben a példában karen@drumkit.onmicrosoft.com a bemutató könyvtár eléréséhez használjuk. A parancsmag megerősítést ad arról, hogy a munkamenet sikeresen csatlakozott a címtárhoz:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Most már elkezdheti használni az AzureAD-parancsmagokat a címtárban lévő csoportok kezeléséhez.

## <a name="retrieve-groups"></a>Csoportok beolvasása

Meglévő csoportok beolvasásához a címtárból, használja a Get-AzureADGroups parancsmag. 

A címtárösszes csoportjának beolvasásához használja a parancsmabot paraméterek nélkül:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

A parancsmag a csatlakoztatott könyvtár összes csoportját adja vissza.

Az -objectID paraméter rel egy adott csoport beolvasásához használhatja, amelyhez a csoport objektumazonosítóját adja meg:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

A parancsmag most azt a csoportot adja vissza, amelynek objectID azonosítója megegyezik a megadott paraméter értékével:

```powershell
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
```

Egy adott csoportot a -filter paraméterrel kereshet. Ez a paraméter egy ODATA szűrőzáradékot vesz fel, és a következő példában szereplő összes olyan csoportot visszaadja, amely megfelel a szűrőnek:

```powershell
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
```

> [!NOTE]
> Az Azure AD PowerShell-parancsmagok megvalósítják az OData-lekérdezési szabványt. További információt az [OData-végpont használatával az OData-végpont használatával](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)$filter az OData rendszer lekérdezési beállításaiban című témakörben talál. **$filter**

## <a name="create-groups"></a>Csoportok létrehozása

Új csoport létrehozásához a címtárban használja a New-AzureADGroup parancsmag. Ez a parancsmag létrehoz egy új biztonsági csoportot, a "Marketing" nevű csoportot:

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Csoportok frissítése

Meglévő csoport frissítéséhez használja a Set-AzureADGroup parancsmag. Ebben a példában az "Intune rendszergazdák" csoport DisplayName tulajdonságát módosítjuk. Először is a Get-AzureADGroup parancsmag és -szűrő használatával találjuk meg a csoportot a DisplayName attribútum használatával:

```powershell
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
```

Ezután módosítjuk a Description tulajdonságot az "Intune-eszközrendszergazdák" új értékre:

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Most, ha újra megtaláljuk a csoportot, azt látjuk, hogy a Description tulajdonság frissül, hogy tükrözze az új értéket:

```powershell
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
```

## <a name="delete-groups"></a>Csoportok törlése

Csoportok törlése a címtárból, használja az Eltávolítás-AzureADGroup parancsmag az alábbiak szerint:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Csoporttagság kezelése

### <a name="add-members"></a>Tagok hozzáadása

Ha új tagokat szeretne hozzáadni egy csoporthoz, használja az Add-AzureADGroupMember parancsmag. Ez a parancs hozzáad egy tagot az előző példában használt Intune Rendszergazdák csoporthoz:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

A -ObjectId paraméter annak a csoportnak az ObjectID azonosítója, amelyhez tagot szeretnénk hozzáadni, és a -RefObjectId annak a felhasználónak az ObjectID azonosítója, amelyet tagként szeretnénk hozzáadni a csoporthoz.

### <a name="get-members"></a>Tagok beszereznie

Egy csoport meglévő tagjainak lekért, használja a Get-AzureADGroupMember parancsmag, mint ebben a példában:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Tagok eltávolítása

A csoporthoz korábban felvett tag eltávolításához használja az Eltávolítás-AzureADGroupMember parancsmalapot, ahogy az itt látható:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Tagok ellenőrzése

A felhasználó csoporttagságainak ellenőrzéséhez használja a Select-AzureADGroupIdsUserIsMemberOf parancsmag. Ez a parancsmag a paraméterek a felhasználó ObjectId, amelynek a csoporttagságok, és a csoportok listáját, amelyek hez, hogy ellenőrizze a tagságok. A csoportok listáját "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck" típusú összetett változó formájában kell megadni, ezért először létre kell hoznunk egy ilyen típusú változót:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Ezután értékeket adunk a groupIds-nek, hogy ellenőrizze a "GroupIds" attribútumot ennek az összetett változónak:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Most, ha azt akarjuk, hogy ellenőrizze a csoport tagsága a felhasználó ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea ellen csoportok $g, akkor kell használni:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

A visszaadott érték azoknak a csoportoknak a listája, amelyeknek ez a felhasználó tagja. Ezt a módszert a Kapcsolattartók, csoportok vagy egyszerű szolgáltatástagok tagságának ellenőrzésére is alkalmazhatja a Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf vagy Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>A felhasználók csoportlétrehozásának letiltása

Megakadályozhatja, hogy a nem rendszergazda felhasználók biztonsági csoportokat hozzanak létre. A Microsoft Online Directory Services (MSODS) alapértelmezett viselkedése az, hogy lehetővé teszi a nem rendszergazda felhasználók számára, hogy csoportokat hozzanak létre, függetlenül attól, hogy az önkiszolgáló csoportkezelés (SSGM) is engedélyezve van-e. Az SSGM beállítás csak a Saját alkalmazások hozzáférési panelen szabályozza a viselkedést.

Csoportlétrehozás letiltása nem rendszergazda felhasználók számára:

1. Ellenőrizze, hogy a nem rendszergazda felhasználók hozhatnak-e létre csoportokat:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Ha visszaadja `UsersPermissionToCreateGroupsEnabled : True`a lehetőséget, akkor a nem rendszergazda felhasználók csoportokat hozhatnak létre. A szolgáltatás letiltása:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Csoportok tulajdonosainak kezelése

Tulajdonosok hozzáadása egy csoporthoz, használja az Add-AzureADGroupOwner parancsmag:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

A -ObjectId paraméter annak a csoportnak az ObjectID azonosítója, amelyhez tulajdonost szeretnénk hozzáadni, és a -RefObjectId a csoport tulajdonosaként hozzáadni kívánt felhasználó vagy szolgáltatásnév ObjectID azonosítója.

Egy csoport tulajdonosainak lekéréséhez használja a Get-AzureADGroupOwner parancsmag:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

A parancsmag a megadott csoport tulajdonosainak (felhasználók és szolgáltatástagok) listáját adja vissza:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Ha el szeretne távolítani egy tulajdonost egy csoportból, használja az Eltávolítás-AzureADGroupOwner parancsmast:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Fenntartott aliasok

Csoport létrehozásakor bizonyos végpontok lehetővé teszik a végfelhasználó számára, hogy megadjon egy mailNickname vagy alias a csoport e-mail címének részeként.A következő magas szintű jogosultsággal rendelkező e-mail aliasokkal rendelkező csoportokat csak egy Azure AD globális rendszergazda hozhatja létre. 
  
* Visszaélés
* admin
* adminisztrátor
* hostmaster
* majordomo között
* Postamester
* Gyökér
* Biztonságos
* biztonság
* ssl-admin
* Webmester

## <a name="group-writeback-to-on-premises-preview"></a>Csoportos visszaírás a helyszíni környezetbe (előzetes verzió)

Ma sok csoport továbbra is felügyelt a helyszíni Active Directoryban. A felhőcsoportok helyszíni szinkronizálására vonatkozó kérésekre való válaszadáshoz az Office 365 csoportok visszaírási szolgáltatása az Azure AD-hez már előzetes verzióban is elérhető.

Az Office 365-csoportok a felhőben jönnek létre és kezelhetők. A visszaírási funkció lehetővé teszi, hogy az Office 365-csoportokat terjesztési csoportként írja vissza egy Active Directory erdőbe, amelyen telepítve van az Exchange. A helyszíni Exchange-postaládával rendelkező felhasználók ezután e-maileket küldhetnek és fogadhatnak ezekből a csoportokból. A csoport visszaírási funkció nem támogatja az Azure AD biztonsági csoportok vagy terjesztési csoportok.

További részletekért tekintse meg az [Azure AD Connect szinkronizálási szolgáltatásdokumentációját.](../hybrid/how-to-connect-syncservice-features.md)

Az Office 365-ös csoportvisszaírás az Azure Active Directory (Azure AD) nyilvános előzetes verziójú szolgáltatása, és bármely fizetős Azure AD licenccsomaggal elérhető. Az előzetes verziókkal kapcsolatos jogi információkat a [Microsoft Azure előzetes verziók kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="next-steps"></a>További lépések

További Azure Active Directory PowerShell-dokumentációmegtalálható az [Azure Active Directory-parancsmagokban.](/powershell/azure/install-adv2?view=azureadps-2.0)

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
