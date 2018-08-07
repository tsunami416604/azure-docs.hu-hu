---
title: Az Azure AD .NET asztali (WPF) első lépések |} A Microsoft Docs
description: Hogyan hozhat létre egy Windows asztali .NET-alkalmazás, amely integrálható az Azure AD-bejelentkezés, és meghívja az Azure AD OAuth protokollt használó API-k védelme.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c4bc777c36608ff9aeeec3428af0103fa3ae29c4
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578962"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Az Azure AD .NET asztali (WPF) első lépések
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ha egy asztali alkalmazást fejleszt, az Azure AD teszi egyszerű és könnyen érthető megjegyzésblokkok írására, hogy hitelesítheti a felhasználókat az Active Directory-fiókjukat. Emellett lehetővé teszi az alkalmazás bármely által védett webes API Azure AD-ben például az Office 365 API-k vagy az Azure API biztonságosan felhasználásához.

.NET natív ügyfelek esetében a védett erőforrásokhoz kell hozzáférniük, az Azure AD az Active Directory Authentication Library, vagy az adal-t biztosít. Adal-t a kizárólagos magánéletéből célja megkönnyítik az alkalmazás hozzáférési. Használatával mutatja be, milyen egyszerűen, itt létrehozunk egy .NET WPF feladatlista-alkalmazás, amely:

* Lekérdezi hozzáférési jogkivonatokat hívásakor, az Azure AD Graph API használatával a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* A felhasználók számára egy adott aliasú könyvtár keres.
* Tünetek felhasználók ki.

Hozhat létre a teljes, működő alkalmazást, a következőket kell tennie:

1. Regisztrálja az alkalmazást az Azure ad-ben.
2. Telepítése és konfigurálása adal-t.
3. Adal-t használó tokenekhez Azure AD-ből való.

