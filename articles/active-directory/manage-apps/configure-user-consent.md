---
title: Annak konfigurálása, hogy a végfelhasználók hogyan járulnak hozzá az Azure AD-t használó alkalmazásokhoz
description: Ismerje meg, hogyan kezelheti, hogyan és mikor járulhatnak hozzá a felhasználók a szervezet adataihoz hozzáféréssel rendelkező alkalmazásokhoz.
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
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519626"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Annak konfigurálása, hogy a végfelhasználók hogyan járuljanak hozzá az alkalmazásokhoz

Az alkalmazások integrálhatók a Microsoft Identity platformmal, így a felhasználók bejelentkezhetnek munkahelyi vagy iskolai fiókjukkal az Azure Active Directoryban (Azure AD), és hozzáférhetnek a szervezet adataihoz, hogy gazdag adatalapú élményt nyújtsanak. A különböző engedélyek lehetővé teszik, hogy az alkalmazás különböző szintű hozzáférést biztosítson a felhasználók és a szervezet adataihoz.

Alapértelmezés szerint a felhasználók beleegyezhetnek abba, hogy az alkalmazások hozzáférjenek a szervezet adataihoz, bár csak bizonyos engedélyekhez. Alapértelmezés szerint például a felhasználó beleegyezhet abba, hogy egy alkalmazás hozzáférjen a saját postaládájához vagy a Csapat teams-beszélgetéseihez egy olyan csapat számára, amely a felhasználó tulajdonában van, de nem járulhat hozzá ahhoz, hogy egy alkalmazás felügyelet nélküli hozzáférést biztosítson a szervezet összes SharePoint-webhelyének olvasásához és írásához. Bár lehetővé teszi a felhasználók beleegyezését önmagukban lehetővé teszi, hogy a felhasználók könnyen beszerezni hasznos alkalmazások, amelyek integrálhatók a Microsoft 365, az Azure és más szolgáltatások, ez kockázatot jelenthet, ha nem használják, és gondosan figyelt.

