---
title: "Ismerkedés az Azure AD-Windows Phone |} Microsoft Docs"
description: "Hogyan hozható létre, amely integrálható az Azure ad-val jelentkezzen be, és meghívja az Azure AD egy Windows Phone-alkalmazás OAuth protokollt használó API-k védett."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 66f5ac20-5e1f-4b9d-bb99-9b3305e26416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c078ae22255190a37d75a4100ebfffcb6288c4cb
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-windows-phone-getting-started"></a>Ismerkedés az Azure AD Windows Phone
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> A Windows Phone 8.1-es és korábbi verziójú projektek nem támogatottak a Visual Studio 2017-ben.  További információ: [A Visual Studio 2017 platform célcsoportkezelése és kompatibilitása](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Ha a Windows Phone 8.1-alkalmazást, az Azure AD segítségével egyszerű és magától értetődő, hogy a felhasználókat az Active Directory-fiókok hitelesítéséhez.  Emellett lehetővé teszi az alkalmazás minden webes API-t az Azure AD, például az Office 365 API-k vagy az Azure API által védett biztonságosan felhasználását.

> [!NOTE]
> A fenti ADAL v2.0 használja.  A legújabb technológiák, érdemes lehet helyette próbálkozzon az [Windows Universal oktatóanyagot ADAL v3.0 használatával](active-directory-devquickstarts-windowsstore.md).  Ha valóban egy alkalmazást a Windows Phone 8.1-es, ez az a megfelelő helyen.  ADAL v2.0 továbbra is teljes mértékben támogatja, és az ajánlott módszer a Windows Phone 8.1-es alkalmazások agianst fejlődő használja az Azure AD.
> 
> 

A .NET natív ügyfelek esetében a védett erőforrások eléréséhez szükséges az Azure AD az Active Directory Authentication Library, vagy az adal-t biztosít.  ADAL meg azzal a céllal életben megkönnyíti a hozzáférési jogkivonatok lekérésére, ha az alkalmazásban.  Annak bemutatásához, milyen könnyű van, itt azt fogja össze egy "Directory keresővel" Windows Phone 8.1-alkalmazást, amely:

* Lekérdezi hozzáférési jogkivonatok hívásakor az Azure AD Graph API segítségével a [OAuth 2.0 hitelesítési protokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Megkeresi a megadott egyszerű felhasználónév a felhasználók számára.
* Kimenő felhasználók jeleket.

A teljes működő alkalmazás létrehozásához szüksége:

1. Az alkalmazás regisztrálása az Azure ad-val.
2. Telepítése és konfigurálása adal-t.
3. Adal-t használó tokenek lekérni az Azure AD.

A kezdéshez [töltse le a projekt vázát](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Egy Visual Studio 2013 megoldást.  Biztosítani kell, amelyben felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő.  Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>1. A könyvtár keresővel alkalmazás regisztrálása
Ahhoz, hogy az alkalmazás a jogkivonatok lekérésére, először regisztrálja az Azure AD-bérlőben, és adja meg azt az Azure AD Graph API hozzáférése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókhoz, majd a a **Directory** menüben válassza ki az Active Directory-bérlőt, ahová be szeretné-e az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **App regisztrációk** válassza **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új **natív ügyfélalkalmazás**.
  * A **neve** az alkalmazás ismerteti az alkalmazást a végfelhasználók számára
  * A **átirányítási Uri-** protokollt és a karakterlánc kombinációját, amely az Azure AD vissza a token válaszokat fogja használni.  Például a lépést, adjon meg egy helyőrzőt értéket `http://DirectorySearcher`.  Később lesz lecserélni ezt az értéket.
6. Miután végrehajtotta a regisztráció, AAD fogja hozzárendelni az alkalmazás egy egyedi azonosítót.  Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás lapján.
7. Az a **beállítások** lapon, válassza ki **szükséges engedélyek** válassza **Hozzáadás**. Válassza ki a **Microsoft Graph** az API-t, és adja hozzá a **címtáradatok olvasása** engedélyt a **delegált engedélyek**.  Ez lehetővé teszi az alkalmazás lekérdezése a Graph API-t a felhasználók számára.

## <a name="2-install--configure-adal"></a>2. Telepítése és konfigurálása adal-t
Most, hogy az Azure AD-alkalmazás, telepítse az adal-t, és az identitás-kapcsolódó kód írása.  Ahhoz, hogy az adal-t tudjanak kommunikálni az Azure AD meg kell biztosítania, bizonyos információkat az alkalmazás regisztrációját.

* Kezdje az adal-t ad hozzá a DirectorySearcher projekt a Csomagkezelő konzol használatával.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* A DirectorySearcher projektben nyissa meg a `MainPage.xaml.cs`.  Cserélje le az értékeket a `Config Values` régió be az Azure portálon megadott értékeknek megfelelően.  A kód minden alkalommal ADAL hivatkozik ezeket az értékeket.
  * A `tenant` a tartományt az Azure AD-bérlőn, pl.: contoso.onmicrosoft.com
  * A `clientId` ClientID-azonosítóját az alkalmazás a portálról másolt van.
* Most meg kell a Windows Phone-alkalmazás visszahívási URI-jának felderítése.  Ez a sor állítson be egy töréspontot a `MainPage` módszert:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
* Futtassa az alkalmazást, és másolja fenntartott értékének `redirectUri` amikor a töréspont elérte-e.  Az alábbiakhoz hasonlóan

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

* Vissza a **konfigurálása** fülre az alkalmazás az Azure felügyeleti portálján, cserélje le a a **RedirectUri** ezzel az értékkel.  

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Adal-t használó tokenek lekérése az aad-ben
Az alapelv ADAL mögött, hogy a hozzáférési tokent kell, ha egyszerűen meghívja `authContext.AcquireToken(…)`, és a többi adal-t.  

* Az első lépés az, hogy az alkalmazás inicializálása `AuthenticationContext` -ADAL tartozó elsődleges osztály.  Ez az adott át adal-t a koordináták kommunikálni az Azure AD, és mondja el, akkor jogkivonatok gyorsítótárazásának kell.

```csharp
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

* Keresse meg a `Search(...)` metódus, amely akkor kell meghívni, amikor a felhasználó cliks a "Search" gombra az alkalmazás felhasználói felületén.  Ez a módszer az Azure AD Graph API lekérdezéshez GET kérés teszi a felhasználók számára, amelyek egyszerű felhasználónév a megadott keresési kifejezés kezdődik.  Ahhoz, hogy a Graph API lekérdezni, meg kell adnia egy access_token a, de a `Authorization` fejlécet a kérelem - ezt az adal-t honnan.

```csharp
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
* Interaktív hitelesítésre szükség, ha adal-t fogja használni a Windows Phone webes hitelesítés Broker (WAB) és [folytatási modell](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) az Azure AD bejelentkezési megjeleníteni.  Amikor a felhasználó bejelentkezik, az alkalmazás ADAL továbbítani kell a WAB interakció eredményeit.  Ez az végrehajtási egyszerűen a `ContinueWebAuthentication` felület:

```csharp
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

* Mostantól az időpontok a `AuthenticationResult` , amely az alkalmazás adal-t küld vissza.  Az a `QueryGraph(...)` visszahívási, csatlakoztassa a access_token szerezte be a GET-kérés hitelesítési fejlécéhez a:

```csharp
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
* Használhatja a `AuthenticationResult` objektum információt szeretne megjeleníteni a felhasználó az alkalmazásban. Az a `QueryGraph(...)` metódust, az eredmény segítségével az oldalon a felhasználói azonosító megjelenítése:

```csharp
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
* Adal-t használó végül jelentkezzen ki, valamint az alkalmazás a felhasználó.  Amikor a felhasználó a "Sign Out" gombra kattint, annak érdekében, hogy a következő hívást szeretnénk `AcquireTokenSilentAsync(...)` sikertelen lesz.  Ez az adal-t, az egyszerű módon, ha a token gyorsítótár kiürítése:

```csharp
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Gratulálunk! Most működik Windows Phone-alkalmazást, amely képes a felhasználók hitelesítésére, biztonságosan hívja a webes API-k, az OAuth 2.0 verziót használja, és a felhasználó alapszintű adatainak beolvasása.  Ha még nem tette meg, most már az egyes felhasználóival a bérlő feltölti idő.  Futtassa a DirectorySearcher alkalmazást, és jelentkezzen be valamelyik azoknak a felhasználóknak.  Az egyszerű Felhasználónevük alapján más felhasználók kereséséhez.  Zárja be az alkalmazást, és futtassa újból.  Figyelje meg, hogyan az ügyfél helye változatlan marad.  Jelentkezzen ki, és jelentkezzen be egy másik felhasználó.

Az adal TÁRAT megkönnyíti, hogy átfogó mindezeket a közös identitás funkciókat az alkalmazásba.  Ez gondoskodik a dirty munkát meg - gyorsítótár kezelése, az OAuth-protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, lejárt jogkivonatokat, és több frissítése a bemutató.  Biztosan tudni, hogy szüksége egy egyetlen API-hívással `authContext.AcquireToken*(…)`.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként biztosított [Itt](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Most már továbbléphet további identitás forgatókönyvek.  Előfordulhat, hogy ki szeretné próbálni:

[.NET webes API-k, az Azure ad-vel biztonságos >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

