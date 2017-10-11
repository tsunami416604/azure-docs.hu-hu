---
title: "Ismerkedés a Mobile Apps a Xamarin Forms app hitelesítéssel |} Microsoft Docs"
description: "Megtudhatja, hogyan hitelesíti a felhasználókat a Xamarin Forms alkalmazás Identitásszolgáltatók, beleértve az aad-ben, a Google, a Facebook, a Twitter és a Microsoft számos, a Mobile Apps segítségével."
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: syntaxc4
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: 9e14e95793bcc81ad46783fd50ba223eec4ea360
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Hitelesítés hozzáadása a Xamarin Forms alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti egy App Service Mobile Apps az ügyfélalkalmazás felhasználóit. Ebben az oktatóanyagban hozzáadja a Xamarin Forms gyorsútmutató-projekt használja az identitásszolgáltató az App Service által támogatott hitelesítési. Miután sikeresen alatt hitelesítése és engedélyezése a Mobile Apps által, a felhasználói azonosító érték jelenik meg, és fogja tudni elérni a korlátozott tábla adatai.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag a legjobb eredmény, javasoljuk, hogy hajtsa végre a [Xamarin Forms-alkalmazás létrehozása] [ 1] oktatóanyag. Ez az oktatóanyag befejezése után fog egy többplatformos TodoList alkalmazást a Xamarin Forms projektet.

Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hitelesítési kiterjesztés csomag fel a projektbe. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service szolgáltatások
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Biztonságos hitelesítéshez az szükséges, hogy az alkalmazás adja meg egy új URL-sémát. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma használjuk _appname_ egész. Bármely választja URL-sémát is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgáló oldalán engedélyezése:

1. Válassza ki az App Service az [Azure-portálon].

2. Kattintson a **hitelesítési / engedélyezési** menüjét.

3. Az a **engedélyezett külső átirányítási URL-t**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** ezt a karakterláncot a rendszer az URL-séma, a mobilalkalmazás.  Akkor érdemes követnie normál URL-cím meghatározása (használata betűket és számokat csak, és betűvel kezdődik) protokoll.  Meg kell győződnie, jegyezze fel a karakterláncot, amely úgy dönt, mivel úgy, hogy az URL-séma több helyen a mobilalkalmazás kódot kell.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Hitelesítés hozzáadása a hordozható osztálytár
Mobile Apps használja a [LoginAsync] [ 3] bővítmény metódust a [MobileServiceClient] [ 4] a felhasználó bejelentkezhet az App Service hitelesítés. Ez a minta egy hitelesítési kiszolgáló által felügyelt folyamat, amely megjeleníti a szolgáltató bejelentkezési felületen az alkalmazás használja. További információkért lásd: [hitelesítési kiszolgáló által kezelt][5]. Az éles alkalmazásban jobb felhasználói élmény biztosítása érdekében érdemes lehet helyette használatával [hitelesítési ügyfél által felügyelt][6].

A Xamarin Forms projekt hitelesítéshez, adja meg egy **IAuthenticate** felületet az alkalmazás a hordozható osztály könyvtárban. Majd adja hozzá a **bejelentkezési** a felhasználói felület gombra kattintva indítsa el a hitelesítés a felhasználó hordozható Class Library definiált. Adatok betöltése a mobil-háttéralkalmazás a sikeres hitelesítést követően.

Alkalmazzon a **IAuthenticate** felületet az alkalmazás által támogatott platformokon.

1. A Visual Studio és Xamarin Studio, nyissa meg az App.cs a projektből az **hordozható** a neve, amely hordozható Class Library projektet, majd adja hozzá a következő `using` utasítást:

        using System.Threading.Tasks;
2. A App.cs, adja hozzá a következő `IAuthenticate` felület előtt közvetlenül a `App` osztály definícióját.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. A platform-specifikus megvalósításának kapcsolaton inicializálni a következő statikus tagokat adjanak hozzá a **App** osztály.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Nyissa meg a TodoList.xaml a hordozható Class Library projekt, adja hozzá a következő **gomb** eleme a *buttonsPanel* elrendezés elem, a meglévő gomb után:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ez a gomb elindítja a hitelesítési kiszolgáló által felügyelt és a mobil-háttéralkalmazás.
5. Nyissa meg a TodoList.xaml.cs a hordozható Class Library projekt, majd adja hozzá a következő mező a `TodoList` osztály:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Cserélje le a **OnAppearing** metódus a következő kóddal:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Ez a kód gondoskodik arról, hogy az adatok csak frissülnek a szolgáltatás Ön hitelesítése után.
7. Adja hozzá a következő kezelőt a **Clicked** esemény a **TodoList** osztály:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. A módosítások mentéséhez és a hibák ellenőrzése hordozható Class Library projekt újraépítéséhez.

## <a name="add-authentication-to-the-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazás
Ez a szakasz bemutatja, hogyan megvalósításához a **IAuthenticate** az Android-alkalmazás projekt felületet. Ez a szakasz kihagyása, ha Android-eszköz nem támogatja.