A Microsoft azt javasolja, hogy tiltsa le a jövőbeli felhasználói hozzájárulási műveleteket a felület csökkentése és a kockázat csökkentése érdekében. Ha a felhasználó hozzájárulása le van tiltva, a korábbi hozzájárulási támogatás továbbra is tiszteletben marad, de minden jövőbeli hozzájárulási műveletet egy rendszergazdának kell végrehajtania. Bérlői szintű rendszergazdai jóváhagyást kérhetnek a felhasználók egy integrált [rendszergazdai hozzájárulási kérelem munkafolyamat](configure-admin-consent-workflow.md) vagy a saját támogatási folyamatok. További részletekért tekintse [meg az Öt lépés a személyazonossági infrastruktúra védelméhez.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Felhasználói hozzájárulás konfigurálása az alkalmazásokhoz
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Felhasználói hozzájárulás letiltása vagy engedélyezése az Azure Portalról

Az Azure Portal segítségével letilthatja vagy engedélyezheti, hogy a felhasználók hozzájáruljanak a szervezet adataihoz hozzáférő alkalmazásokhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) [globális rendszergazdaként.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Válassza az **Azure Active Directory**, majd a Vállalati **alkalmazások**, majd a Felhasználói **beállítások lehetőséget.**
3. A felhasználói hozzájárulás engedélyezése vagy letiltása a vezérlő **címkével: A felhasználók beleegyezhetnek abba, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások hozzáférjenek.**
4. (Nem kötelező) Konfigurálja [a rendszergazdai hozzájárulási kérelem munkafolyamatát](configure-admin-consent-workflow.md) annak biztosítására, hogy azok a felhasználók, akik nem járulhatnak hozzá az alkalmazáshoz, jóváhagyást kérhetnek.

> [!TIP]
> Annak engedélyezéséhez, hogy a felhasználók rendszergazdai felülvizsgálatot kérjenek egy olyan alkalmazásról, amelyhez a felhasználó nem járulhat hozzá (például azért, mert a felhasználói hozzájárulás le van tiltva, vagy mert az alkalmazás olyan engedélyeket kér, amelyeket a felhasználó nem adhat meg), fontolja meg [a rendszergazdai hozzájárulási munkafolyamat konfigurálását.](configure-admin-consent-workflow.md)

### <a name="disable-or-enable-user-consent-using-powershell"></a>Felhasználói hozzájárulás letiltása vagy engedélyezése a PowerShell használatával

Az Azure AD PowerShell v1 modul[(MSOnline)](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)használatával engedélyezheti vagy letilthatja a felhasználók beleegyezését a szervezet adataihoz hozzáférő alkalmazásokhoz.

1. Jelentkezzen be a szervezetbe a parancsmag futtatásával:

    ```powershell
    Connect-MsolService
    ```

2. Ellenőrizze, hogy a parancsmag futtatásával engedélyezve van-e a felhasználói hozzájárulás:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. A felhasználói hozzájárulás engedélyezése vagy letiltása. A felhasználói hozzájárulás letiltásához például futtassa a parancsmast:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Csoporttulajdonosi hozzájárulás konfigurálása a csoportadatokhoz hozzáférő alkalmazásokhoz

> [!IMPORTANT]
> A következő információk egy közelgő funkció, amely lehetővé teszi a csoport tulajdonosok számára, hogy alkalmazások hozzáférést a csoportok adatait. Ha ez a funkció megjelent, alapértelmezés szerint engedélyezve lesz. Bár ez a funkció még nem jelent meg széles körben, ezekkel az utasításokkal letilthatja a funkciót a kiadás előtt.

A csoporttulajdonosok engedélyezhetik, hogy az alkalmazások (például a külső gyártók által közzétett alkalmazások) hozzáférjenek a szervezet csoporthoz társított adataihoz. Egy csapattulajdonos (aki a csapat Office 365-csoportjának tulajdonosa) engedélyezheti például, hogy egy alkalmazás elolvassa a csapat összes Teams-üzenetét, vagy felsorolja a csoport tagjainak alapvető profilját.

> [!NOTE]
> Ettől a beállítástól függetlenül a csoport tulajdonosa mindig hozzáadhat más felhasználókat vagy alkalmazásokat közvetlenül csoporttulajdonosként.

### <a name="configure-group-owner-consent-using-powershell"></a>Csoporttulajdonosi hozzájárulás konfigurálása a PowerShell használatával

Az Azure AD PowerShell előzetes modul[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)használatával engedélyezheti vagy letilthatja a csoporttulajdonosok azon képességét, hogy hozzájáruljanak ahhoz, hogy a szervezet adataihoz hozzáférő alkalmazások a saját csoportjaikhoz hozzáférjenek.

1. Győződjön meg arról, hogy az [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult használja (ez a lépés akkor fontos, ha telepítette az [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) modult és az [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult is).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Csatlakozzon az Azure AD PowerShellhez.

   ```powershell
   Connect-AzureAD
   ```

3. A *hozzájárulási házirend beállításai* könyvtárbeállításainak aktuális lekérése a bérlőben. Ehhez ellenőrizni kell, hogy a szolgáltatás könyvtárbeállításai létrejöttek-e, és ha nem, akkor a megfelelő könyvtárbeállítási sablon értékeit kell használni.

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

4. Ismerje meg a beállítási értékeket. Két beállítási érték határozza meg, hogy mely felhasználók engedélyezhetik az alkalmazások számára a csoport adataihoz való hozzáférést:

    | Beállítás       | Típus         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logikai |  Jelző, amely azt jelzi, hogy a csoportok tulajdonosai adhatnak-e csoportspecifikus engedélyeket. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Ha _az EnableGroupSpecificConsent_ értéke "True", és ez az érték egy csoport objektumazonosítójára van állítva, akkor az azonosított csoport tagjai jogosultak csoportspecifikus engedélyeket adni a saját csoportjuknak. |

5. A kívánt konfiguráció beállítási értékeinek frissítése:

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

## <a name="configure-risk-based-step-up-consent"></a>Kockázatalapú step-up hozzájárulás konfigurálása

A kockázatalapú step-up hozzájárulás segít csökkenteni a felhasználók kitettségét a rosszindulatú alkalmazások illegális [hozzájárulási kérelmeket.](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Ha a Microsoft kockázatos végfelhasználói hozzájárulási kérelmet észlel, a kérelemhez "step-up" szükséges a rendszergazdai hozzájáruláshoz. Ez a funkció alapértelmezés szerint engedélyezve van, de csak akkor eredményez viselkedésváltozást, ha a végfelhasználói jóváhagyás engedélyezve van.

Kockázatos hozzájárulási kérelem észlelésekor a hozzájárulási üzenet egy üzenetet jelenít meg, amely jelzi, hogy rendszergazdai jóváhagyásra van szükség. Ha a [rendszergazdai hozzájárulási kérelem munkafolyamata](configure-admin-consent-workflow.md) engedélyezve van, a felhasználó elküldheti a kérelmet egy rendszergazdának további ellenőrzésre közvetlenül a hozzájárulási kérelemből. Ha nincs engedélyezve, a következő üzenet jelenik meg:

* **AADSTS90094:** &lt;ClientAppDisplayName&gt; engedélyre van szüksége, hogy hozzáférjen a szervezet erőforrásaihoz, hogy csak egy rendszergazda adhat. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ebben az esetben egy naplózási esemény is naplózza a kategória "ApplicationManagement", tevékenység típusa "Hozzájárulás az alkalmazáshoz" és állapot oka a "Kockázatos alkalmazás észlelt".

> [!IMPORTANT]
> A rendszergazdáknak a jóváhagyás előtt gondosan ki kell [értékelniük az összes hozzájárulási kérelmet,](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) különösen akkor, ha a Microsoft kockázatot észlelt.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Kockázatalapú step-up hozzájárulás letiltása vagy újbóli engedélyezése a PowerShell használatával

Az Azure AD PowerShell előzetes modul[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)használatával letilthatja a rendszergazdai hozzájáruláshoz szükséges előrelépést azokban az esetekben, amikor a Microsoft kockázatot észlel, vagy újra engedélyezheti, ha korábban le volt tiltva.

Ez a fentiekben bemutatott lépésekkel végezhető el a [csoporttulajdonos identikéna PowerShell használatával történő konfigurálásához,](#configure-group-owner-consent-using-powershell)de egy másik beállítási érték helyettesítéséhez. Három különbség van a lépésekben: 

1. Ismerje meg a kockázatalapú step-up hozzájárulás beállítási értékeit:

    | Beállítás       | Típus         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logikai |  Jelző, amely jelzi, ha a felhasználó hozzájárulása le lesz tiltva, ha kockázatos kérést észlel. |

2. A 3.

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. Az 5.

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>További lépések

[A rendszergazdai hozzájárulási munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[További információ az alkalmazásokhoz való hozzájárulás kezeléséről és a hozzájárulási kérelmek kiértékeléséről](manage-consent-requests.md)

[Bérlői rendszergazdai hozzájárulás megadása egy alkalmazáshoz](grant-admin-consent.md)

[Engedélyek és hozzájárulás a Microsoft identitásplatformján](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)
