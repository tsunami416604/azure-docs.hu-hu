---
title: "Ismerkedés az Azure AD .NET |} Microsoft Docs"
description: "Hogyan hozható létre a .NET Windows asztali alkalmazás, amely integrálható az Azure ad-val jelentkezzen be, és meghívja az Azure AD API-k OAuth protokollt használó védett."
services: active-directory
documentationcenter: .net
author: jmprieur
manager: mtillman
editor: 
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 41abe20d778a8c51c6b19733ddf5426d12d8751e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Az Azure AD integrálása Windows asztali WPF-alkalmazások
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ha az asztali alkalmazások, az Azure AD segítségével egyszerű és magától értetődő, hogy a felhasználókat az Active Directory-fiókok hitelesítéséhez.  Emellett lehetővé teszi az alkalmazás minden webes API-t az Azure AD, például az Office 365 API-k vagy az Azure API által védett biztonságosan felhasználását.

A .NET natív ügyfelek esetében a védett erőforrások eléréséhez szükséges az Azure AD az Active Directory Authentication Library, vagy az adal-t biztosít.  ADAL meg azzal a céllal életben megkönnyíti a hozzáférési jogkivonatok lekérésére, ha az alkalmazásban.  Annak bemutatásához, milyen könnyű van, itt azt fogja összeállíthat egy .NET WPF feladatlista alkalmazást, amely:

* Lekérdezi hozzáférési jogkivonatok hívásakor az Azure AD Graph API segítségével a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Egy könyvtárat a felhasználók egy adott aliasnév keres.
* Kimenő felhasználók jeleket.

A teljes működő alkalmazás létrehozásához szüksége:

1. Az alkalmazás regisztrálása az Azure ad-val.
2. Telepítése és konfigurálása adal-t.
3. Adal-t használó tokenek lekérni az Azure AD.