1. A Visual Studio és Xamarin Studióban, kattintson a jobb gombbal a **droid** projektre, majd **beállítás kezdőprojektként**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakereső, majd győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. A 401-es kód jön létre, mert a háttérkiszolgálón hozzáférést csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg az Android-projekt MainActivity.cs, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítés a **MainActivity** osztály megvalósításához a **IAuthenticate** csatoló, az alábbiak szerint:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Frissítés a **MainActivity** hozzáadásával osztály egy **MobileServiceUser** mező és egy **hitelesítés** metódus, amely szükséges a **IAuthenticate** csatoló, az alábbiak szerint:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Ha használja az identitásszolgáltató Facebook-on kívül, adjon meg más értéket a [MobileServiceAuthenticationProvider][7].

6. Adja hozzá a következő kódot <application> AndroidManifest.xml csomópontján:

```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
```

1. Adja hozzá a következő kódot a **OnCreate** metódusában a **MainActivity** hívása előtt osztály `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ez a kód biztosítja, hogy a hitelesítő inicializálása előtt az alkalmazás terhelés.
2. Építse újra az alkalmazást, majd futtassa, majd jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze akkor érhessék el az adatokat egy hitelesített felhasználóként-e.

## <a name="add-authentication-to-the-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazás
Ez a szakasz bemutatja, hogyan megvalósításához a **IAuthenticate** az iOS-alkalmazás projekt felülettel. Ez a szakasz kihagyása, ha nem támogatja a iOS-eszközök.

1. A Visual Studio és Xamarin Studióban, kattintson a jobb gombbal a **iOS** projektre, majd **beállítás kezdőprojektként**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakereső, majd győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. A 401-es válasz jön létre, mert a háttérkiszolgálón hozzáférést csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg a AppDelegate.cs az iOS-projektre, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítés a **AppDelegate** osztály megvalósításához a **IAuthenticate** csatoló, az alábbiak szerint:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Frissítés a **AppDelegate** hozzáadásával osztály egy **MobileServiceUser** mező és egy **hitelesítés** metódus, amely szükséges a **IAuthenticate** csatoló, az alábbiak szerint:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Ha használja az identitásszolgáltató Facebook-on kívül, adjon meg más értéket a(z) [MobileServiceAuthenticationProvider].

6. A AppDelegate osztály OpenUrl (UIApplication alkalmazás által igényelt NSUrl URL-címe, NSDictionary beállítások) metódus túlterhelési hozzáadásával frissítése

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }

6. Adja hozzá a következő kódsort a **FinishedLaunching** metódus hívása előtt `LoadApplication()`:

        App.Init(this);

    Ez a kód biztosítja, hogy a hitelesítő inicializálása előtt az alkalmazáshoz be van töltve.

6. Adja hozzá **{url_scheme_of_your_app}** az URL-sémákat Info.plist-ben.

7. Építse újra az alkalmazást, majd futtassa, majd jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze akkor érhessék el az adatokat egy hitelesített felhasználóként-e.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Hitelesítés hozzáadása a Windows 10 (beleértve a telefonszám) alkalmazás projektek
Ez a szakasz bemutatja, hogyan megvalósításához a **IAuthenticate** felület a Windows 10-alkalmazást projektekben. Ugyanezek a lépések alkalmazni az univerzális Windows Platform (UWP) projektek, de a segítségével a **UWP** project (beállításértékeket módosításokkal). Ez a szakasz kihagyása, ha nem támogatja a Windows-eszközök.

1. "A Visual Studióban, kattintson a jobb gombbal bármelyik a **UWP** projektre, majd **beállítás kezdőprojektként**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakereső, majd győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. A 401-es válasz az oka, hogy a háttérkiszolgálón hozzáférést csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg a MainPage.xaml.cs a Windows-alkalmazás projekt, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Cserélje le `<your_Portable_Class_Library_namespace>` a hordozható osztály szalagtár névtérrel.
4. Frissítés a **MainPage** osztály megvalósításához a **IAuthenticate** csatoló, az alábbiak szerint:

        public sealed partial class MainPage : IAuthenticate
5. Frissítés a **MainPage** hozzáadásával osztály egy **MobileServiceUser** mező és egy **hitelesítés** metódus, amely szükséges a **IAuthenticate**csatoló, az alábbiak szerint:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Ha használja az identitásszolgáltató Facebook-on kívül, adjon meg más értéket a(z) [MobileServiceAuthenticationProvider].

1. Adja hozzá a következő kódsort konstruktorában a **MainPage** hívása előtt osztály `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Cserélje le `<your_Portable_Class_Library_namespace>` a hordozható osztály szalagtár névtérrel.

3. Ha használ **UWP**, adja hozzá a következő **OnActivated** metódus bírálja felül, ha a **App** osztály:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }

       }

   Ha a metódus-felülbírálása már létezik, adja hozzá a feltételes kódot az előző részlet.  Ez a kód nincs szükség az univerzális Windows-projektek esetében.

3. Adja hozzá **{url_scheme_of_your_app}** a Package.appxmanifest. 

4. Építse újra az alkalmazást, majd futtassa, majd jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze akkor érhessék el az adatokat egy hitelesített felhasználóként-e.

## <a name="next-steps"></a>Következő lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, fontolja meg valamelyik az alábbi oktatóanyagok folytatása:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)

  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Megtudhatja, hogyan adhat offline támogatást az alkalmazást a Mobile Apps-háttéralkalmazás segítségével. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, amellyel kommunikálni tud a mobilalkalmazás - megtekintését, hozzáadását és - adatok módosítását, akkor is, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
