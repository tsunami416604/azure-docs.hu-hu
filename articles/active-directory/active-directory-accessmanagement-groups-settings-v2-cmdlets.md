---
title: "A csoportkezelés az Azure Active Directory PowerShell-példák |} Microsoft Docs"
description: "Ezen a lapon biztosít a PowerShell-példák segítséget nyújtanak az Azure Active Directoryban csoportok kezelése"
keywords: "Az Azure Active Directory, Azure Active Directory PowerShell, csoportok, csoportok kezelése"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 3f57e1a0ded679325c8c739e73cc79f69c037191
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Csoportok kezelése az Azure Active Directory 2-es parancsmagok
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Ez a cikk tartalmaz példákat az Azure Active Directory (Azure AD) a csoportok kezelése a PowerShell segítségével.  Azt is bemutatja, hogyan rendszerrel az Azure AD PowerShell modult. Először meg kell [töltse le az Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése
Az Azure AD PowerShell-modul telepítéséhez használja a következő parancsokat:

    PS C:\Windows\system32> install-module azuread

Győződjön meg arról, hogy a modul el lett-e telepítve, használja a következő parancsot:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Most elindíthatja a modulban a parancsmagok használatával. Az Azure AD modulban a parancsmagok teljes leírása, olvassa el az online dokumentációját [Azure Active Directory PowerShell 2-es verzió](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>A directory rendszerhez kapcsolódni
Azure AD PowerShell-parancsmagok használatával csoportok kezelése elindításához, csatlakoznia kell a kezelni kívánt könyvtár a PowerShell-munkamenethez. Használja az alábbi parancsot:

    PS C:\Windows\system32> Connect-AzureAD

A parancsmag felszólítja a könyvtár eléréséhez használni kívánt hitelesítő adataival. A jelen példában használjuk karen@drumkit.onmicrosoft.com a telepítési könyvtár eléréséhez. A parancsmag visszaadja a jóváhagyást a munkamenet sikeresen csatlakoztatva lett a címtárhoz megjelenítése:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Most elindíthatja a AzureAD-parancsmagok használatával kezelheti a csoportok a címtárban.

## <a name="retrieve-groups"></a>Csoportok beolvasása
A címtárban lévő meglévő csoportok lekéréséhez használja a Get-AzureADGroups parancsmag. 

A címtár összes csoportjainak lekérdezésére, paraméterek nélkül a parancsmagot használja:

    PS C:\Windows\system32> get-azureadgroup

A parancsmag az összes csoport a csatlakoztatott címtárhoz adja vissza.

A - objectID paraméter segítségével egy adott csoport, amelynek meg a csoport objectID beolvasásához:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

A parancsmag most visszaadja a csoportot, amelynek objectID megegyezik a megadott paraméter értékét:

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

Kereshet egy adott csoport használja a - szűrő paramétert. Ez a paraméter egy ODATA szűrőfeltétel vesz igénybe, és minden csoport a szűrőnek, az alábbi példában látható módon adja vissza:

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
> Az Azure AD PowerShell-parancsmagok valósítja meg az OData-lekérdezés szabványnak. További információkért lásd: **$filter** a [OData rendszer lekérdezési lehetőségek az OData-végpont használatával](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Csoportok létrehozása
Új csoport létrehozása a könyvtárban, a New-AzureADGroup parancsmag használható. Ez a parancsmag "Marketing" nevű új biztonsági csoportot hoz létre:

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Csoportok frissítése
Meglévő csoport frissítéséhez használja a Set-AzureADGroup parancsmagot. Ebben a példában a Microsoft épp módosított a DisplayName tulajdonsága a következő csoporthoz: "Az Intune-rendszergazdák." Először azt még keresése a csoport, a Get-AzureADGroup parancsmaggal, és a DisplayName attribútum szűrés:

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

Ezt követően a Microsoft épp módosított a Description tulajdonságot az "Intune eszköz-rendszergazdák" új érték:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Most ismét találtunk a csoportot, ha az új érték a Description tulajdonságot frissíti a rendszer látható:

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
A címtárban lévő csoport törléséhez használja a Remove-AzureADGroup parancsmag az alábbiak szerint:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Csoporttagság kezelése 
### <a name="add-members"></a>Tagok hozzáadása
Új tagok hozzáadása egy csoporthoz, az Add-AzureADGroupMember parancsmaggal. Ez a parancs hozzáadja a használtuk az előző példában Intune rendszergazdák csoport tagja:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

-ObjectId paraméter a ObjectID szeretnénk a tag hozzáadása a csoporthoz, és azt szeretné, hogy a csoportba tagként felvenni kívánt felhasználó ObjectID - RefObjectId.

### <a name="get-members"></a>Tagok beolvasása
A meglévő egy csoport tagjai, amelyet a Get-AzureADGroupMember parancsmag, ebben a példában látható módon:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Tagok eltávolítása
Távolítsa el a korábban hozzáadott a csoport tagja, használja a Remove-AzureADGroupMember parancsmag, az itt látható:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Ellenőrizze a tagok
Ellenőrizze a csoporttagságok a felhasználó, a Select-AzureADGroupIdsUserIsMemberOf parancsmag használható. Ez a parancsmag paramétereit, vesz igénybe, a ObjectId, amelyre vonatkozóan szeretné ellenőrizni a csoporttagságot a felhasználó és csoportok, amelyre vonatkozóan szeretné ellenőrizni a csoporttagságot listája. A csoportok listáját megadott "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck" típusú összetett változó formájában, így azt először hozzon létre egy változót ezzel a típussal kell lennie:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

A következő nyújtunk a komplex változó a "csoportazonosítókat" attribútumban ellenőrzéséhez csoportazonosítókat értékeit:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Most elleni $g a csoportok ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea rendelkező felhasználó csoporttagságát ellenőrizni szeretnénk, ha érhetjük el:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


A visszaadott érték, amelynek tagja a felhasználói csoportok listája. A partnerek, csoportok vagy Szolgáltatásnevekről tagsági csoportok használatával válassza AzureADGroupIdsContactIsMemberOf, válasszon-AzureADGroupIdsGroupIsMemberOf megadott listája ellenőrzési módszert is alkalmazhat vagy SELECT-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Tiltsa le a csoport létrehozása a felhasználók által
Megakadályozhatja, hogy a nem rendszergazda felhasználók biztonsági csoportok létrehozása. A Microsoft Online Directory Services (MSODS) alapértelmezés lesz a nem rendszergazda felhasználók számára csoportok létrehozását, hogy az önkiszolgáló csoportkezelési (SSGM) is engedélyezve van-e. A SSGM beállítás csak a saját alkalmazások hozzáférési panelen viselkedését szabályozza. 

A nem rendszergazdai felhasználók csoport létrehozásának letiltása:

1. Győződjön meg arról, hogy a nem rendszergazda felhasználók hozhatnak létre csoportokat:
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Ha a visszaadott érték `UsersPermissionToCreateGroupsEnabled : True`, akkor a nem rendszergazda felhasználók létrehozhatnak csoportokat. Ez a funkció letiltása:
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>Csoport tulajdonosainak kezelése
Tulajdonosok hozzáadása egy csoporthoz, az Add-AzureADGroupOwner parancsmaggal:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

A - ObjectId paraméter nem a csoport tulajdonosa hozzáadandó szeretnénk ObjectID, és - RefObjectId azt szeretnénk, ha szeretne hozzáadni a csoport tulajdonosa felhasználó ObjectID.

Egy csoport tulajdonosainak lekéréséhez használja a Get-AzureADGroupOwner parancsmagot:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

A parancsmag visszaadja a megadott csoport tulajdonosok listája:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Ha szeretne egy olyan tulajdonost eltávolítása egy csoportból, használja a Remove-AzureADGroupOwner parancsmag:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Fenntartott aliasok 
A csoport létrehozásakor egyes végpontokkal, a végfelhasználó számára adjon meg egy mailNickname vagy alias használata az e-mail cím, a csoport részeként. A következő magas szintű jogosultsággal rendelkező e-mail-aliasokkal használt csoportok csak az Azure AD globális rendszergazda hozhatók létre. 
  
* visszaélés 
* Rendszergazda 
* Rendszergazda 
* hostmaster 
* majordomo 
* postamester 
* legfelső szintű 
* Biztonságos 
* biztonság 
* SSL-rendszergazda 
* gazdáját 

## <a name="next-steps"></a>Következő lépések
További Azure Active Directory PowerShell dokumentációját a található [Azure Active Directory-modul parancsmagokkal](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
