---
title: Tokenek élettartamának beállítása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be a Microsoft Identity platform által kiállított jogkivonatok élettartamát. Ismerje meg, hogyan kezelheti a szervezet alapértelmezett házirendjét, hogyan hozhat létre webes bejelentkezésre vonatkozó házirendet, hogyan hozhat létre házirendet a webes API-t meghívó natív alkalmazásokhoz, és hogyan kezelheti a speciális házirendeket.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f70a11165f6433e580fd857f2d5a620deb6640c0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604275"
---
# <a name="configure-token-lifetime-policies-preview"></a>Jogkivonat élettartamára vonatkozó szabályzatok konfigurálása (előzetes verzió)
Az Azure AD-ben számos forgatókönyv lehetséges, ha az alkalmazásokhoz, az egyszerű szolgáltatásokhoz és a teljes szervezethez token-élettartamot hozhat létre és kezelhet. További információért olvassa el [a konfigurálható jogkivonat élettartamait a Microsoft Identity platformon](active-directory-configurable-token-lifetimes.md). 

> [!IMPORTANT]
> Az előzetes verzióban az ügyfelek meghallgatása után az Azure AD feltételes hozzáférés szolgáltatásban implementálta a [hitelesítési munkamenet-kezelési képességeket](../conditional-access/howto-conditional-access-session-lifetime.md) . Ezt az új funkciót használhatja a frissítési jogkivonat élettartamának konfigurálásához a bejelentkezési gyakoriság beállításával. 2020. május 30-ig az új bérlők nem használhatnak konfigurálható jogkivonat-élettartamot a munkamenet-és frissítési tokenek konfigurálásához. Az elavultság több hónapon belül megtörténik, ami azt jelenti, hogy a meglévő munkamenetek tiszteletben tartását és a tokenek frissítési jogkivonatait is megszüntetjük. A hozzáférési token élettartamát továbbra is beállíthatja az elavulás után.


Ebben a szakaszban néhány olyan általános házirend-forgatókönyvet ismertetünk, amely segíthet a következő új szabályok bevezetésében:

* Jogkivonat élettartama
* Token maximális inaktív ideje
* Token maximális kora

A példákban megtudhatja, hogyan végezheti el a következőket:

* A szervezet alapértelmezett házirendjének kezelése
* Házirend létrehozása webes bejelentkezéshez
* Szabályzat létrehozása egy webes API-t meghívó natív alkalmazáshoz
* Speciális szabályzat kezelése

## <a name="prerequisites"></a>Előfeltételek
Az alábbi példákban létrehozhat, frissíthet, csatolhat és törölhet szabályzatokat az alkalmazásokhoz, az egyszerű szolgáltatásokhoz és a teljes szervezethez. Ha még nem ismeri az Azure AD-t, javasoljuk, hogy Ismerje meg, [hogyan szerezhet be Azure ad-bérlőt](quickstart-create-new-tenant.md) , mielőtt folytatja ezeket a példákat.  

A kezdéshez hajtsa végre a következő lépéseket:

1. Töltse le a legújabb [Azure ad PowerShell-modul nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview).
2. A parancs futtatásával `Connect` Jelentkezzen be az Azure ad-rendszergazdai fiókjába. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Futtassa ezt a parancsot a legtöbb művelet után a következő esetekben. A parancs futtatása a szabályzatok * * * * beszerzését is segíti.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>A szervezet alapértelmezett házirendjének kezelése
Ebben a példában olyan házirendet hoz létre, amely lehetővé teszi, hogy a felhasználók a teljes szervezeten belül ritkábban jelentkezzenek be. Ehhez hozzon létre egy jogkivonat-élettartam-szabályzatot az egytényezős frissítési tokenekhez, amelyet a rendszer a szervezeten belül alkalmaz. A szabályzatot a szervezet minden alkalmazására, valamint minden olyan egyszerű szolgáltatásnév alkalmazza, amely még nem rendelkezik házirend-beállítással.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Állítsa az egytényezős frissítési tokent a "visszavonás visszavonása" értékre. A jogkivonat nem jár le, amíg vissza nem vonja a hozzáférést. Hozza létre a következő szabályzat-definíciót:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. A szabályzat létrehozásához futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. A szóköz eltávolításához futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId**beszerzéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Frissítse a szabályzatot.

    Dönthet úgy is, hogy az ebben a példában megadott első szabályzat nem annyira szigorú, mint a szolgáltatás. Ha úgy szeretné beállítani az egytényezős frissítési tokent, hogy két nap múlva lejárjon, futtassa a következő parancsot:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Házirend létrehozása webes bejelentkezéshez

Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban. Ezzel a szabályzattal állítható be a hozzáférési/azonosító tokenek élettartama, valamint a többtényezős munkamenet-tokenek maximális kora a webalkalmazás egyszerű szolgáltatásnév számára.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    Ez a házirend a webes bejelentkezéshez megadja a hozzáférési/azonosító jogkivonat élettartamát és az egytényezős munkamenet-token maximális életkorát két óráig.

    1. A szabályzat létrehozásához futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId**lekéréséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév **ObjectId** is le kell kérnie.

    1. A [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmaggal tekintheti meg az összes szervezet egyszerű szolgáltatását vagy egy egyszerű szolgáltatásnevet.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa az [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Szabályzat létrehozása egy webes API-t meghívó natív alkalmazáshoz
Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználóknak ritkábban kell hitelesíteniük magukat. A szabályzat emellett meghosszabbítja azt az időtartamot is, ameddig a felhasználó inaktív lehet, mielőtt a felhasználónak újra hitelesítenie kell magát. A szabályzatot a rendszer a webes API-ra alkalmazza. Ha a natív alkalmazás erőforrásként kéri a webes API-t, a rendszer alkalmazza ezt a házirendet.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Ha szigorú szabályzatot szeretne létrehozni egy webes API-hoz, futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot a webes API-hoz. Emellett le kell kérnie az alkalmazás **ObjectId** is. Használja a [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) parancsmagot az alkalmazás **ObjectId**megkereséséhez, vagy használja a [Azure Portal](https://portal.azure.com/).

    Szerezze be az alkalmazás **ObjectId** , és rendelje hozzá a szabályzatot:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>Speciális szabályzat kezelése
Ebben a példában néhány szabályzatot hoz létre a prioritási rendszer működésének megismeréséhez. Azt is megtudhatja, hogyan kezelhet több objektumra alkalmazott házirendeket.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Ha a szervezet alapértelmezett házirendjét szeretné létrehozni, amely az egytényezős frissítési token élettartamát 30 napra állítja be, futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megjelenítéséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot egy egyszerű szolgáltatáshoz.

    Most már rendelkezik egy szabályzattal, amely a teljes szervezetre vonatkozik. Előfordulhat, hogy meg szeretné őrizni ezt a 30 napos házirendet egy adott egyszerű szolgáltatásnév esetében, de a szervezet alapértelmezett házirendjét a "visszavonás visszavonása" felső korlátra kell módosítania.

    1. A szervezet összes szolgáltatásának megtekintéséhez használja a [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmagot.

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa az [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. A jelző beállítása false (hamis) értékre `IsOrganizationDefault` :

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Új szervezet alapértelmezett házirendjének létrehozása:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már rendelkezik a szolgáltatáshoz tartozó eredeti házirenddel, és az új szabályzat beállítása a szervezet alapértelmezett házirendje. Fontos megjegyezni, hogy az egyszerű szolgáltatásokra alkalmazott szabályzatok elsőbbséget élveznek a szervezet alapértelmezett házirendjeivel szemben.

## <a name="next-steps"></a>További lépések
Ismerje meg az Azure AD feltételes hozzáférésének [hitelesítési munkamenet-kezelési képességeit](../conditional-access/howto-conditional-access-session-lifetime.md) .