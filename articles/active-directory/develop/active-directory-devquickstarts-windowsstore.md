---
title: "Az Azure AD Windows Universal Platform (UWP/XAML) bevezetés |} Microsoft Docs"
description: "Build Windows Áruházbeli alkalmazások, amelyek a bejelentkezés az Azure AD integrálása, meghívják a Azure AD API-k OAuth protokollt használó védett."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bc8c3a897363da2a8ebe7ac6bd8798c8e22ba04
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-windows-universal-platform-uwpxaml-getting-started"></a>Az Azure AD Windows Universal Platform (UWP/XAML) első lépések
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Windows áruház 8.1 és korábbi verziójú projektek nem támogatottak a Visual Studio 2017.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Ha az alkalmazások a Windows áruházhoz, Azure Active Directory (Azure AD) segítségével egyszerű és magától értetődő, a felhasználókat az Active Directory-fiókok hitelesítésére. Integrálja az Azure AD-val, az alkalmazások biztonságos felhasználhat a webes API-t az Azure AD, például az Office 365 API-k vagy az Azure API által védett.

Windows áruház asztali alkalmazások esetén, amely a védett erőforrások eléréséhez szükséges az Azure AD az Active Directory Authentication Library (ADAL) biztosít. ADAL kizárólagos célja az alkalmazásnak, hogy a hozzáférési jogkivonatok lekérésére, könnyen. Annak bemutatásához, hogy milyen egyszerűen, ez a cikk bemutatja, hogyan hozhat létre a DirectorySearcher Windows Áruházbeli alkalmazások, amelyek:

* Lekérdezi az Azure AD Graph API felület meghívásakor használatával jogkivonatainak hozzáférni a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Megkeresi a felhasználók számára egy megadott egyszerű felhasználónév (UPN).
* Kimenő felhasználók jeleket.

## <a name="before-you-get-started"></a>A kezdés előtt
* Töltse le a [projekt vázát](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip), vagy töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Minden letöltés egy olyan Visual Studio 2015 megoldás.
* Akkor is, amelyben a felhasználók létrehozásához, és regisztrálja az alkalmazást az Azure AD-bérlő kell. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

Ha készen áll, hajtsa végre a következő három szakasz.

