---
title: Ismerkedés a hitelesítéssel a Xamarin iOS-ben
description: Megtudhatja, hogyan használhatja a Mobile Appst a Xamarin iOS-alkalmazás felhasználóinak hitelesítésére, például a HRE, a Google, a Facebook, a Twitter és a Microsoft identitásával.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461317"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Hitelesítés hozzáadása a Xamarin. iOS-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan hitelesítheti App Service Mobile apps felhasználóit az ügyfélalkalmazás használatával. Ebben az oktatóanyagban a Xamarin. iOS gyors üzembe helyezési projekthez a App Service által támogatott identitás-szolgáltató használatával ad hozzá hitelesítést. Miután a Mobile App sikeresen hitelesítette és engedélyezte a hitelesítést, a rendszer megjeleníti a felhasználói azonosító értékét, és hozzáférhet a korlátozott táblák adataihoz.

Először végre kell hajtania az oktatóanyag [Xamarin. iOS-alkalmazás létrehozása]. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia a hitelesítési bővítmény csomagját a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben. Használhat azonban bármely kiválasztott URL-címet. Egyedinek kell lennie a Mobile-alkalmazás számára. Az átirányítás engedélyezése a kiszolgálóoldali oldalon:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `url_scheme_of_your_app://easyauth.callback`.  A karakterláncban szereplő **url_scheme_of_your_app** a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* A Visual Studióban vagy a Xamarin Studióban futtassa az ügyfél-projektet egy eszközön vagy emulátoron. Ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401 (nem engedélyezett) állapotkóddal. A hibát a hibakereső konzolján naplózza a rendszer. A Visual Studióban tehát a kimenet ablakban kell megjelennie a hibát.

    Ez a jogosulatlan hiba azért fordul elő, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a Mobile apps-háttérhöz. A *TodoItem* táblához már hitelesítés szükséges.

Ezután frissítenie kell az ügyfélszoftvert, hogy egy hitelesített felhasználótól kérjen erőforrásokat a Mobile apps-háttérből.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
Ebben a szakaszban módosítani fogja az alkalmazást, hogy megjelenjen a bejelentkezési képernyő az adatok megjelenítése előtt. Amikor az alkalmazás elindul, nem fog csatlakozni a App Servicehoz, és nem jelenít meg semmilyen adattartalmat. Miután a felhasználó első alkalommal elvégezte a frissítési kézmozdulatot, megjelenik a bejelentkezési képernyő. a sikeres bejelentkezés után megjelenik a ToDo-elemek listája.

1. Az ügyfél projektben nyissa meg a **QSTodoService.cs** fájlt, és adja hozzá a következő using utasítást és `MobileServiceUser` a QSTodoService osztályhoz:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adja hozzá a **QSTodoService** **nevű új metódust a következő** definícióval:

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
    > Ha a Facebooktól eltérő identitás-szolgáltatót használ, módosítsa az átadott értéket a **LoginAsync** -re a következők egyikére: _MicrosoftAccount_, _Twitter_, _Google_vagy _WindowsAzureActiveDirectory_.

3. Nyissa meg a **QSTodoListViewController.cs**. Módosítsa a **ViewDidLoad** metódusának definícióját, és távolítsa el a **RefreshAsync ()** függvényt a vége közelében:

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

4. Módosítsa a **RefreshAsync** metódust a hitelesítéshez, ha a **felhasználói** tulajdonság null értékű. Adja hozzá a következő kódot a metódus definíciójának elejéhez:

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

5. Nyissa meg a **AppDelegate.cs**, adja hozzá a következő metódust:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Nyissa meg az **info. plist** fájlt, navigáljon a **speciális** szakaszban található **URL-típusokhoz** . Most konfigurálja az URL-cím típusának **azonosítóját** és **URL-sémáját** , és kattintson az **URL-cím hozzáadása típus**elemre. Az **URL-sémáknak** meg kell egyezniük a {url_scheme_of_your_app} értékkel.
7. A Visual Studióban, a Mac-gazdagéphez vagy Visual Studio for Machoz csatlakoztatva futtassa az eszközt vagy emulátort célzó ügyfél-projektet. Győződjön meg arról, hogy az alkalmazás nem jelenít meg adatmegjelenítést.

    Végezze el a frissítési kézmozdulatot az elemek listájának lehúzásával, ami a bejelentkezési képernyő megjelenését eredményezi. Miután sikeresen megadta az érvényes hitelesítő adatokat, az alkalmazás megjeleníti a ToDo-elemek listáját, és frissítheti az adatokat.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin. iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
