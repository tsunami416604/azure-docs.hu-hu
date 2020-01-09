---
title: Annak konfigurálása, hogy a végfelhasználók miként egyeznek az Azure AD-vel az alkalmazásokkal
description: Megtudhatja, hogyan és mikor férhet hozzá a felhasználók a szervezet adataihoz hozzáférő alkalmazásokhoz.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443390"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Annak konfigurálása, hogy a végfelhasználók hogyan hozzájárulásukat az alkalmazásokhoz

Az alkalmazások integrálása a Microsoft Identity platformmal lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a Azure Active Directory (Azure AD) munkahelyi vagy iskolai fiókjával, és a szervezet adataihoz hozzáférjenek, hogy gazdag adatvezérelt felhasználói élményt nyújtsanak. A különböző engedélyek lehetővé teszik, hogy az alkalmazás különböző szintű hozzáférést biztosítson a felhasználók és a szervezete adataihoz.

Alapértelmezés szerint a felhasználók beleegyeznek a szervezet adataihoz hozzáférő alkalmazásokhoz, de csak bizonyos engedélyekhez. Alapértelmezés szerint például egy felhasználó hozzájárulhat ahhoz, hogy egy alkalmazás hozzáférhessen a saját postaládájához, vagy a csapatnak a felhasználó tulajdonában lévő csapatával, de nem tudja beleegyezni, hogy a szervezet összes SharePoint-webhelye számára lehetővé teszi az alkalmazás felügyelet nélküli elérését. A felhasználók saját maguk is engedélyezhetik, hogy a felhasználók egyszerűen beszerezzék a Microsoft 365-, Azure-és egyéb szolgáltatásokkal integrálható hasznos alkalmazásokat, és ez kockázatot jelenthet, ha nem használatos és nem figyeli őket körültekintően.

A Microsoft javasolja a jövőbeli felhasználói engedélyezési műveletek letiltását a felület csökkentése és a kockázat enyhítése érdekében. Ha a felhasználói hozzájárulás le van tiltva, a korábbi hozzájárulási támogatások továbbra is érvényben maradnak, de az összes jövőbeli hozzájárulási műveletet egy rendszergazdának kell elvégeznie. A felhasználók teljes körű rendszergazdai jogosultságot kérhetnek a felhasználóknak egy integrált [rendszergazdai engedélyezési kérelem-munkafolyamattal](configure-admin-consent-workflow.md) vagy a saját támogatási folyamataival. További részletekért tekintse [meg az identitás-infrastruktúra biztonságossá tételének öt lépését](../../security/fundamentals/steps-secure-identity.md) .

## <a name="configure-user-consent-to-applications"></a>Felhasználói beleegyező alkalmazások konfigurálása
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Felhasználói engedély letiltása vagy engedélyezése a Azure Portal

A Azure Portal segítségével letilthatja vagy engedélyezheti a felhasználók számára, hogy hozzáférjenek a szervezet adataihoz hozzáférő alkalmazásokhoz:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Válassza a **Azure Active Directory**, majd a **vállalati alkalmazások**, majd a **felhasználói beállítások**lehetőséget.
3. Engedélyezheti vagy letilthatja a felhasználói belekötést a **felhasználók nevében, ha a felhasználó beleegyezik abba, hogy az alkalmazások hozzáférjenek a vállalati adatokhoz**.
4. Választható Adja meg a [rendszergazdai hozzájárulási kérelem munkafolyamatát](configure-admin-consent-workflow.md) annak biztosítása érdekében, hogy az alkalmazásnak nem engedélyezett felhasználók jóváhagyást kérjenek.

> [!TIP]
> Annak lehetővé tétele érdekében, hogy a felhasználók egy olyan alkalmazásra vonatkozó rendszergazdai felülvizsgálatot kérjenek, amelynek a felhasználó számára nem engedélyezett a jóváhagyása (például azért, mert a felhasználói belefoglalást letiltották, vagy mert az alkalmazás olyan engedélyeket kér, amelyeknek a felhasználó nem engedélyezte a hozzáférést), érdemes [konfigurálni a rendszergazdai belefoglalási munkafolyamatot](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Felhasználói engedély letiltása vagy engedélyezése a PowerShell használatával

Az Azure AD PowerShell v1 modult ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) használva engedélyezheti vagy letilthatja a felhasználók számára, hogy hozzáférjenek a szervezet adataihoz hozzáférő alkalmazásokhoz.

