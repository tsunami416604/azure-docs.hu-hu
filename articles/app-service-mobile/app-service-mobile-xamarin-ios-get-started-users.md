---
title: Ismerkedés a Xamarin IOS Mobile Apps hitelesítése
description: Ismerje meg, hogyan hitelesítheti a felhasználókat a Xamarin iOS-alkalmazás identitás-szolgáltatóktól, többek között az aad-ben, Google, Facebook, Twitter és a Microsoft számos, a Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326170"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Bővítse Xamarin.iOS-alkalmazását hitelesítési funkcióval
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan hitelesítheti felhasználóit egy App Service Mobile Apps, az ügyfél-alkalmazásból. Ebben az oktatóanyagban hozzáadja a Xamarin.iOS példaprojekthez egy identitásszolgáltatótól az App Service által támogatott hitelesítési. Folyamatban sikeresen hitelesíti és a Mobile Apps, a felhasználói azonosító értéke megjelenik, és lesz korlátozva táblák adatainak eléréséhez.

Először végezze el az oktatóanyag [Xamarin.iOS-alkalmazás létrehozása]. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a hitelesítési kiterjesztés csomagot a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Regisztrálja az alkalmazást a hitelesítés és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Hitelesítésre van szükség, hogy az alkalmazás egy új URL-séma meghatározása. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejeződése után. Ebben az oktatóanyagban azt használja az URL-séma _appname_ során. Bármely URL-séma választja is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgálói oldalon az átirányítás engedélyezése:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüpont.

3. Az a **engedélyezett külső átirányítási URL-címeket**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** Ez a karakterlánc a következő az URL-séma esetében.  Normál URL-cím-specifikáció (használata betűket és csak számokból állhat, és betűvel kezdődhet) protokoll, érdemes követnie.  Meg kell jegyezze fel a karakterlánc, amely úgy dönt, mert szüksége lesz a mobilalkalmazás-kód az URL-séma több helyen való beállításához.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* A Visual Studióban vagy a Xamarin Studióban futtassa az eszközt vagy emulátort a. Győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. A hiba kerül a hibakeresőt a konzolja felé. Így a Visual Studióban láthatja a hibát a kimeneti ablakban.

    Ez nem engedélyezett a hiba oka, hogy az alkalmazást próbál meg hozzáférni egy nem hitelesített felhasználóként a mobilalkalmazás háttérrendszerének. A *TodoItem* táblázat most már hitelesítést igényel.

Ezután az ügyfélalkalmazás szeretne frissíteni erőforrás-kérelmek a Mobile Apps-háttéralkalmazást a hitelesített felhasználóval.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
Ebben a szakaszban fog módosítani az alkalmazás adatainak megjelenítése előtt egy bejelentkezési képernyő megjelenítéséhez. Az alkalmazás indításakor nem tudnak csatlakozni az App Service, és nem jelennek meg adatok. Miután az első alkalommal, amikor a felhasználó hajt végre a frissítési jelentkezhessenek be a bejelentkezési képernyőn jelenik meg; sikeres bejelentkezés után a teendőlista elemeinek listája jelenik meg.

1. Az ügyfél-projektben nyissa meg a fájlt **QSTodoService.cs** , és adja hozzá a következő using utasítást, és `MobileServiceUser` hozzáférő a QSTodoService osztályhoz az:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adja hozzá az új metódust **hitelesítés** való **QSTodoService** az alábbi definíciójában:

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
    > Ha eltérő egy Facebook-identitásszolgáltató használ, módosítsa az értéket, átadott **LoginAsync** felett, a következők egyikét: _MicrosoftAccount_, _Twitter_, _Google_, vagy _WindowsAzureActiveDirectory_.

3. Nyissa meg **QSTodoListViewController.cs**. Módosítsa a metódus definícióját **ViewDidLoad** eltávolítása a hívást **RefreshAsync()** a végfelhasználók közelében:

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

4. A módszer módosításához **RefreshAsync** – Ha a **felhasználói** tulajdonság null értékű. A metódusdefiníciót tetején adja hozzá a következő kódot:

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

5. Nyissa meg **AppDelegate.cs**, adja hozzá a következő metódust:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Nyissa meg **Info.plist** fájlt, lépjen a **URL-cím-típusok** a a **speciális** szakaszban. Mostantól konfigurálhatja az **azonosító** és a **URL-sémákat** a URL-cím típusa, és kattintson a **URL-cím-típus hozzáadása**. **URL-sémákat** legyen ugyanaz, mint a {url_scheme_of_your_app}.
7. A Visual Studióban, a Mac-gazdagépről vagy a Visual Studio csatlakoztatott Mac számítógépen futtassa az ügyfél-projektet egy eszközt vagy emulátort. Győződjön meg arról, hogy az alkalmazás nem jelennek meg adatok.

    Hajtsa végre a frissítési kézmozdulat kiindulásként lefelé a listában, a cikkek, amelyek a bejelentkezési képernyő megjelenését, okoz. Miután sikeresen megadta érvényes hitelesítő adatokat, az alkalmazás a teendőlista elemeinek listáját jeleníti meg, és az adatok lehetővé teszi a frissítések.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
