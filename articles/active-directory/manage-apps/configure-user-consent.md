---
title: Annak konfigurálása, hogy a végfelhasználók miként egyeznek az Azure AD-vel az alkalmazásokkal
description: Megtudhatja, hogyan és mikor férhet hozzá a felhasználók a szervezet adataihoz hozzáférő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763465"
---
# <a name="configure-how-end-users-consent-to-applications"></a>A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása

Az alkalmazásait a Microsoft Identity platformmal integrálva engedélyezheti a felhasználók számára, hogy munkahelyi vagy iskolai fiókjával jelentkezzenek be, és hozzáférjenek a szervezet adataihoz, és így gazdag adatvezérelt felhasználói élményt nyújtsanak.

Ahhoz, hogy egy alkalmazás hozzáférhessen a szervezet adataihoz, a felhasználónak meg kell adnia az alkalmazás engedélyeit. A különböző engedélyek különböző hozzáférési szinteket tesznek lehetővé. Alapértelmezés szerint az összes felhasználó beleegyezik abba, hogy a rendszergazdai jogosultságokat nem igénylő engedélyekkel rendelkező alkalmazások is beleegyeznek. Alapértelmezés szerint például egy felhasználó hozzájárulhat ahhoz, hogy egy alkalmazás hozzáférjen a postaládához, de nem tud beleegyezni, hogy az alkalmazás korlátlanul hozzáférhessen a szervezet összes fájljának olvasásához és írásához.

Azáltal, hogy lehetővé teszi, hogy a felhasználók alkalmazásokat adjanak az adathozzáféréshez, a felhasználók könnyen vásárolhatnak hasznos alkalmazásokat, és hatékonyan dolgozhatnak. Bizonyos helyzetekben azonban ez a konfiguráció akkor is jelenthet kockázatot, ha a rendszer nem figyeli és nem ellenőrzi körültekintően.

## <a name="user-consent-settings"></a>Felhasználói beleegyező beállítások

Annak szabályozásához, hogy a felhasználók milyen esetekben engedélyezhetik az alkalmazásokhoz való hozzájárulásukat, válassza ki az összes felhasználóra érvényes beleegyező szabályzatot. Az alábbi három engedélyezési házirend-lehetőség közül választhat:

* **Felhasználói jóváhagyás letiltása** – a felhasználók nem adhatnak engedélyeket az alkalmazásoknak. A felhasználók továbbra is bejelentkezhetnek azokba az alkalmazásokba, amelyekre korábban beleegyezett, vagy amelyeket a rendszergazdák jóváhagytak a nevükben, de nem jogosultak új engedélyekre vagy új alkalmazásokra. Csak azok a felhasználók férhetnek hozzá az új engedélyekhez vagy új alkalmazásokhoz, akik engedélyt kaptak a hozzájárulás engedélyezésére.