1. Jelentkezzen be a szervezetbe a következő parancsmag futtatásával:

    ```powershell
    Connect-MsolService
    ```

2. Ellenőrizze, hogy engedélyezve van-e a felhasználói engedély a következő parancsmag futtatásával:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Felhasználói engedély engedélyezése vagy letiltása. Ha például le szeretné tiltani a felhasználói engedélyt, futtassa a következő parancsmagot:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>A csoportra vonatkozó adatokhoz hozzáférő alkalmazások csoportbeli tulajdonosi engedélyének konfigurálása

> [!IMPORTANT]
> A következő információk egy közelgő funkcióra vonatkoznak, amely lehetővé teszi a csoport tulajdonosai számára, hogy hozzáférést biztosítanak az alkalmazások számára a csoportjuk adataihoz. Ha ez a funkció megjelent, alapértelmezés szerint engedélyezve lesz. Bár ez a funkció még nem jelent meg széles körben, ezekkel az utasításokkal letilthatja a funkciót a kiadás előtt.

A csoport tulajdonosai engedélyezhetik az alkalmazások (például a harmadik féltől származó gyártók által közzétett alkalmazások) számára, hogy hozzáférjenek a szervezethez a csoporthoz társított adataihoz. Például a csapat tulajdonosa (aki a csapat Office 365-csoportjának tulajdonosa) lehetővé teszi az alkalmazás számára, hogy beolvassa az összes csapat üzenetet a csapatban, vagy listázza a csoport tagjainak alapszintű profilját.

> [!NOTE]
> Ettől a beállítástól függetlenül a csoport tulajdonosának mindig lehetősége van más felhasználók vagy alkalmazások közvetlen hozzáadására csoport tulajdonosaként.

### <a name="configure-group-owner-consent-using-powershell"></a>Csoport tulajdonosi engedélyének konfigurálása a PowerShell használatával

Az Azure AD PowerShell előzetes verziójának ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) használatával engedélyezheti vagy letilthatja a csoport tulajdonosai számára, hogy a szervezet adataihoz hozzáférjenek a saját csoportok számára.

1. Győződjön meg arról, hogy a [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult használja (ez a lépés akkor fontos, ha a [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) modult és a [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult is telepítette).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Kapcsolódjon az Azure AD PowerShell-hez.

   ```powershell
   Connect-AzureAD
   ```

3. Az aktuális érték beolvasása a bérlőben a *beleegyező házirend-beállítások* címtár-beállításainál. Ehhez ellenőrizni kell, hogy a szolgáltatáshoz tartozó címtár-beállítások létre lettek-e hozva, és ha nem, akkor a megfelelő címtár-beállítási sablon értékeit használja.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. A beállítás értékeinek megismerése. Két beállítási érték határozza meg, hogy mely felhasználók számára engedélyezhető, hogy egy alkalmazás hozzáférhessen a csoport adatait:

    | Beállítás       | Type (Típus)         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logikai |  Jelző, amely azt jelzi, hogy a csoportok tulajdonosai jogosultak-e a csoportra vonatkozó engedélyek megadására. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Ha a _EnableGroupSpecificConsent_ értéke "true" (igaz), és ez az érték egy csoport objektumazonosító, az azonosított csoport tagjai jogosultak a csoportokra vonatkozó engedélyek megadására a saját csoportok számára. |

5. Módosítsa a kívánt konfiguráció beállításait:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Beállítások mentése.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Következő lépések

[Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[Bérlői szintű rendszergazdai jóváhagyás engedélyezése egy alkalmazás számára](grant-admin-consent.md)

[Engedélyek és beleegyezett a Microsoft Identity platform](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
