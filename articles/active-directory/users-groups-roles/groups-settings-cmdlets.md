---
title: Csoportbeállítások konfigurálása a PowerShell használatával – Azure AD | Microsoft dokumentumok
description: Csoportok beállításainak kezelése az Azure Active Directory-parancsmagokkal
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d56bb7c30a8289fe7f261979dca6a4ffe2bfe99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048142"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához

Ez a cikk utasításokat tartalmaz az Azure Active Directory (Azure AD) PowerShell-parancsmagok használatával csoportok létrehozásához és frissítéséhez. Ez a tartalom csak az Office 365-csoportokra (más néven egyesített csoportokra) vonatkozik.

> [!IMPORTANT]
> Egyes beállítások hoz egy Azure Active Directory Premium P1 licenc. További információt a [Sablon beállítások](#template-settings) táblázatban talál.

Ha többet szeretne tudni arról, hogy miként `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` akadályozhatja meg a nem rendszergazda i rendszergazdai felhasználókat a biztonsági csoportok létrehozásában, állítsa be a [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)beállítás csoportban leírtak szerint.

Az Office 365 Csoportok beállításai egy Settings objektum és egy SettingsTemplate objektum használatával vannak konfigurálva. Kezdetben nem jelenik meg a Beállítások objektum a könyvtárban, mert a könyvtár az alapértelmezett beállításokkal van konfigurálva. Az alapértelmezett beállítások módosításához új beállítási objektumot kell létrehoznia egy beállítássablon használatával. A beállítássablonokat a Microsoft határozza meg. Számos különböző beállítási sablon létezik. Az Office 365 csoportbeállításainak konfigurálásához használja a "Group.Unified" nevű sablont. Ha egyetlen csoportra szeretné konfigurálni az Office 365 csoport beállításait, használja a "Group.Unified.Guest" nevű sablont. Ezzel a sablonnal kezelheti az Office 365-csoportokhoz való vendéghozzáférést. 

A parancsmagok az Azure Active Directory PowerShell V2 modul részét képezik. A modul letöltéséről és telepítéséről az Azure [Active Directory PowerShell 2-es verziója](https://docs.microsoft.com/powershell/azuread/)című cikkben olvashat. A modul 2-es verzióját a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAD/)telepítheti.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

Győződjön meg arról, hogy távolítsa el az Azure Active Directory PowerShell for Graph module for Windows PowerShell, és telepítse az [Azure Active Directory PowerShell for Graph – Nyilvános előzetes kiadás (később 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) a PowerShell-parancsok futtatása előtt.

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
   
## <a name="create-settings-at-the-directory-level"></a>Beállítások létrehozása könyvtárszinten
Ezek a lépések címtárszintű beállításokat hoznak létre, amelyek a címtár ban lévő összes Office 365-csoportra vonatkoznak. A Get-AzureADDirectorySettingTemplate parancsmag csak az [Azure AD PowerShell előzetes modul graph](https://www.powershellgallery.com/packages/AzureADPreview)érhető el.

1. A DirectorySettings parancsmagokban meg kell adnia a használni kívánt SettingsTemplate azonosítóját. Ha nem ismeri ezt az azonosítót, a parancsmag az összes beállítássablon listáját adja vissza:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Ez a parancsmag-hívás az összes elérhető sablont visszaadja:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Használati irányelv URL-címének hozzáadásához először be kell szereznie a SettingsTemplate objektumot, amely meghatározza a használati irányelv URL-értékét; azaz a Group.Unified sablon:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Ezután hozzon létre egy új beállítási objektumot a sablon alapján:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Ezután frissítse a használati irányvonal értékét:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Ezután alkalmazza a beállítást:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Az értékeket a következő használatával olvashatja el:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Beállítások frissítése a könyvtár szintjén
A használati sablonban található UsageGuideLinesUrl értékének frissítéséhez olvassa el az Azure AD aktuális beállításait, ellenkező esetben a usageguidelinesurl-től eltérő meglévő beállítások felülírása.

1. Az aktuális beállítások beszerezése a Group.Unified SettingsTemplate sablonból:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Ellenőrizze az aktuális beállításokat:
   
   ```powershell
   $Setting.Values
   ```
   
   Kimenet:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. A UsageGuideLinesUrl értékének eltávolításához az URL-címet üres karakterláncként szerkesztheti:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Frissítés mentése a könyvtárba:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Sablon beállításai
Az alábbiakban a Group.Unified SettingsTemplate(Csoport.Unified SettingsTemplate) beállítássablonban meghatározott beállításokat találja. Eltérő rendelkezés hiányában ezek a funkciók azure-beli Active Directory Prémium P1 licencet igényelnek. 

| **Beállítás** | **Leírás** |
| --- | --- |
|  <ul><li>EnableGroupCreation (Engedélyezőcsoportlétrehozása)<li>Típus: Logikai<li>Alapértelmezett: Igaz |A jelző, amely azt jelzi, hogy az Office 365-csoport létrehozása engedélyezett-e a címtárban a rendszergazdai felhasználók számára. Ehhez a beállításhoz nincs szükség Azure Active Directory Prémium P1 licencre.|
|  <ul><li>GroupCreationAllowedGroupId<li>Típus: Sztring<li>Alapértelmezett: "" |Annak a biztonsági csoportnak a GUID azonosítója, amelyhez a tagok akkor is létrehozhatnak Office 365-csoportokat, ha az EnableGroupCreation == hamis. |
|  <ul><li>UsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett: "" |A csoporthasználati irányelvekre mutató hivatkozás. |
|  <ul><li>Besorolások leírása<li>Típus: Sztring<li>Alapértelmezett: "" | Az osztályozási leírások vesszővel tagolt listája. A ClassificationDescriptions értéke csak ebben a formátumban érvényes:<br>$setting["ClassificationDescriptions"] ="Osztályozás:Leírás,Osztályozás:Leírás"<br>ahol a besorolás megegyezik a ClassificationList egy bejegyzéssel.<br>Ez a beállítás nem érvényes, ha enableMIPLabels == True.|
|  <ul><li>DefaultClassification (Alapértelmezett besorolás)<li>Típus: Sztring<li>Alapértelmezett: "" | A csoport alapértelmezett besorolásaként használandó besorolás, ha nincs megadva.<br>Ez a beállítás nem érvényes, ha enableMIPLabels == True.|
|  <ul><li>ElőtagSutótagNamingRequirement<li>Típus: Sztring<li>Alapértelmezett: "" | Legfeljebb 64 karakter hosszúságú karakterlánc, amely meghatározza az Office 365-csoportokhoz konfigurált elnevezési konvenciót. További információt az [Office 365-csoportok elnevezési házirendjének kényszerítése](groups-naming-policy.md)című témakörben talál. |
| <ul><li>CustomBlockedWordsList<li>Típus: Sztring<li>Alapértelmezett: "" | Vesszővel tagolt kifejezéssorozat, amelyet a felhasználók nem használhatnak csoportnevekben vagy aliasokban. További információt az [Office 365-csoportok elnevezési házirendjének kényszerítése](groups-naming-policy.md)című témakörben talál. |
| <ul><li>EnableMSStandardBlockedWords<li>Típus: Logikai<li>Alapértelmezett: "Hamis" | Ne használja
|  <ul><li>Engedélyezésaacsoporttulajdonosnak<li>Típus: Logikai<li>Alapértelmezett: Hamis | Logikai érték, amely azt jelzi, hogy egy vendégfelhasználó lehet-e csoportok tulajdonosa. |
|  <ul><li>Hozzáféréscsoportok engedélyezésea<li>Típus: Logikai<li>Alapértelmezett: Igaz | Logikai érték, amely azt jelzi, hogy egy vendégfelhasználó hozzáférhet-e az Office 365-csoportok tartalmához.  Ehhez a beállításhoz nincs szükség Azure Active Directory Prémium P1 licencre.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Típus: Sztring<li>Alapértelmezett: "" | A vendég használati irányelveire mutató hivatkozás url-címe. |
|  <ul><li>AllowToAddVendégek<li>Típus: Logikai<li>Alapértelmezett: Igaz | Logikai érték, amely azt jelzi, hogy a címtárhoz hozzáadhat-e vendégeket. <br>Ez a beállítás felülbírálható, és csak olvashatóvá válhat, ha az *EnableMIPLabels* *értéke True,* és a vendégszabályzat a csoporthoz rendelt érzékenységi címkéhez van társítva.<br>Ha az AllowToAddGuests beállítás bérlői szinten Hamis, a csoport szinten lévő AllowToAddGuests-beállításokat a rendszer figyelmen kívül hagyja. Ha csak néhány csoport számára szeretné engedélyezni a vendéghozzáférést, be kell állítania az AllowToAddGuests értéket, hogy a bérlői szinten igaz legyen, majd szelektíven le kell tiltania az adott csoportokszámára. |
|  <ul><li>Besorolási lista<li>Típus: Sztring<li>Alapértelmezett: "" | Az Office 365 csoportokra alkalmazható érvényes besorolási értékek vesszővel tagolt listája. <br>Ez a beállítás nem érvényes, ha enableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>Típus: Logikai<li>Alapértelmezett: "Hamis" |A Microsoft 365 Compliance Centerben közzétett érzékenységi címkék alkalmazását jelző jelző az Office 365-csoportokra. További információt az [Érzékenységi címkék hozzárendelése az Office 365-csoportokhoz](groups-assign-sensitivity-labels.md)című témakörben talál. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Példa: Vendégházirend konfigurálása a címtár szintű csoportokhoz
1. Az összes beállítássablon beszerezni:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. A csoportok vendégházirendjének címtárszintű beállításához Csoport.Unified sablonra van szükség.
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Ezután hozzon létre egy új beállítási objektumot a sablon alapján:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Ezután frissítse az AllowToAddGuests beállítást
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Ezután alkalmazza a beállítást:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Az értékeket a következő használatával olvashatja el:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Olvasási beállítások a könyvtár szintjén

Ha ismeri a beolvasni kívánt beállítás nevét, az alábbi parancsmag segítségével beolvashatja az aktuális beállítási értéket. Ebben a példában egy "UsageGuidelinesUrl" nevű beállítás értékét kérünk be. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Ezek a lépések könyvtárszintű beállításokat olvasnak, amelyek a címtár összes Office-csoportjára vonatkoznak.

1. Az összes meglévő könyvtárbeállítás beolvasása:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Ez a parancsmag az összes könyvtárbeállítás listáját adja vissza:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Egy adott csoport összes beállításának beolvasása:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Egy adott könyvtárbeállítási objektum összes könyvtárbeállítási értékének olvasása a Beállítások azonosító guid azonosítójával:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Ez a parancsmag az adott csoport hoz tartozó beállításobjektum nevét és értékeit adja vissza:
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

## <a name="remove-settings-at-the-directory-level"></a>Beállítások eltávolítása a könyvtár szintjén
Ez a lépés címtárszinten eltávolítja a beállításokat, amelyek a címtár összes Office-csoportjára vonatkoznak.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Adott csoport beállításainak létrehozása

1. A "Groups.Unified.Guest" nevű beállítássablon keresése
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
2. A Groups.Unified.Guest sablonobjektum beolvasása:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Hozzon létre egy új beállításobjektumot a sablonból:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Állítsa a beállítást a kívánt értékre:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. A beállítás alkalmazásához kívánt csoport azonosítójának leállítása:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Hozza létre a címtárban a szükséges csoport új beállítását:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. A beállítások ellenőrzéséhez futtassa a következő parancsot:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Adott csoport beállításainak frissítése
1. Annak a csoportnak az azonosítóját, amelynek a beállítását frissíteni szeretné:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. A csoport beállításának beolvasása:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Frissítse a csoport beállítását, amennyire szüksége van, pl.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Ezután kapja meg az adott csoport beállításának azonosítóját:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   A következőhöz hasonló választ kap:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Ezután beállíthatja az új értéket ehhez a beállításhoz:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. A beállítás értékének elolvasásával meggyőződhet arról, hogy megfelelően frissült:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet szintaxis hivatkozás
További Azure Active Directory PowerShell-dokumentációmegtalálható az [Azure Active Directory-parancsmagokban.](/powershell/azure/install-adv2?view=azureadps-2.0)

## <a name="additional-reading"></a>További olvasás

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
