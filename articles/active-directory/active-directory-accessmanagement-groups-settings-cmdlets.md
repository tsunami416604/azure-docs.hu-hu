---
title: Az Azure Active Directory PowerShell használatával beállításainak konfigurálásához |} Microsoft Docs
description: Hogyan az Azure Active Directory-parancsmagok használatával csoportok beállításainak kezelése
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 5da5bd920becdc86e133cb1b4e67fb3125c2638e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260532"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához
Ez a cikk az Azure Active Directory (Azure AD) PowerShell-parancsmagok használatával történő létrehozásához és frissítési csoportokká utasításokat tartalmaz. Ez a tartalom csak az Office 365-csoportok (más néven egyesített csoportok) vonatkozik. 

> [!IMPORTANT]
> Egyes beállítások egy Azure Active Directory Premium P1 licenc szükséges. További információkért lásd: a [sablonbeállítások](#template-settings) tábla.

További információ a nem rendszergazda felhasználók számára, hogy hogyan *biztonsági* csoportok, állítsa be `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` leírtak [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Az Office 365 csoportok beállításai a beállítási objektumot és egy SettingsTemplate objektum használatával. Kezdetben nem lát minden objektumokat a könyvtárban, a könyvtár van beállítva, az alapértelmezett beállításokkal. Ha módosítani szeretné az alapértelmezett beállításokat, a beállítások sablon használatával új beállítási objektumot kell létrehoznia. Beállítások sablonok Microsoft határozzák meg. Nincsenek számos különböző beállításokat sablont. Hogy a címtárban a Office 365-beállításainak konfigurálásához, a "Group.Unified" nevű sablont használ. Egy különálló csoportot az Office 365 csoport beállításainak konfigurálásához használja a "Group.Unified.Guest" nevű sablont. Ez a sablon Vendég hozzáférést az Office 365-csoportok kezelésére szolgál. 

A parancsmagok az Azure Active Directory PowerShell V2 modulja részét képezik. További tájékoztatást szeretne töltse le és a modul telepítése a számítógépre, tekintse meg a cikket [Azure Active Directory PowerShell 2-es verzió](https://docs.microsoft.com/powershell/azuread/). A modulnak a 2-es verziójú kiadásában telepítése [a PowerShell-galériában](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Egy adott beállítás értékének beolvasása
Ha ismeri a keresett beállítás nevét, használhatja az alábbi parancsmagot, hogy a jelenlegi beállítás értékének beolvasása. Ebben a példában azt olvas be a "UsageGuidelinesUrl." nevű beállítás értéke Címtár-beállítások és a nevek kapcsolatos további le a cikkben olvasható.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások létrehozása
Ezeket a lépéseket beállítások létrehozása könyvtár szinten, amelyek vonatkoznak az összes Office 365-csoportokat a címtárban.

1. A DirectorySettings parancsmagok meg kell adnia a használni kívánt SettingsTemplate Azonosítóját. Ha nem ismeri ezt az Azonosítót, ez a parancsmag az összes beállítások sablonok listáján adja vissza:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Ez a parancsmag hívás elérhető összes sablonok adja vissza:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. A használati iránymutatás URL-cím hozzáadásához először kell beszereznie a SettingsTemplate objektum, amely meghatározza a használati iránymutatás URL-érték; Ez azt jelenti, hogy a Group.Unified sablont:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Ezután hozzon létre egy új beállítási objektumot, hogy a sablon alapján:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Ezután frissítse a használati iránymutatás értéket:
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"

  ```  
5. Végezetül a beállítások alkalmazásához:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Sikeres létrehozása után a parancsmag az új beállítások objektum Azonosítóját adja vissza:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Sablon beállításait
Az alábbiakban a Group.Unified SettingsTemplate megadott beállításoknak. Eltérő rendelkezés hiányában, ezen funkciók szükségesek egy Azure Active Directory Premium P1 licencet. 

| **Beállítás** | **Leírás** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Típus: logikai<li>Alapértelmezett: igaz |A jelzőt, amely azt jelzi, hogy Office 365-csoport létrehozása a könyvtárban által engedélyezett nem rendszergazda felhasználóknak. Ez a beállítás nem szükséges egy Azure Active Directory Premium P1 licencet.|
|  <ul><li>GroupCreationAllowedGroupId<li>Típus: Sztring<li>Alapértelmezett érték: "" |A biztonsági csoport, amelynek a tagjai hozhatnak létre az Office 365-csoportok GUID akkor is, ha EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett érték: "" |A csoport használatára vonatkozó irányelvek mutató hivatkozást. |
|  <ul><li>ClassificationDescriptions<li>Típus: Sztring<li>Alapértelmezett érték: "" | Besorolási leírások vesszővel tagolt listája. |
|  <ul><li>DefaultClassification<li>Típus: Sztring<li>Alapértelmezett érték: "" | A besorolás, amely használható a alapértelmezett besorolást egy csoportra, ha nincs megadva.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Típus: Sztring<li>Alapértelmezett érték: "" | Legfeljebb 64 karakter hosszúságú lehet, amely meghatározza az Office 365-csoportok beállítása elnevezési karakterlánc. További információkért lásd: [elnevezési vonatkozó szabályzat alkalmazása az Office 365-csoportok (előzetes verzió)](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Típus: Sztring<li>Alapértelmezett érték: "" | Vesszővel elválasztott karakterlánc, amely a felhasználók nem engedélyezhető a felügyeleticsoport-nevek és aliasok használandó kifejezések közül. További információkért lásd: [elnevezési vonatkozó szabályzat alkalmazása az Office 365-csoportok (előzetes verzió)](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Típus: logikai<li>Alapértelmezett érték: "False" | Ne használja
|  <ul><li>AllowGuestsToBeGroupOwner<li>Típus: logikai<li>Alapértelmezett: hamis | Logikai érték-e a Vendég felhasználói csoportok tulajdonosa lehet jelző. |
|  <ul><li>AllowGuestsToAccessGroups<li>Típus: logikai<li>Alapértelmezett: igaz | Jelző logikai érték beolvasása-e a Vendég felhasználó rendelkezhet Office 365 csoportok tartalomhoz való hozzáférést.  Ez a beállítás nem szükséges egy Azure Active Directory Premium P1 licencet.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett érték: "" | A Vendég használatára vonatkozó irányelvek mutató hivatkozás URL-címét. |
|  <ul><li>AllowToAddGuests<li>Típus: logikai<li>Alapértelmezett: igaz | Egy logikai jelző Vendégek hozzáadása a következő könyvtár számára engedélyezett-e.|
|  <ul><li>ClassificationList<li>Típus: Sztring<li>Alapértelmezett érték: "" |Office 365-csoportok alkalmazható érvényes osztályozási értékeket vesszővel tagolt listája. |

## <a name="read-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások beolvasása
Ezeket a lépéseket a címtár összes Office-csoport könyvtár szintjén beállítások olvasása.

1. Olvassa el az összes meglévő címtár beállításai:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Ez a parancsmag az összes könyvtárbeállításai listáját adja vissza:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Egy adott csoport összes beállítások beolvasása:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Olvassa el az összes könyvtár beállítási értékei egy meghatározott könyvtár beállítások objektum, a beállítások azonosító GUID Azonosítónak a használatával:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Ez a parancsmag az adott csoport a beállítási objektumot a neveket és értékeket adja vissza:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  CustomBlockedWordsList        
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            https://guideline.example.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Egy adott csoport beállításainak frissítése

1. Keresse meg a "Groups.Unified.Guest" nevű beállítások sablont
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. A sablon objektumának Groups.Unified.Guest sablon beolvasása:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Hozzon létre egy új beállítási objektumot a sablon alapján:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Állítsa be a szükséges érték:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. A szükséges csoportot az új beállítás létrehozása a könyvtárban:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>A könyvtár szintjén beállításainak frissítése

Ezeket a lépéseket az összes Office 365-csoportokat a címtárban könyvtár szintjén beállítások frissítése. Ezek a példák feltételezik, hogy már létezik a beállítási objektumot a címtárban.

1. A meglévő beállítások objektum keresése:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Frissítse az értéket:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. A beállítás módosítása:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások eltávolítása
Ez a lépés eltávolítja a címtár összes Office-csoport könyvtár szintjén beállítások.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>A parancsmag szintaxisa referencia
További Azure Active Directory PowerShell dokumentációját a található [Azure Active Directory-modul parancsmagokkal](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>További olvasnivaló

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
