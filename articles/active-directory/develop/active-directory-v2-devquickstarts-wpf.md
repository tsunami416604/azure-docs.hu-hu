---
title: "Az Azure Active Directory v2.0 .NET natív alkalmazással |} Microsoft Docs"
description: "Hogyan hozhat létre, mely aláírja a felhasználók be mindkét személyes Microsoft-Account natív .NET-alkalmazás és a munkahelyi vagy iskolai fiókját."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7389f55ee6fef9548abb0ca4ac1bbd0399868d47
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Bejelentkezés a Windows asztali alkalmazások hozzáadása
Az a v2.0-végponttól adhat gyorsan hozzá támogatja a személyes Microsoft-fiókot is az asztali alkalmazások hitelesítés és a munkahelyi vagy iskolai fiókok.  Emellett lehetővé teszi az alkalmazásnak, hogy biztonságosan tudjon kommunikálni a háttér webes API-t, valamint [a Microsoft Graph](https://graph.microsoft.io) és néhány a [Office 365 egyesített API-k](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Nem minden Azure Active Directory (AD) forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

A [az eszközön futó .NET-natív alkalmazások](active-directory-v2-flows.md#mobile-and-native-apps), az Azure AD a Microsoft Identity hitelesítési tár vagy MSAL biztosít.  MSAL meg azzal a céllal életben megkönnyítik az alkalmazások jogkivonatok lekérésére történt a webszolgáltatások.  Annak bemutatásához, milyen könnyű van, itt azt fogja hozza létre a .NET WPF feladatlista alkalmazását, amelyek:

* A felhasználó bejelentkezik, és lekérdezi a hozzáférési jogkivonatok használatával a [OAuth 2.0 hitelesítési protokoll](active-directory-v2-protocols.md).
* Biztonságosan meghívja a háttérkiszolgálón feladatlista webszolgáltatáshoz, amely OAuth 2.0 is védi.
* A felhasználó kijelentkezik.

## <a name="download-sample-code"></a>Mintakód letöltése
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) vagy klónozza a vázat:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

A kész alkalmazásról, valamint az oktatóanyag végén valósul meg.

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy kövesse az alábbi [részletes lépéseket](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolja le a **alkalmazásazonosító** be az alkalmazáshoz hozzárendelt szüksége lehet rájuk hamarosan.
* Adja hozzá a **Mobile** platform az alkalmazásra vonatkozóan.

## <a name="install--configure-msal"></a>Telepítse & MSAL konfigurálása
Most, hogy az alkalmazás regisztrálva a Microsoft, MSAL telepítse, és az identitás-kapcsolódó kód írása.  Ahhoz, hogy tudjon kommunikálni a v2.0-végponttal való MSAL meg kell biztosítania, bizonyos információkat az alkalmazás regisztrációját.

* Első lépésként MSAL ad hozzá a TodoListClient projekt a Csomagkezelő konzol használatával.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* A TodoListClient projektben nyissa meg a `app.config`.  Cserélje le az értékeket az elemek a `<appSettings>` szakaszban az alkalmazás regisztrációs portált megadott értékeknek megfelelően.  A kód minden alkalommal MSAL hivatkozik ezeket az értékeket.
  
  * A `ida:ClientId` van a **alkalmazásazonosító** az alkalmazás a portálról másolta.
* Nyissa meg a TodoList-projekt, `web.config` a projekt gyökérkönyvtárában található.  
  
  * Cserélje le a `ida:Audience` azonos érték **alkalmazásazonosító** a portálról.

## <a name="use-msal-to-get-tokens"></a>A jogkivonatok lekérésére MSAL segítségével
Az alapelv MSAL mögött, hogy a hozzáférési tokent kell, ha egyszerűen meghívja a `app.AcquireToken(...)`, és a többi MSAL.  

* Az a `TodoListClient` projektben nyissa meg `MainWindow.xaml.cs` , és keresse meg a `OnInitialized(...)` metódust.  Az első lépés az, hogy az alkalmazás inicializálása `PublicClientApplication` -natív alkalmazások képviselő MSAL tartozó elsődleges osztály.  Ez azért, ahol át MSAL kommunikálni az Azure AD, és mondja el, akkor jogkivonatok gyorsítótárazásának kell koordinátáit.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Az alkalmazás indításakor azt szeretnénk, ha a felhasználó már bejelentkezett az alkalmazásba ellenőrizheti, hogy végzi el.  Azonban a bejelentkezési felhasználói felület meghívása még nem szeretnénk - lesz biztosítjuk, kattintson a "Bejelentkezés" ehhez a felhasználó.  A is a `OnInitialized(...)` módszert:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Ha a felhasználó nem jelentkezett be, és a "Bejelentkezés" gombra kattintva, szeretnénk meghívni egy bejelentkezési felhasználói felület és a felhasználó adja meg a hitelesítő adatokat.  A bejelentkezési gomb kezelő megvalósításához:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Ha a felhasználó sikeresen jelentkezik be, MSAL fogadja és gyorsítótárazza a jogkivonatot, és folytathatja a hívás a `GetTodoList()` metódus az vetett bizalmat.  Összes felhasználói feladatok beolvasása marad, hogy alkalmazza a `GetTodoList()` metódust.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Futtassa a következőt:
Gratulálunk! Most már rendelkezik egy működő .NET WPF-alkalmazás, amely képes a felhasználók hitelesítéséhez és biztonságosan hívja a webes API-k, az OAuth 2.0 verziót használja.  A mindkét projekt futtatásához, és jelentkezzen be személyes Microsoft-fiókkal vagy egy munkahelyi vagy iskolai fiókkal.  Feladatok hozzáadása, a felhasználó tennivalók listájára.  Jelentkezzen ki, és jelentkezzen be egy másik felhasználó megtekintéséhez a tennivalók listájára.  Zárja be az alkalmazást, és futtassa újból.  Figyelje meg, hogyan sértetlenek maradnak a felhasználói munkamenet -, amelyek oka, hogy az alkalmazás gyorsítótárazza a tokenek egy helyi fájlban.

MSAL megkönnyíti az alkalmazásba, az általános identitás-szolgáltatások átfogó személyes és munkahelyi fiókokat egyaránt használ.  Ez gondoskodik a dirty munkát meg - gyorsítótár kezelése, az OAuth-protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, lejárt jogkivonatokat, és több frissítése a bemutató.  Biztosan tudni, hogy szüksége egy egyetlen API-hívással `app.AcquireTokenAsync(...)`.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [is letöltheti a .zip Itt](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), vagy a Githubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Következő lépések
Ön most már továbbléphet az összetettebb témákra.  Előfordulhat, hogy ki szeretné próbálni:

* [A v2.0-végponttal a TodoListService webes API biztonságossá tétele](active-directory-v2-devquickstarts-dotnet-api.md)

További forrásokért tekintse meg:  

* [A v2.0 – útmutató fejlesztőknek >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "msal" címke >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.