Első lépésként [töltse le az alkalmazás skeleton](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Emellett szüksége, amelyben létre felhasználókat és regisztrálni egy alkalmazást az Azure AD-bérlővel. Ha még nem rendelkezik egy bérlő [megtudhatja, hogyan tehet szert egy](quickstart-create-new-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy az alkalmazást, hogy a jogkivonatok lekérésére, először szüksége az Azure AD-bérlő regisztrálásához, és adja meg azt az Azure AD Graph API hozzáférési engedélyt:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A fiókjában, majd a kattintson a felső sávon a **Directory** menüben válassza ki az Active Directory-bérlőt, az alkalmazásokat regisztrálni szeretne.
3. Kattintson a **minden szolgáltatás** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **alkalmazásregisztrációk** válassza **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új **natív ügyfélalkalmazás**.
  * A **neve** az alkalmazás ismerteti az alkalmazást a végfelhasználók számára
  * A **átirányítási URI-t** sémát és karakterlánc kombinációja, amely az Azure AD a jogkivonatválaszok visszaadására fog használni. Adja meg például egy adott értéket az alkalmazás `http://DirectorySearcher`.
6. Miután végrehajtotta a regisztráció, AAD rendeli az alkalmazást egy egyedi azonosítóját. Ez az érték kell a következő szakaszokban, ezért másolja ki az alkalmazás oldaláról.
7. Az a **beállítások** lapon a **szükséges engedélyek** válassza **Hozzáadás**. Válassza ki a **Microsoft Graph** az API-ként, és adja hozzá a **címtáradatok olvasása** engedélyt a **delegált engedélyek**. Ez lehetővé teszi az alkalmazás a felhasználók számára a Graph API lekérdezéséhez.

## <a name="2-install--configure-adal"></a>2. Telepítése és konfigurálása adal-t
Most, hogy egy alkalmazás az Azure ad-ben, telepítheti az adal-t és az identitással kapcsolatos kód írása. Ahhoz, hogy tudjon kommunikálni az Azure ad-vel az ADAL meg kell biztosítania, némi információt az alkalmazás regisztrációját.

* Első lépésként adja hozzá a DirectorySearcher projekthez, a Package Manager konzol segítségével adal-t.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* A DirectorySearcher projektben nyissa meg a `app.config`. Cserélje le az értékeket az elemek a `<appSettings>` szakasz az értékekhez adjon meg az Azure Portalra. A kód ezeket az értékeket hivatkozik, amikor az adal-t használ.
  * A `ida:Tenant` a tartomány az Azure AD-bérlő, például contoso.onmicrosoft.com
  * A `ida:ClientId` clientId-t az alkalmazás a portálról kimásolt van.
  * A `ida:RedirectUri` van az átirányítási URL-címet, a portál regisztrált.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Adal-t használó tokenek beolvasni az aad-ből
Az alapelv ADAL mögött, hogy minden alkalommal, amikor az alkalmazás kell egy hozzáférési jogkivonatot, egyszerűen meghívja `authContext.AcquireTokenAsync(...)`, ADAL végzi. 

* Az a `DirectorySearcher` projektben nyissa meg `MainWindow.xaml.cs` , és keresse meg a `MainWindow()` metódust. Az első lépés az, hogy az alkalmazás inicializálása `AuthenticationContext` -ADAL által elsődleges osztály. Ez az, ahol adja át az adal-t a koordináták kommunikálni az Azure ad-ben, és mondja el, hogyan gyorsítótárazza a jogkivonatok szükséges.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Keresse meg a `Search(...)` módot, amelyet akkor kell meghívni, amikor a felhasználó az alkalmazás felhasználói felületén a "Search" gombra kattint. Ez a módszer egy GET kéréssel teszi az Azure AD Graph API lekérdezéséhez, a felhasználók számára, akiknek UPN kezdődik-e a megadott keresési kifejezés. A Graph API lekérdezéséhez, meg kell adni egy access_token lévő, de a `Authorization` fejléc a kérelem - ezt az hol ADAL érhető el.

```csharp
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
* Ha az alkalmazás tokent kér meghívásával `AcquireTokenAsync(...)`, ADAL megkísérli egy token vissza a felhasználói hitelesítő adatok kérése nélkül. Ha adal-t, hogy a felhasználónak kell-e jogkivonat beszerzéséhez jelentkezzen be, azt fog egy bejelentkezési párbeszédpanel megjelenítése, a felhasználó hitelesítő adatainak gyűjtése és sikeres hitelesítést követően jogkivonat. Ha adal-t nem adható vissza a jogkivonat bármilyen okból, hogy kivételt fogja kijelezni egy `AdalException`.
* Figyelje meg, hogy a `AuthenticationResult` az objektum tartalmaz egy `UserInfo` objektum, amely használható az alkalmazás szükségessé kapcsolatos információk összegyűjtéséhez. Az a DirectorySearcher `UserInfo` szabható testre az alkalmazás felhasználói felületén, a felhasználói azonosítóval.
* Ha a felhasználó a "Sign Out" gombra kattint, annak érdekében, hogy a következő hívást szeretnénk `AcquireTokenAsync(...)` kéri a felhasználót, hogy jelentkezzen be. Az adal-t ez így nem kell tennie, csak a jogkivonat gyorsítótár kiürítése:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Azonban ha a felhasználó nem a "Sign Out" gombra kattintva célszerű a következő biztonsági a DirectorySearcher futnak a felhasználói munkamenet fenntartásához. Az alkalmazás indításakor adal-t a jogkivonatok gyorsítótárát egy meglévő token ellenőrizheti és a felhasználói felületen ennek megfelelően frissülnek. Az a `CheckForCachedToken()` módszer, egy másik hívás `AcquireTokenAsync(...)`, ezúttal ad át a `PromptBehavior.Never` paraméter. `PromptBehavior.Never` adal-t jelzi, hogy a felhasználó nem kéri a bejelentkezéshez, és az adal-t kell helyette kivételt, ha nem tudja jogkivonat.

```csharp
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
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

Gratulálunk! Most már rendelkezésére áll, amely képes hitelesíteni a felhasználókat, az OAuth 2.0-val webes API-k biztonságos hívása .NET WPF-alkalmazás, és alapvető információkat szeretne a felhasználó kaphat. Ha még nem tette, most már az az idő, az egyes felhasználók a bérlő feltölti. A DirectorySearcher alkalmazás futtatásához, és jelentkezzen be egy ezen felhasználók. Keresse meg a más felhasználók, az egyszerű felhasználónév alapján. Zárja be az alkalmazást, és futtassa újból. Figyelje meg, hogyan a felhasználói munkamenet nem módosulnak. Jelentkezzen ki, és a egy másik felhasználóként jelentkezzen be.

Adal-t megkönnyíti az alkalmazásba beépíteni az összes alábbi általános identitás-szolgáltatások. Ez gondoskodik a dirty munka, - gyorsítótár kezeléséhez, OAuth-protokoll támogatása, bemutatása a felhasználó egy bejelentkezési felhasználói felület, lejárt, és további frissítése. Feltétlenül szükséges tudnia, csak egyetlen API hívással `authContext.AcquireTokenAsync(...)`.

Referenciaként az elkészült mintát (a konfigurációs értékek) nélkül biztosított [Itt](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Most már továbbléphet további forgatókönyvek. Előfordulhat, hogy szeretné próbálni:

[.NET webes API Azure AD-vel biztonságos >>](quickstart-v1-dotnet-webapi.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