A kezdéshez [töltse le az alkalmazás vázat](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Biztosítani kell, amelyben felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő.  Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy az alkalmazás a jogkivonatok lekérésére, először regisztrálja az Azure AD-bérlőben, és adja meg azt az Azure AD Graph API hozzáférése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókhoz, majd a a **Directory** menüben válassza ki az Active Directory-bérlőt, ahová be szeretné-e az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **App regisztrációk** válassza **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új **natív ügyfélalkalmazás**.
  * A **neve** az alkalmazás ismerteti az alkalmazást a végfelhasználók számára
  * A **átirányítási Uri-** protokollt és a karakterlánc kombinációját, amely az Azure AD vissza a token válaszokat fogja használni.  Adja meg például egy adott értéket az alkalmazás `http://DirectorySearcher`.
6. Miután végrehajtotta a regisztráció, AAD fogja hozzárendelni az alkalmazás egy egyedi azonosítót.  Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás oldalról.
7. Az a **beállítások** lapon, válassza ki **szükséges engedélyek** válassza **Hozzáadás**. Válassza ki a **Microsoft Graph** az API-t, és adja hozzá a **címtáradatok olvasása** engedélyt a **delegált engedélyek**.  Ez lehetővé teszi az alkalmazás lekérdezése a Graph API-t a felhasználók számára.

## <a name="2-install--configure-adal"></a>2. Telepítése és konfigurálása adal-t
Most, hogy az Azure AD-alkalmazás, telepítse az adal-t, és az identitás-kapcsolódó kód írása.  Ahhoz, hogy az adal-t tudjanak kommunikálni az Azure AD meg kell biztosítania, bizonyos információkat az alkalmazás regisztrációját.

* Kezdje az adal-t ad hozzá a DirectorySearcher projekt a Csomagkezelő konzol használatával.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* A DirectorySearcher projektben nyissa meg a `app.config`.  Cserélje le az értékeket az elemek a `<appSettings>` szakaszban az Azure portált megadott értékeknek megfelelően.  A kód minden alkalommal ADAL hivatkozik ezeket az értékeket.
  * A `ida:Tenant` a tartományt az Azure AD-bérlőn, pl.: contoso.onmicrosoft.com
  * A `ida:ClientId` ClientID-azonosítóját az alkalmazás a portálról másolt van.
  * A `ida:RedirectUri` van az átirányítási URL-cím regisztrálta a portálon.

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Adal-t használó tokenek lekérése az aad-ben
Az alapelv ADAL mögött, hogy a hozzáférési tokent kell, ha egyszerűen meghívja `authContext.AcquireTokenAsync(...)`, és a többi adal-t.  

* Az a `DirectorySearcher` projektben nyissa meg `MainWindow.xaml.cs` , és keresse meg a `MainWindow()` metódust.  Az első lépés az, hogy az alkalmazás inicializálása `AuthenticationContext` -ADAL tartozó elsődleges osztály.  Ez az adott át adal-t a koordináták kommunikálni az Azure AD, és mondja el, akkor jogkivonatok gyorsítótárazásának kell.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Keresse meg a `Search(...)` metódus, amely akkor kell meghívni, amikor a felhasználó cliks a "Search" gombra az alkalmazás felhasználói felületén.  Ez a módszer az Azure AD Graph API lekérdezéshez GET kérés teszi a felhasználók számára, amelyek egyszerű felhasználónév a megadott keresési kifejezés kezdődik.  Ahhoz, hogy a Graph API lekérdezni, meg kell adnia egy access_token a, de a `Authorization` fejlécet a kérelem - ezt az adal-t honnan.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Ha az alkalmazás kísérel meg jogkivonat meghívásával `AcquireTokenAsync(...)`, ADAL megkísérli jogkivonat vissza a felhasználói hitelesítő adatok kérése nélkül.  ADAL határozza meg, hogy a felhasználó bejelentkezhet a szolgáltatáshitelesítést egy token kell-e, ha azt fogja bejelentkezési párbeszédablakot jelenít meg, a felhasználói hitelesítő adatok összegyűjtése és térjen vissza a sikeres hitelesítés esetén egy token.  Ha nem lehet visszaadni a jogkivonatot a bármilyen okból adal-t, akkor kivételhibát egy `AdalException`.
* Figyelje meg, hogy a `AuthenticationResult` objektum tartalmaz egy `UserInfo` objektum, amely segítségével az alkalmazás esetleg adatokat gyűjteni.  Az a DirectorySearcher `UserInfo` segítségével testre szabhatja az alkalmazás Kezelőfelületén a felhasználói azonosítóval.
* Amikor a felhasználó a "Sign Out" gombra kattint, annak érdekében, hogy a következő hívást szeretnénk `AcquireTokenAsync(...)` ekkor megkérdezi a felhasználót, hogy jelentkezzen be.  Ez az adal-t, az egyszerű módon, ha a token gyorsítótár kiürítése:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Azonban ha a felhasználó nem a "Sign Out" gombra kattintva érdemes a felhasználói munkamenetet a következő alkalommal futtatja a DirectorySearcher a karbantartása.  Az alkalmazás indításakor ellenőrizze az adal-t a jogkivonatok gyorsítótárát, vagy egy meglévő jogkivonatot, és ennek megfelelően frissíti a felhasználói felületen.  Az a `CheckForCachedToken()` módszer, egy másik hívás `AcquireTokenAsync(...)`, ezúttal benyújtása a `PromptBehavior.Never` paraméter.  `PromptBehavior.Never`adal-t jelzi, hogy a felhasználói bejelentkezés nem kéri, és ADAL helyette kell kivételt jelez, ha nem adja vissza egy token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Gratulálunk! Most már rendelkezik működő .NET WPF-alkalmazás, amely képes a felhasználók hitelesítésére, biztonságosan hívja az OAuth 2.0 használatával webes API-k, és a felhasználóval kapcsolatos alapvető információk.  Ha még nem tette meg, most már az egyes felhasználóival a bérlő feltölti idő.  Futtassa a DirectorySearcher alkalmazást, és jelentkezzen be valamelyik azoknak a felhasználóknak.  Az egyszerű Felhasználónevük alapján más felhasználók kereséséhez.  Zárja be az alkalmazást, és futtassa újból.  Figyelje meg, hogyan az ügyfél helye változatlan marad.  Jelentkezzen ki, és jelentkezzen be egy másik felhasználó.

Az adal TÁRAT megkönnyíti, hogy átfogó mindezeket a közös identitás funkciókat az alkalmazásba.  Ez gondoskodik a dirty munkát meg - gyorsítótár kezelése, az OAuth-protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, lejárt jogkivonatokat, és több frissítése a bemutató.  Biztosan tudni, hogy szüksége egy egyetlen API-hívással `authContext.AcquireTokenAsync(...)`.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként biztosított [Itt](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Most már továbbléphet további helyzeteket is.  Előfordulhat, hogy ki szeretné próbálni:

[.NET webes API-k, az Azure ad-vel biztonságos >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