## <a name="step-1-register-the-directorysearcher-app"></a>1. lépés: A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy az alkalmazás a jogkivonatok lekérésére, először regisztrálja az Azure AD-bérlőben, és adja meg azt az Azure AD Graph API hozzáférési engedélyt. Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Ekkor a a **Directory** listára, válassza ki az Active Directory-bérlőt, ahol az alkalmazást regisztrálni kívánt.
3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse a megjelenő utasításokat hozzon létre egy **natív ügyfélalkalmazás**.
  * **Név** az alkalmazásnak, hogy a felhasználók ismerteti.
  * **Átirányítási URI** protokollt és a karakterlánc kombinációját, amely az Azure AD token válaszok adja vissza. Adja meg a helyőrző értékét most (például **http://DirectorySearcher**). Cseréli le a érték később.
6. A regisztráció befejezését követően az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. A kimásolhatja az értéket a **alkalmazás** lapon, mert később szüksége.
7. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**.
8. Az a **Azure Active Directory** alkalmazást, jelölje be **Microsoft Graph** , az API-t.
9. A **delegált engedélyek**, vegye fel a **hozzáférés a címtárhoz a bejelentkezett felhasználó az** engedéllyel. Így lehetővé teszi az alkalmazásnak, hogy a felhasználók számára a Graph API lekérdezése.

## <a name="step-2-install-and-configure-adal"></a>2. lépés: Telepítse és konfigurálja az adal-t
Most, hogy egy alkalmazást az Azure ad-ben, telepítse az adal-t, és az identitás-kapcsolódó kód írása. Ahhoz, hogy az adal-t az Azure AD kommunikálni, adjon neki az alkalmazás regisztrációs adatait.

1. Adal-t a Csomagkezelő konzol használatával adja hozzá a DirectorySearcher projekt.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. A DirectorySearcher projektben nyissa meg a MainPage.xaml.cs.
3. Cserélje le az értékeket a **konfigurációs értékeire** régió az Azure-portálon megadott értékekkel. A kód hivatkozik ezeket az értékeket, minden alkalommal adal-t.
  * A *bérlői* a tartományt az Azure AD bérlője (például contoso.onmicrosoft.com).
  * A *clientId* az ügyfél-azonosító az alkalmazás, amely a portálról kimásolt.
4. Most szeretné felderíteni a visszahívási URI a Windows Áruházbeli alkalmazás. Ez a sor állítson be egy töréspontot a `MainPage` módszert:
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. A megoldás felépítéséhez, annak biztosítása, hogy az összes csomag-hivatkozások, a rendszer visszaállítja. Ha hiányoznak a csomagokat, nyissa meg a NuGet-Csomagkezelőt, és állítsa vissza őket.
6. Futtassa az alkalmazást, és másolja a értékének `redirectUri` amikor a töréspont elérte-e. Az érték hasonlóan kell kinéznie a következő:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Újra be a **beállítások** lapon vegye fel az alkalmazás az Azure portálon, a **RedirectUri** az előző érték.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>3. lépés: Használja az adal TÁRAT jogkivonatok lekérni az Azure AD
Az alapelv ADAL mögött, hogy az alkalmazás olyan hozzáférési jogkivonatot kell, ha egyszerűen meghívja `authContext.AcquireToken(…)`, és a többi adal-t.  

1. Az alkalmazás inicializálása `AuthenticationContext`, vagyis az elsődleges osztály az adal-t. Ez a művelet továbbítja az adal-t a koordináták kommunikálni az Azure AD, és mondja el, akkor jogkivonatok gyorsítótárazásának kell.

    ```csharp
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Keresse meg a `Search(...)` metódust, ha a felhasználók kattintanak, amelyet a **keresési** az alkalmazás Kezelőfelületén gombjára. Ez a módszer az Azure AD Graph API lekérdezéshez get kérés teszi a felhasználók számára, amelyek egyszerű felhasználónév a megadott keresési kifejezés kezdődik. A Graph API lekérdezéséhez közé tartoznak az olyan hozzáférési jogkivonatot a kérelemben szereplő **engedélyezési** fejléc. Ez az adal-t honnan.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Ha kér az alkalmazás jogkivonat meghívásával `AcquireTokenAsync(...)`, adal-t próbálja elküldeni a jogkivonatot a felhasználói hitelesítő adatok kérése nélkül. ADAL határozza meg, hogy a felhasználó bejelentkezhet a szolgáltatáshitelesítést egy token kell-e, ha azt egy bejelentkezési párbeszédpanel jelenik meg, a felhasználó hitelesítő adatait gyűjti és egy jogkivonatot ad vissza, sikeres hitelesítést követően. Ha adal-t nem lehet visszaadni a jogkivonat bármilyen okból a *AuthenticationResult* állapot: Hiba történt.
3. Most meg kell használni a kapott hozzáférési jogkivonat. A is a `Search(...)` módszer, csatolni a tokent a Graph API get kérést a **engedélyezési** fejléc:

    ```csharp
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Használhatja a `AuthenticationResult` objektum információt szeretne megjeleníteni a felhasználó az alkalmazásban, például a felhasználói azonosító:

    ```csharp
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. ADAL segítségével is bejelentkezhetnek a felhasználók az alkalmazásból. Amikor a felhasználó kattint a **Kijelentkezés** gombra, győződjön meg arról, hogy a következő hívást `AcquireTokenAsync(...)` jeleníti meg, bejelentkezhet. Az ADAL Ez a művelet nem egyszerű módon, ha a token gyorsítótár kiürítése:

    ```csharp
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>A következő lépések
Most már rendelkezik egy működő, amely hitelesíti a felhasználókat, biztonságos webes API-k OAuth 2.0 használatával hívható, és a felhasználóval kapcsolatos alapvető információk a Windows áruház egy alkalmazásához.

Ha már nincs beállítva, feltöltve a felhasználókkal a bérlő, most az ehhez idő.
1. A DirectorySearcher alkalmazás futtatását, és jelentkezzen be a felhasználók közül.
2. Az egyszerű Felhasználónevük alapján más felhasználók kereséséhez.
3. Zárja be az alkalmazást, és újra futtathatja. Figyelje meg, hogyan az ügyfél helye változatlan marad.
4. Kattintson a jobb gombbal az alsó sáv megjelenítése jelentkezzen ki, majd jelentkezzen be egy másik felhasználó.

ADAL megkönnyíti a közös identitás funkciók ilyen beépítse az alkalmazást. Azt gondoskodik a dirty munkát meg, például a gyorsítótár kezelése OAuth protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, a bemutató, és a jogkivonatok frissítése lejárt. Csak egyetlen API-hívással, ismernie kell `authContext.AcquireToken*(…)`.

Hivatkozás, töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (a konfigurációs értékek nélkül).

Most már továbbléphet további identitás forgatókönyvek. Például [egy .NET webes API-t az Azure ad-vel biztonságos](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
