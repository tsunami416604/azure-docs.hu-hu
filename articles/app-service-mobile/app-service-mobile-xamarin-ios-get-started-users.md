---
title: A hitelesítés első lépései a Xamarin iOS rendszerben
description: Ismerje meg, hogyan hitelesítheti a Xamarin iOS-alkalmazás felhasználóit olyan identitásszolgáltatókkal, mint az AAD, a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461317"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Hitelesítés hozzáadása a Xamarin.iOS alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan hitelesítheti az App Service Mobile-alkalmazás felhasználóit az ügyfélalkalmazásból. Ebben az oktatóanyagban az App Service által támogatott identitásszolgáltató használatával adja hozzá a hitelesítést a Xamarin.iOS gyorsindítási projekthez. Miután a mobilalkalmazás sikeresen hitelesítette és engedélyezte, megjelenik a felhasználói azonosító értéke, és hozzáférhet a korlátozott táblaadatokhoz.

Először be kell fejeznie az [oktatóanyagot Xamarin.iOS alkalmazás létrehozása]. Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia a hitelesítési bővítménycsomagot a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Az alkalmazás regisztrálása hitelesítésre és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba. Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk. Azonban bármilyen URL-sémát használhat. Meg kell egyedi a mobil alkalmazás. Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki az App Service.In the Azure Portal , select your App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `url_scheme_of_your_app://easyauth.callback`  A **karakterlánc url_scheme_of_your_app** a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

4. Kattintson az **OK** gombra.

5. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* A Visual Studio vagy a Xamarin Studio alkalmazásban futtassa az ügyfélprojektet egy eszközön vagy emulátoron. Ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. A hiba a hibakereső konzoljára kerül. Így a Visual Studio, meg kell jelennie a hiba a kimeneti ablakban.

    Ez a jogosulatlan hiba azért történik, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a mobilalkalmazás-háttérrendszerhez. A *TodoItem* tábla most hitelesítést igényel.

Ezután frissíteni fogja az ügyfélalkalmazást, hogy erőforrásokat kérjen a mobilalkalmazás-háttérrendszertől egy hitelesített felhasználóval.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
Ebben a szakaszban módosíthatja az alkalmazást, hogy az adatok megjelenítése előtt megjelenítse a bejelentkezési képernyőt. Amikor az alkalmazás elindul, nem csatlakozik az App Service szolgáltatáshoz, és nem jelenít meg adatokat. Miután a felhasználó először hajtja végre a frissítési kézmozdulatot, megjelenik a bejelentkezési képernyő; sikeres bejelentkezés után megjelenik a teendők listája.

1. Az ügyfélprojektben nyissa meg a fájlt **QSTodoService.cs,** és adja hozzá a következőt utasítással és `MobileServiceUser` accessorral a QSTodoService osztályhoz:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adjon hozzá **új, Hitelesítés** nevű metódust a **QSTodoService szolgáltatáshoz** a következő definícióval:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Ha nem Facebook-identitásszolgáltatót használ, módosítsa a fenti **LoginAsync** értékre átadott értéket a következők egyikére: _MicrosoftAccount_, _Twitter_, _Google_vagy _WindowsAzureActiveDirectory_.

3. Nyissa meg **a QSTodoListViewController.cs.** Módosítsa a **ViewDidLoad** metódusdefinícióját, amely eltávolítja a **RefreshAsync()** hívását a vége felé:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Módosítsa a **RefreshAsync** metódust, hogy hitelesítse magát, ha a **User** tulajdonság null értékű. Adja hozzá a következő kódot a metódusdefiníció tetején:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Nyissa **meg AppDelegate.cs,** adja hozzá a következő módszert:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Nyissa **meg az Info.plist** fájlt, és keresse meg az **URL-típusokat** a **Speciális** szakaszban. Most konfigurálja az **URL-típus azonosítóját** és **URL-sémákat,** és kattintson az **URL-típus hozzáadása gombra.** **Az URL-sémáknak** meg kell egyezniük a(z) {url_scheme_of_your_app} címével.
7. A Visual Studio programban, amely a Mac Host vagy a Visual Studio for Mac alkalmazáshoz csatlakozik, futtassa az eszköz vagy emulátor t. Ellenőrizze, hogy az alkalmazás nem jelenít-e meg adatokat.

    Hajtsa végre a frissítési kézmozdulatot az elemek listájának lehúzásával, ami a bejelentkezési képernyő megjelenését eredményezi. Miután sikeresen megadta az érvényes hitelesítő adatokat, az alkalmazás megjeleníti a teendők listáját, és frissítheti az adatokat.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