* A felhasználók beleegyeznek **az ellenőrzött közzétevők alkalmazásaiba, de csak a kiválasztott engedélyekhez (előzetes verzió)** – az összes felhasználó csak a [hitelesített közzétevő](../develop/publisher-verification-overview.md) és a bérlőben regisztrált alkalmazások által közzétett alkalmazások számára engedélyezheti a jóváhagyást. A felhasználók csak az "alacsony hatású" besorolású engedélyekkel rendelkezhetnek.

  Győződjön meg arról, hogy az [engedélyek besorolásával](#configure-permission-classifications-preview) kiválaszthatja, hogy a felhasználók milyen engedélyeket adhatnak hozzá.

* A felhasználók beleegyeznek **az összes** alkalmazásba – ez a beállítás lehetővé teszi, hogy minden felhasználó beleegyezést kérjen bármely alkalmazáshoz a rendszergazdai hozzájárulást nem igénylő engedélyekhez. 

   Annak érdekében, hogy csökkentse a felhasználókat a szervezet adataihoz való hozzáférést biztosító rosszindulatú alkalmazások kockázatát, javasoljuk, hogy csak a [hitelesített közzétevő](../develop/publisher-verification-overview.md)által közzétett alkalmazásokhoz engedélyezze a felhasználói jóváhagyást.

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Felhasználói beleegyező beállítások konfigurálása a Azure Portal

A felhasználói beleegyező beállítások konfigurálása a Azure Portal használatával:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **beleegyezett és engedélyek**  >  **felhasználói beleegyező beállításait**.
1. **Az alkalmazások felhasználói beleegyezike**területen válassza ki, hogy az összes felhasználóra vonatkozóan melyik beleegyező beállítást szeretné konfigurálni.
1. A beállítások mentéséhez kattintson a **Mentés** gombra.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Felhasználói beleegyező beállítások":::

> [!TIP]
> Érdemes lehet [engedélyezni a rendszergazdai hozzájárulási munkafolyamatot](configure-admin-consent-workflow.md) , amely lehetővé teszi, hogy a felhasználók kérjenek egy olyan alkalmazás rendszergazdájának felülvizsgálatát és jóváhagyását, amelyet a felhasználó nem jogosult beleegyezésre – például ha a felhasználó beleegyezését letiltották, vagy ha egy alkalmazás olyan engedélyeket kér, amelyet a felhasználó nem engedélyez.

### <a name="configure-user-consent-settings-using-powershell"></a>Felhasználói beleegyező beállítások konfigurálása a PowerShell használatával

A legújabb Azure AD PowerShell előzetes [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)használatával kiválaszthatja, hogy melyik engedélyezési szabályzat szabályozza az alkalmazások felhasználói belekötését.

* **Felhasználói** beleegyezikés letiltása – a felhasználói engedély letiltásához állítsa be a felhasználói beleegyezikés megadására vonatkozó engedélyezési házirendeket:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* Az **ellenőrzött közzétevőtől származó alkalmazások felhasználói jóváhagyásának engedélyezése (előzetes verzió)** – a korlátozott felhasználói jóváhagyás engedélyezése csak az ellenőrzött közzétevők és a bérlőben regisztrált alkalmazások alkalmazásai számára, és csak az "alacsony hatásnak" minősülő engedélyek esetében, a beépített beleegyező házirend konfigurálása a következő néven `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Ne felejtse el [besorolni az engedélyeket](#configure-permission-classifications-preview) annak kiválasztásához, hogy a felhasználók milyen engedélyeket vehetnek igénybe.

* Az **összes alkalmazás felhasználói beleegyezikének engedélyezése** – a felhasználók beleegyezett az összes alkalmazásba:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Ez a beállítás lehetővé teszi, hogy minden felhasználó beleegyezést kérjen bármely alkalmazáshoz a rendszergazdai hozzájárulást nem igénylő engedélyekhez. Javasoljuk, hogy csak az ellenőrzött közzétevők alkalmazásai számára engedélyezze a felhasználói jóváhagyást.

## <a name="configure-permission-classifications-preview"></a>Engedélyek besorolásának konfigurálása (előzetes verzió)

Az engedélyek besorolása lehetővé teszi annak a meghatározását, hogy a különböző engedélyek milyen hatással vannak a szervezet szabályzatai és a kockázatértékelések alapján. Az engedélyezési házirendekben szereplő engedélyek besorolásával például azonosíthatja azokat az engedélyeket, amelyeket a felhasználók jóváhagynak.

> [!NOTE]
> Jelenleg csak az "alacsony hatású" engedélyek besorolása támogatott. Csak a rendszergazdai jogosultságokat nem igénylő delegált engedélyek besorolása "alacsony hatás" lehet.

### <a name="classify-permissions-using-the-azure-portal"></a>Engedélyek osztályozása a Azure Portal használatával

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **hozzájárulása és engedélyek**  >  **besorolása**lehetőséget.
1. Válassza az **engedélyek hozzáadása** lehetőséget, hogy egy másik engedélyt "alacsony hatásként" minősítse. 
1. Válassza ki az API-t, majd válassza ki a delegált engedélyeket (ka) t.

Ebben a példában az egyszeri bejelentkezéshez minimálisan szükséges engedélyt soroltuk be:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Engedélyek besorolása":::

> [!TIP]
> A Microsoft Graph API esetében az alapszintű egyszeri bejelentkezéshez szükséges minimális engedélyek a következők:, `openid` `profile` `User.Read` és `offline_access` . Ezekkel az engedélyekkel az alkalmazás beolvashatja a bejelentkezett felhasználó profiljának részleteit, és akkor is karbantarthatja ezt a hozzáférést, ha a felhasználó már nem használja az alkalmazást.

### <a name="classify-permissions-using-powershell"></a>Engedélyek osztályozása a PowerShell használatával

Az engedélyek besorolásához használhatja a legújabb Azure AD PowerShell előzetes [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)-modult is. Az engedélyek besorolása az API **ServicePrincipal** objektumán van konfigurálva, amely közzéteszi az engedélyeket.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Egy API aktuális engedélyezési besorolásának beolvasása:

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Olvassa el az API delegált jogosultsági besorolását:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Az engedélyek osztályozása "kis hatásként":

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg a minősíteni kívánt delegált engedélyt:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Állítsa be az engedélyek besorolását az engedély neve és azonosítója alapján:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Delegált engedélyek besorolásának eltávolítása:

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg az eltávolítani kívánt delegált engedélyek besorolását:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Az engedélyek besorolásának törlése:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>A csoportra vonatkozó adatokhoz hozzáférő alkalmazások csoportbeli tulajdonosi engedélyének konfigurálása

A csoport tulajdonosai engedélyezhetik az alkalmazások, például a külső gyártók által közzétett alkalmazások számára, hogy hozzáférjenek a szervezethez a csoporthoz társított adataihoz. A Microsoft Teams csapatának tulajdonosa például lehetővé teheti az alkalmazások számára, hogy beolvassák a csapat összes csapatának üzenetét, vagy felsorolják a csoport tagjainak alapszintű profilját.

Beállíthatja, hogy mely felhasználók férjenek hozzá az alkalmazásokhoz a csoportok adatokhoz való hozzáféréshez, vagy le is tilthatja ezt a funkciót.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Csoport tulajdonosi engedélyének konfigurálása a Azure Portal használatával

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **beleegyezett és engedélyek**  >  **felhasználói beleegyező beállításait**.
3. Az **adatokhoz hozzáférő alkalmazások csoport tulajdonosi engedélye** területen válassza ki az engedélyezni kívánt beállítást.
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

Ebben a példában minden csoport tulajdonosa jogosult a csoportok adatokhoz hozzáférő alkalmazásokhoz való hozzáférésre:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Csoport tulajdonosának engedélyezési beállításai":::

### <a name="configure-group-owner-consent-using-powershell"></a>Csoport tulajdonosi engedélyének konfigurálása a PowerShell használatával

A [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)Azure ad PowerShell-előnézeti moduljának használatával engedélyezheti vagy letilthatja a csoport tulajdonosai számára, hogy a szervezet adataihoz hozzáférjenek a saját csoportok számára.

1. Győződjön meg arról, hogy a [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult használja. Ez a lépés akkor fontos, ha a [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) modult és a [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) modult is telepítette.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Kapcsolódjon az Azure AD PowerShell-hez.

   ```powershell
   Connect-AzureAD
   ```

1. Az aktuális érték beolvasása a bérlőben a **beleegyező házirend-beállítások** címtár-beállításainál. Ehhez ellenőrizni kell, hogy a szolgáltatáshoz tartozó címtár-beállítások létre lettek-e hozva, és ha nem, akkor a megfelelő címtár-beállítási sablon értékeit használja.

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

1. A beállítás értékeinek megismerése. Két beállítási érték határozza meg, hogy mely felhasználók számára engedélyezhető, hogy egy alkalmazás hozzáférhessen a csoport adatait:

    | Beállítás       | Típus         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logikai | Jelző, amely azt jelzi, hogy a csoportok tulajdonosai jogosultak-e a csoportra vonatkozó engedélyek megadására. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Ha a _EnableGroupSpecificConsent_ értéke "true" (igaz), és ez az érték egy csoport objektumazonosító, akkor az azonosított csoport tagjai jogosultak a csoportokra vonatkozó engedélyek megadására a saját maguknak. |

1. Módosítsa a kívánt konfiguráció beállításait:

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

1. Mentse a beállításokat.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Kockázatalapú lépésekre vonatkozó engedély konfigurálása

A kockázatalapú lépésekre [vonatkozó](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)beleegyezikés segít csökkenteni a felhasználókat olyan kártékony alkalmazásokkal szemben, amelyek nem tesznek lehetővé jogcímeket. Ha a Microsoft egy kockázatos végfelhasználói kérést észlel, a kérelemhez "Step-up" értékre van szükség a rendszergazdai beleegyező művelethez. Ez a funkció alapértelmezés szerint engedélyezve van, de csak akkor eredményezi a viselkedést, ha engedélyezve van a végfelhasználói engedély.

Kockázatos beleegyezési kérelem észlelésekor a beleegyezés kérése üzenet jelenik meg, amely jelzi, hogy a rendszergazda jóváhagyása szükséges. Ha a [rendszergazdai hozzájárulási kérelem munkafolyamata](configure-admin-consent-workflow.md) engedélyezve van, a felhasználó a kérést egy rendszergazdának küldheti el további áttekintés céljából közvetlenül a hozzájárulási kérésből. Ha nincs engedélyezve, a következő üzenet jelenik meg:

* **AADSTS90094:** &lt; &gt;a clientAppDisplayName engedélyre van szüksége a szervezet erőforrásaihoz való hozzáféréshez, csak a rendszergazda adhat meg. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ebben az esetben a naplózási esemény a "ApplicationManagement" kategóriába kerül, az "alkalmazás beleegyezése", a "kockázatos alkalmazás észlelése" állapot oka is.

> [!IMPORTANT]
> A rendszergazdáknak a kérelmek jóváhagyása előtt körültekintően ki kell [értékelniük az összes jóváhagyási kérést](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) , különösen akkor, ha a Microsoft kockázatot észlelt.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Kockázatalapú lépésenkénti belefoglalási engedély letiltása vagy újbóli engedélyezése a PowerShell használatával

A [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)Azure ad PowerShell-előnézeti modullal letilthatja a rendszergazdai belefoglaláshoz szükséges lépéseket olyan esetekben, amikor a Microsoft észleli a kockázatokat, vagy ha korábban letiltották, újra engedélyezi.

Ezt a fentiekben látható lépések végrehajtásával is megteheti a [csoport tulajdonosi engedélyének a PowerShell használatával történő konfigurálásához](#configure-group-owner-consent-using-powershell), de más beállítások értékét is behelyettesítheti. A lépések három különbséggel rendelkeznek: 

1. A kockázati alapú lépésekre vonatkozó beleegyezés értékeinek ismertetése:

    | Beállítás       | Típus         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logikai |  Jelző, amely azt jelzi, hogy a felhasználó beleegyezik-e a kockázatos kérelem észlelésekor. |

1. Helyettesítse be a következő értéket a 3. lépésben:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Helyettesítse be a következők egyikét az 5. lépésben:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>További lépések

További tudnivalók:

* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [Megtudhatja, hogyan kezelheti az alkalmazásokra vonatkozó beleegyezett, és hogyan értékelheti a hozzájárulásukat](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/active-directory-v2-scopes.md)

Segítség kérése vagy válaszok keresése a kérdéseire:
* [Azure AD a StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
