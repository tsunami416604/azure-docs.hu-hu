---
title: Ismerkedés a Mobile Apps Xamarin IOS-hitelesítés
description: Útmutató a Mobile Apps segítségével hitelesíti a felhasználókat identitás-szolgáltatóktól, beleértve az aad-ben, a Google, a Facebook, a Twitter és a Microsoft számos a Xamarin IOS-es alkalmazás.
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
ms.openlocfilehash: c2b77d7c9caab1c7ac13af5ccd50e992bcce1caf
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31787658"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Bővítse Xamarin.iOS-alkalmazását hitelesítési funkcióval
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan hitelesítheti egy App Service Mobile Apps az ügyfélalkalmazás felhasználóit. Ebben az oktatóanyagban hozzáadja hitelesítési gyorsútmutató-projekt Xamarin.iOS App Service által támogatott identitásszolgáltató használatával. Miután sikeresen alatt hitelesítése és engedélyezése a Mobile Apps által, a felhasználói azonosító érték jelenik meg, és fogja tudni elérni a korlátozott tábla adatai.

Először el kell végeznie az oktatóanyag [Xamarin.iOS-alkalmazás létrehozása]. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hitelesítési kiterjesztés csomag fel a projektbe. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service szolgáltatások
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Biztonságos hitelesítéshez az szükséges, hogy az alkalmazás adja meg egy új URL-sémát. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma használjuk _appname_ egész. Bármely választja URL-sémát is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgáló oldalán engedélyezése:

1. Válassza ki az App Service az [Azure-portálon].

2. Kattintson a **hitelesítési / engedélyezési** menüjét.

3. Az a **engedélyezett külső átirányítási URL-t**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** ezt a karakterláncot a rendszer az URL-séma, a mobilalkalmazás.  Akkor érdemes követnie normál URL-cím meghatározása (használata betűket és számokat csak, és betűvel kezdődik) protokoll.  Meg kell győződnie, jegyezze fel a karakterláncot, amely úgy dönt, mivel úgy, hogy az URL-séma több helyen a mobilalkalmazás kódot kell.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. A Visual Studio és Xamarin Studio futtatható az ügyfélprojekt egy eszközt vagy emulátort. Győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. A problémát a hibakereső konzoljába naplózza. Ezért a Visual Studio kell megjelennie a hiba a kimeneti ablakban.

&nbsp;&nbsp;A jogosulatlan hiba akkor fordul elő, mert az alkalmazás megpróbál hozzáférni a mobil-háttéralkalmazást, nem hitelesített felhasználónak. A *TodoItem* tábla most hitelesítést igényel.

A következő később frissíteni az ügyfélalkalmazás az erőforrásokat a Mobile Apps-háttéralkalmazás a hitelesített felhasználók.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
Ebben a szakaszban az alkalmazás egy bejelentkezési képernyőt megjelenítendő adatok megjelenítése előtt fog módosítani. Az alkalmazás indításakor nem nem csatlakoznak az App Service, és nem jelenik meg az adatokat. Az első alkalommal, amikor a felhasználó hajtja végre a frissítési kézmozdulat a bejelentkezési képernyőn megjelenik; sikeres bejelentkezés után a teendőlista elemeinek listája jelenik meg.

1. Az ügyfél projektben nyissa meg a fájl **QSTodoService.cs** és adja hozzá a következő utasítás használatával és `MobileServiceUser` az elérő QSTodoService osztályra:
 
        using UIKit;
       
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Adja hozzá nevű új metódust **hitelesítés** való **QSTodoService** a következő definícióját:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                AppDelegate.ResumeWithURL = url => url.Scheme == "zumoe2etestapp" && client.ResumeWithURL(url);
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Ha használja az identitásszolgáltató eltérő a Facebook, módosítsa az értéket, átadott **LoginAsync** fent a következőhöz: _MicrosoftAccount_, _Twitter_,  _Google_, vagy _WindowsAzureActiveDirectory_.

3. Nyissa meg **QSTodoListViewController.cs**. Módosítsa a metódus definícióját **ViewDidLoad** hívása eltávolítása **RefreshAsync()** vége:
   
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
4. A módszer módosításához **RefreshAsync** – Ha a **felhasználói** tulajdonsága null értékű. A metódusdefiníciót tetején adja hozzá a következő kódot:
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate(this);
            if (todoService.User == null) {
                Console.WriteLine("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
5. Nyissa meg **AppDelegate.cs**, adja hozzá a következő metódust:

        public static Func<NSUrl, bool> ResumeWithURL;

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return ResumeWithURL != null && ResumeWithURL(url);
        }
6. Nyissa meg **Info.plist** fájlt, keresse meg **URL-cím típusú** a a **speciális** szakasz. Mostantól konfigurálhatja a **azonosító** és a **URL-sémákat** a URL-típust, és kattintson a **URL-típus hozzáadása**. **URL-sémákat** meg kell egyeznie a {url_scheme_of_your_app}.
7. A Visual Studio, a kapcsolat a Mac-gazdagép vagy a Visual Studio Mac futtassa az ügyfélprojekt célzó egy eszközt vagy emulátort. Győződjön meg arról, hogy az alkalmazás nem jelennek meg adatok.
   
    Hajtsa végre a frissítési kézmozdulat modulba húzza le az elemeket, és emiatt a bejelentkezési képernyő jelenik meg. Miután sikeresen megadta az érvényes hitelesítő adatokat, az alkalmazás a teendőlista elemeinek listáját jeleníti meg, és frissíthetik az adatokat.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
