---
title: PowerShell – Azure Active Directory használatával csoport beállításainak konfigurálása |} A Microsoft Docs
description: Hogyan felügyelheti a csoportok az Azure Active Directory-parancsmagok használatával
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e838af0ac8e9cfd1d42b768fa68ec7d9f46386c6
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512227"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához
Ez a cikk útmutatást az Azure Active Directory (Azure AD) PowerShell-parancsmagok használatával hozhat létre, és a csoportokat. Ez a tartalom csak az Office 365-csoportok (más néven egyesített csoportok) vonatkozik. 

> [!IMPORTANT]
> Egyes beállítások egy Azure Active Directory Premium P1-licenc szükséges. További információkért lásd: a [sablonbeállítások](#template-settings) tábla.

További információ a biztonsági csoportok létrehozása a nem rendszergazdai felhasználók megakadályozása beállítása `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` leírtak szerint [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Az Office 365-csoportok beállításai vannak konfigurálva, a beállítási objektum és a egy SettingsTemplate objektum használatával. Kezdetben nem jelenik meg minden olyan beállítási objektumok a címtárban, mivel a címtárban az alapértelmezett beállításokkal van konfigurálva. Módosítsa az alapértelmezett beállításokat, létre kell hoznia egy új beállítások objektum beállítások sablon használatával. A Microsoft által definiált beállítások sablonok. Nincsenek számos különböző beállítások sablont. A címtár az Office 365-beállítások konfigurálása, használhatja a "Group.Unified" nevű sablont. Egyetlen csoportnak az Office 365-csoport beállításainak konfigurálásához használja a "Group.Unified.Guest" nevű sablont. Ez a sablon segítségével kezelheti a vendéghozzáférés az Office 365-csoportot. 

A parancsmagok az Azure Active Directory PowerShell V2 modul részét képezik. Útmutatás hogyan töltse le és telepítse a modult a számítógépen, tekintse meg a cikket [Azure Active Directory PowerShell 2-es verzió](https://docs.microsoft.com/powershell/azuread/). Telepítheti a modul a 2. verzió kiadását [a PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Egy adott beállítás értékének beolvasása
Ha ismeri a lekérdezni kívánt beállítás nevére, használhatja az alábbi parancsmag segítségével kérje le az aktuális beállítások értéket. Ebben a példában azt olvas be egy elnevezett "UsageGuidelinesUrl." beállítás értéke További információt a directory-beállítások és nevét az ebben a cikkben lejjebb talál.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások létrehozása
Ezeket a lépéseket beállítások létrehozása könyvtár szintjén, amely az összes Office 365-csoportok a címtárban a alkalmazni. A Get-AzureADDirectorySettingTemplate parancsmag csak érhető el a [megtekintése az Azure AD PowerShell-modul a Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. A Címtárbeállítások parancsmagok meg kell adnia a használni kívánt SettingsTemplate azonosítója. Ha nem ismeri ezt az Azonosítót, ez a parancsmag minden beállítások sablonok listáját adja vissza:
  
  ```powershell
  Get-AzureADDirectorySettingTemplate
  ```
  Ez a parancsmag-hívást az összes sablon elérhető adja vissza:
  
  ```powershell
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Használati útmutató arra az esetre URL-címet hozzáadni, először le szeretné kérni az SettingsTemplate objektum, amely meghatározza a használati útmutató arra az esetre URL-cím értéket; vagyis a Group.Unified sablon:
  
  ```powershell
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Ezután hozza létre a sablonon alapuló új beállítások objektum:
  
  ```powershell
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Ezután frissítse a használati útmutató arra az esetre értéket:
  
  ```powershell
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
  ```  
5. Végül a alkalmazni a beállításokat:
  
  ```powershell
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Sikeres telepítést a parancsmag az új beállítások objektum Azonosítóját adja vissza:

  ```powershell
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

## <a name="template-settings"></a>Sablon beállításai
Az alábbiakban a Group.Unified SettingsTemplate megadott beállítások. Hiányában az ezeket a szolgáltatásokat egy Azure Active Directory Premium P1-licenc szükséges. 

| **Beállítás** | **Leírás** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Típus: Logikai<li>Alapértelmezett: True (Igaz) |A jelzőt, amely azt jelzi, hogy az Office 365-csoport létrehozása engedélyezett-e a címtárban nem rendszergazdai felhasználók által. Ezt a beállítást nem szükséges egy Azure Active Directory Premium P1-licencet.|
|  <ul><li>GroupCreationAllowedGroupId<li>Típus: String<li>Alapértelmezett érték: "" |A biztonsági csoport, amelynek a tagjai hozhatnak létre az Office 365-csoportok GUID akkor is, ha EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Típus: String<li>Alapértelmezett érték: "" |A Csoporthasználati mutató hivatkozást. |
|  <ul><li>ClassificationDescriptions<li>Típus: String<li>Alapértelmezett érték: "" | Besorolási leírások vesszővel tagolt listája. ClassificationDescriptions értéke csak érvényes a következő formátumban:
  $setting ["ClassificationDescriptions"] = "Besorolást: leírás, besorolási: Description", ahol a besorolás megegyezik a ClassificationList a karakterláncokat.|
|  <ul><li>DefaultClassification<li>Típus: String<li>Alapértelmezett érték: "" | A besorolás, amely használható az alapértelmezett besorolást egy csoportot, ha nem.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Típus: String<li>Alapértelmezett érték: "" | Egy legfeljebb 64 karakter hosszúságú, amely meghatározza az Office 365-csoportok konfigurált elnevezési karakterlánc. További információkért lásd: [Office 365-csoportokra vonatkozó elnevezési szabályzat kényszerítése](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Típus: String<li>Alapértelmezett érték: "" | Vesszővel tagolt karakterláncot, amely a felhasználók csoport a nevek és aliasok használata nem fog tudni kifejezések közül. További információkért lásd: [Office 365-csoportokra vonatkozó elnevezési szabályzat kényszerítése](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Típus: Logikai<li>Alapértelmezett: "False" | Ne használja
|  <ul><li>AllowGuestsToBeGroupOwner<li>Típus: Logikai<li>Alapértelmezett: False (Hamis) | Logikai érték-e a vendégfelhasználó lehet-e a csoport tulajdonosa jelzi. |
|  <ul><li>AllowGuestsToAccessGroups<li>Típus: Logikai<li>Alapértelmezett: True (Igaz) | Jelző logikai érték beolvasása e vendég felhasználók még az Office 365-csoportok tartalomhoz való hozzáférését.  Ezt a beállítást nem szükséges egy Azure Active Directory Premium P1-licencet.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Típus: String<li>Alapértelmezett érték: "" | A Vendég használati útmutató mutató URL-címe |
|  <ul><li>AllowToAddGuests<li>Típus: Logikai<li>Alapértelmezett: True (Igaz) | Egy logikai jelző Vendégek hozzáadása a címtárhoz engedélyezett-e.|
|  <ul><li>ClassificationList<li>Típus: String<li>Alapértelmezett érték: "" |Az Office 365-csoportokra alkalmazható érvényes osztályozási értékeket vesszővel tagolt listája. |

## <a name="read-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások beolvasása
Ezeket a lépéseket olvassa el a könyvtár szintjén beállításokat, amelyeket a alkalmazni az összes Office-csoportok a címtárban.

1. Olvassa el az összes meglévő címtár beállításai:
  ```powershell
  Get-AzureADDirectorySetting -All $True
  ```
  Ez a parancsmag az összes címtár beállításai listáját adja vissza:
  ```powershell
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Olvassa el az egy adott csoport összes beállítása:
  ```powershell
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Olvassa el az összes könyvtár beállítások értéket egy adott címtárhoz beállítások objektum, a beállítások azonosító GUID használatával:
  ```powershell
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Ez a parancsmag adja vissza a tartalmazó objektum az adott csoport neveket és értékeket:
  ```powershell
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

1. Keresse meg a "Groups.Unified.Guest" nevű beállítások sablon
  ```powershell
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Kérje le a sablonobjektum Groups.Unified.Guest sablon:
  ```powershell
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Hozzon létre egy új beállítások objektumot a sablonból:
  ```powershell
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Állítsa a beállítást a szükséges érték:
  ```powershell
  $Setting["AllowToAddGuests"]=$False
  ```
5. Az új beállítás a szükséges csoport létrehozása a könyvtárban:
  ```powershell
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>A könyvtár szintjén beállításainak frissítése

Ezeket a lépéseket minden Office 365-csoportok a címtárban könyvtár szintjén beállítások frissítése. Ezek a példák feltételezik, hogy már van egy beállítási objektumot a címtárban.

1. Keresse meg a meglévő beállítási objektumot:
  ```powershell
  $setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
2. Frissítse az értéket:
  
  ```powershell
  $Setting["AllowToAddGuests"] = "false"
  ```
3. A beállítás frissítése:
  
  ```powershell
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>A könyvtár szintjén beállítások eltávolítása
Ez a lépés eltávolítja a könyvtár szintjén beállításokat, amelyeket a alkalmazni az összes Office-csoportok a címtárban.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>A parancsmag szintaxisának referenciája
További Azure Active Directory PowerShell-dokumentáció, annak [Azure Active Directory-parancsmagok](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>További olvasnivaló

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
