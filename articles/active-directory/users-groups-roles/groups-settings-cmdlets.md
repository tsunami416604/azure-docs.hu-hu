---
title: Csoportházirend-beállítások konfigurálása a PowerShell használatával – Azure AD | Microsoft Docs
description: Csoportok beállításainak kezelése Azure Active Directory-parancsmagok használatával
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 6a1b900d4a67390ae867d770c3b984c43fd501b5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026754"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához

Ez a cikk a csoportok létrehozásához és frissítéséhez szükséges Azure Active Directory (Azure AD) PowerShell-parancsmagok használatára vonatkozó utasításokat tartalmazza. Ez a tartalom csak az Office 365-csoportokra vonatkozik (más néven Unified groups).

> [!IMPORTANT]
> Egyes beállításokhoz prémium szintű Azure Active Directory P1 licenc szükséges. További információt a [sablon beállításai](#template-settings) táblázatban talál.

A nem rendszergazda felhasználók biztonsági csoportok létrehozásával kapcsolatos további információkért állítsa be a `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` a [set-msolcompanysettings parancsmagjával](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)című témakörben leírtak szerint.

Az Office 365-csoportok beállításait egy Setting objektummal és egy SettingsTemplate objektummal kell konfigurálni. Kezdetben a címtárban nem jelennek meg beállítások objektumok, mert a címtár az alapértelmezett beállításokkal van konfigurálva. Az alapértelmezett beállítások módosításához egy új beállítási objektumot kell létrehoznia egy beállítási sablonnal. A beállítások sablonjait a Microsoft határozza meg. Több különböző beállítási sablon is létezik. A címtárhoz tartozó Office 365-csoport beállításainak konfigurálásához használja a "Group. Unified" nevű sablont. Az Office 365-csoport beállításainak egyetlen csoportra való konfigurálásához használja a "Group. Unified. Guest" nevű sablont. Ez a sablon egy Office 365-csoport vendég-hozzáférésének kezelésére szolgál. 

A parancsmagok a Azure Active Directory PowerShell V2 modul részét képezik. A modul számítógépekre történő letöltéséről és telepítéséről a [PowerShell 2. verziójának Azure Active Directory](https://docs.microsoft.com/powershell/azuread/)cikkében talál útmutatást. A modul 2. verziójának kiadását [a PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAD/)is telepítheti.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

A PowerShell-parancsok futtatása előtt mindenképpen távolítsa el a Windows PowerShellhez készült Azure Active Directory PowerShell for Graph modul összes régebbi verzióját, és telepítse az [Azure Active Directory PowerShell for Graph 2.0.0.137-es nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nyissa meg a Windows PowerShell alkalmazást rendszergazdaként.
2. Távolítsa el az AzureADPreview korábbi verzióit.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Telepítse az AzureADPreview legújabb verzióját.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Beállítások létrehozása a címtár szintjén
Ezek a lépések a címtár szintjén hoznak létre beállításokat, amelyek a címtárban található összes Office 365-csoportra érvényesek. A Get-AzureADDirectorySettingTemplate parancsmag csak a [Graph Azure ad PowerShell előzetes verziójú moduljában](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)érhető el.

1. A Directorysetting objektumok-parancsmagokban meg kell adnia a használni kívánt SettingsTemplate AZONOSÍTÓját. Ha nem ismeri ezt az azonosítót, a parancsmag az összes beállítási sablon listáját adja vissza:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Ez a parancsmag-hívás az összes elérhető sablont visszaadja:
  
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
2. Használati útmutató URL-címének hozzáadásához először le kell kérnie a SettingsTemplate objektumot, amely meghatározza a használati útmutató URL-címét. Ez a Group. Unified sablon:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Ezután hozzon létre egy új beállítási objektumot a sablon alapján:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Ezután frissítse a használati útmutató értékét:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Ezután alkalmazza a beállítást:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Az értékeket a következő paranccsal olvashatja:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Beállítások frissítése a címtár szintjén
A UsageGuideLinesUrl értékének a beállítás sablonban való frissítéséhez egyszerűen szerkessze az URL-címet a fenti 4. lépéssel, majd hajtsa végre az 5. lépést az új érték beállításához.

A UsageGuideLinesUrl értékének eltávolításához szerkessze az URL-címet üres karakterláncként a fenti 4. lépés használatával:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Ezután hajtsa végre az 5. lépést az új érték megadásához.

## <a name="template-settings"></a>Sablon beállításai
Itt láthatók a Group. Unified SettingsTemplate megadott beállítások. Ha másként nincs jelezve, ezeknek a szolgáltatásoknak prémium szintű Azure Active Directory P1 licencre van szükségük. 

| **Beállítás** | **Leírás** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Típus: Boolean<li>Alapértelmezett: true |Az a jelző, amely azt jelzi, hogy az Office 365-csoport létrehozása engedélyezve van-e a címtárban a nem rendszergazda felhasználók számára. Ehhez a beállításhoz nem szükséges prémium szintű Azure Active Directory P1-licenc.|
|  <ul><li>GroupCreationAllowedGroupId<li>Típus: Sztring<li>Alapértelmezett: "" |Annak a biztonsági csoportnak a GUID azonosítója, amelynek tagjai számára engedélyezett az Office 365-csoportok létrehozása, még akkor is, ha EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett: "" |A csoport használati iránymutatásaira mutató hivatkozás. |
|  <ul><li>ClassificationDescriptions<li>Típus: Sztring<li>Alapértelmezett: "" | A besorolási leírások vesszővel tagolt listája. A ClassificationDescriptions értéke csak ebben a formátumban érvényes:<br>$setting ["ClassificationDescriptions"] = "besorolás: Leírás, besorolás: Leírás"<br>ahol a besorolás megegyezik a ClassificationList található karakterláncokkal.|
|  <ul><li>DefaultClassification<li>Típus: Sztring<li>Alapértelmezett: "" | Az a besorolás, amelyet egy csoport alapértelmezett besorolásként kell használni, ha nincs megadva.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Típus: Sztring<li>Alapértelmezett: "" | Legfeljebb 64 karakterből álló karakterlánc, amely meghatározza az Office 365-csoportokhoz konfigurált elnevezési konvenciót. További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Típus: Sztring<li>Alapértelmezett: "" | Vesszővel tagolt karakterláncok, amelyeket a felhasználók nem használhatnak a csoportok neveiben vagy az Aliasokban. További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Típus: Boolean<li>Alapértelmezett: "false" | Ne használja
|  <ul><li>AllowGuestsToBeGroupOwner<li>Típus: Boolean<li>Alapértelmezett: false | Logikai érték, amely azt jelzi, hogy a vendég felhasználó lehet-e a csoportok tulajdonosa. |
|  <ul><li>AllowGuestsToAccessGroups<li>Típus: Boolean<li>Alapértelmezett: true | Logikai érték, amely azt jelzi, hogy a vendég felhasználó tud-e hozzáférni az Office 365-csoportok tartalmához.  Ehhez a beállításhoz nem szükséges prémium szintű Azure Active Directory P1-licenc.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett: "" | A vendég használati irányelvekre mutató hivatkozás URL-címe. |
|  <ul><li>AllowAddGuests<li>Típus: Boolean<li>Alapértelmezett: true | Logikai érték, amely azt jelzi, hogy engedélyezett-e a vendégek hozzáadása a címtárhoz.|
|  <ul><li>ClassificationList<li>Típus: Sztring<li>Alapértelmezett: "" |Az Office 365-csoportokra alkalmazható érvényes besorolási értékek vesszővel tagolt listája. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Példa: a csoportokra vonatkozó vendég házirend konfigurálása a címtár szintjén
1. Az összes beállítási sablon beolvasása:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Ha a csoportok számára a címtár szintjén szeretné beállítani a vendég házirendet, a Group. Unified sablonra van szükség.
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Ezután hozzon létre egy új beállítási objektumot a sablon alapján:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Ezután frissítse a AllowAddGuests-beállítást
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Ezután alkalmazza a beállítást:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Az értékeket a következő paranccsal olvashatja:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Beállítások beolvasása a könyvtár szintjén

Ha ismeri a lekérdezni kívánt beállítás nevét, az alábbi parancsmaggal kérheti le az aktuális beállítások értékét. Ebben a példában egy "UsageGuidelinesUrl" nevű beállítás értékét kérdezi le. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Ezek a lépések a címtár szintjén olvassák el a beállításokat, amelyek a címtárban található összes Office-csoportra érvényesek.

1. Az összes létező címtár-beállítás olvasása:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Ez a parancsmag az összes címtár-beállítás listáját adja vissza:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Egy adott csoport összes beállításának olvasása:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Egy adott címtár-beállítási objektum összes címtár-beállítási értékének beolvasása a Settings ID GUID azonosító használatával:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Ez a parancsmag az ebben a beállítási objektumban szereplő neveket és értékeket adja vissza ehhez az adott csoporthoz:
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
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Beállítások eltávolítása a könyvtár szintjén
Ezzel a lépéssel eltávolítja a beállításokat a címtár szintjén, amely az összes Office-csoportra érvényes a könyvtárban.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Adott csoport beállításainak létrehozása

1. Keressen rá a "groups. Unified. Guest" nevű beállítási sablonra.
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
2. A groups. Unified. Guest sablon sablon objektumának beolvasása:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Hozzon létre egy új beállítási objektumot a sablonból:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Állítsa a beállítást a szükséges értékre:
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Szerezze be annak a csoportnak az AZONOSÍTÓját, amelyre alkalmazni kívánja ezt a beállítást:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Hozza létre az új beállítást a szükséges csoport számára a címtárban:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. A beállítások ellenőrzéséhez futtassa a következő parancsot:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Adott csoport beállításainak frissítése
1. Szerezze be annak a csoportnak az AZONOSÍTÓját, amelynek a beállításait frissíteni kívánja:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. A csoport beállításának beolvasása:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Frissítse a csoport beállítását, mint amennyire szüksége van, például:
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Ezután kérje le az adott csoport beállításának AZONOSÍTÓját:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Ehhez a következőhöz hasonló választ fog kapni:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Ezt követően beállíthatja a beállítás új értékét:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. A beállítás értékének beolvasásával győződjön meg arról, hogy az megfelelően frissült:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Parancsmag szintaxisának referenciája
[Azure Active Directory-parancsmagokkal](/powershell/azure/install-adv2?view=azureadps-2.0)kapcsolatban további Azure Active Directory PowerShell-dokumentációt talál.

## <a name="additional-reading"></a>További olvasnivaló

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
