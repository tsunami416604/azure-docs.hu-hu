---
title: Ismerkedés a Mobile Apps hitelesítése a Xamarin Forms-alkalmazás |} A Microsoft Docs
description: Ismerje meg, hogyan hitelesítheti a felhasználókat az identitás-szolgáltatóktól, többek között az aad-ben, Google, Facebook, Twitter és a Microsoft számos Xamarin Forms-alkalmazás a Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: f7e500fb5856c7eec48a371042244b44dd944779
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063774"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Hitelesítés hozzáadása a Xamarin Forms-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti felhasználóit egy App Service Mobile Apps, az ügyfél-alkalmazásból. Ebben az oktatóanyagban hozzáadja a Xamarin Forms eszközkészlethez példaprojekthez egy identitásszolgáltatótól az App Service által támogatott hitelesítési. Folyamatban sikeresen hitelesíti és a Mobile Apps, a felhasználói azonosító értéke megjelenik, és lesz korlátozva táblák adatainak eléréséhez.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban, a legjobb eredmény érdekében ajánlott először végezzen a [Xamarin Forms-alkalmazás létrehozása] [ 1] oktatóanyag. Az oktatóanyag elvégzéséhez után egy Xamarin Forms-projektet, amely egy többplatformos TodoList alkalmazás.

Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a hitelesítési kiterjesztés csomagot a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Regisztrálja az alkalmazást a hitelesítés és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Hitelesítésre van szükség, hogy az alkalmazás egy új URL-séma meghatározása. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejeződése után. Ebben az oktatóanyagban azt használja az URL-séma _appname_ során. Bármely URL-séma választja is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgálói oldalon az átirányítás engedélyezése:

1. Az a [az Azure portal][8], válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüpont.

3. Az a **engedélyezett külső átirányítási URL-címeket**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** Ez a karakterlánc a következő az URL-séma esetében.  Normál URL-cím-specifikáció (használata betűket és csak számokból állhat, és betűvel kezdődhet) protokoll, érdemes követnie.  Meg kell jegyezze fel a karakterlánc, amely úgy dönt, mert szüksége lesz a mobilalkalmazás-kód az URL-séma több helyen való beállításához.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Hitelesítés hozzáadása a hordozható osztálytárat
Mobile Apps használja a [LoginAsync] [ 3] a metódust a [MobileServiceClient] [ 4] bejelentkezni egy felhasználó az App Service használatával a hitelesítés. Ez a minta egy hitelesítési kiszolgáló által felügyelt folyamatot, amely a szolgáltató bejelentkezési felületen jeleníti meg az alkalmazás használja. További információkért lásd: [hitelesítési kiszolgáló által kezelt][5]. Az éles alkalmazásban jobb felhasználói élmény biztosítása érdekében érdemes lehet inkább használatával [hitelesítési ügyfél által felügyelt][6].

A Xamarin Forms eszközkészlethez projekt hitelesítéshez, adja meg egy **IAuthenticate** az alkalmazás a hordozható osztálytár felületének. Majd adja hozzá a **bejelentkezési** gombra a felhasználói felületen meghatározott a hordozható osztálytár, amelyre kattintva indítsa el a hitelesítést. Adatok betöltése a mobil-háttéralkalmazást a sikeres hitelesítés után.

Alkalmazzon a **IAuthenticate** felület az alkalmazás által támogatott platformokon.

1. A Visual Studióban vagy a Xamarin Studióban, megnyitása a projektből az App.cs **hordozható** a neve, amely hordozható osztálytár projektet, majd adja hozzá a következő `using` utasítást:

        using System.Threading.Tasks;
2. A App.cs, adja hozzá a következő `IAuthenticate` felület deklarációja közvetlenül a `App` osztálydefiníciót.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. A felület egy platformspecifikus megvalósításra inicializálása, a következő statikus tagok hozzáadására a **alkalmazás** osztály.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. TodoList.xaml nyissa meg a hordozható osztálytár projektet, adja hozzá a következő **gomb** eleme a *buttonsPanel* elrendezés elem után a meglévő gombra:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ez a gomb elindít hitelesítési kiszolgáló által felügyelt és a mobile Apps-háttéralkalmazást.
5. Nyissa meg a hordozható osztálytár projektek TodoList.xaml.cs, majd adja hozzá a következő mező a `TodoList` osztály:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Cserélje le a **OnAppearing** módszer a következő kóddal:

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
7. Adja hozzá a következő kezelő a **gombos kattintással elérhető** esemény a **TodoList** osztály:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Mentse a módosításokat, és építse újra a hibák ellenőrzése hordozható osztálytár projektet.

## <a name="add-authentication-to-the-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazás
Ez a szakasz bemutatja, hogyan valósíthat meg a **IAuthenticate** az Android-alkalmazás projekt felületet. Kihagyhatja ezt a szakaszt, ha nem támogatja a Android-eszközökön.

1. A Visual Studióban vagy a Xamarin Studióban, kattintson a jobb gombbal a **droid** projektre, majd **Set as StartUp Project**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakeresőt, majd győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. A 401-es kód jön létre, mert a háttérbeli hozzáférés csak a jogosult felhasználókra korlátozódik.
3. MainActivity.cs nyissa meg az Android-projektre, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítés a **MainActivity** osztályba a **IAuthenticate** felület, a következő:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Frissítés a **MainActivity** hozzáadásával osztály egy **MobileServiceUser** mezőt és a egy **hitelesítés** módszer, amelyek szükségesek a **IAuthenticate** felület, a következő:

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

    Eltérő Facebook identitásszolgáltatót használja, ha az eltérő értéket választ [MobileServiceAuthenticationProvider][7].

6. Frissítés a **AndroidManifest.xml** fájlban adja hozzá a következő XML formátumú belül a `<application>` elem:

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
    Cserélje le `{url_scheme_of_your_app}` az URL-cím sémával.
7. Adja hozzá a következő kódot a **OnCreate** módszere a **MainActivity** hívása előtt osztály `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ez a kód biztosítja, hogy a hitelesítő alkalmazás terhelés előtt inicializálva van.
8. Építse újra az alkalmazást, futtassa, és jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze, érhessék el az adatokat egy hitelesített felhasználóként.

### <a name="troubleshooting"></a>Hibaelhárítás

**Az alkalmazás az összeomlott `Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben ütközik a támogatási csomagok, csak a Visual studio, de ehhez a kivételhez futásidőben az alkalmazás-összeomlásokat figyelmeztetés jelenik meg. Ebben az esetben kell győződjön meg arról, hogy a projekt hivatkozott összes támogatási csomag verziójával rendelkezik-e. A [Azure Mobile Apps NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) rendelkezik `Xamarin.Android.Support.CustomTabs` függőséget az Android platformhoz, így ha a projekt újabb használja a támogatási csomagok, a csomag telepítéséhez szükséges verziójú ütközések elkerülése érdekében közvetlenül kell.

## <a name="add-authentication-to-the-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazás
Ez a szakasz bemutatja, hogyan valósíthat meg a **IAuthenticate** felületének az iOS-alkalmazás projekt. Kihagyhatja ezt a szakaszt, ha nem támogatja az iOS-eszközök.

1. A Visual Studióban vagy a Xamarin Studióban, kattintson a jobb gombbal a **iOS** projektre, majd **Set as StartUp Project**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakeresőt, majd győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. A 401-es választ jön létre, mert a háttérbeli hozzáférés csak a jogosult felhasználókra korlátozódik.
3. AppDelegate.cs nyissa meg az iOS-projektre, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítés a **AppDelegate** osztályba a **IAuthenticate** felület, a következő:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Frissítés a **AppDelegate** hozzáadásával osztály egy **MobileServiceUser** mezőt és a egy **hitelesítés** módszer, amelyek szükségesek a **IAuthenticate** felület, a következő:

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

    Ha eltérő Facebook identitásszolgáltatót használja, egy másik értéket választania [MobileServiceAuthenticationProvider].
    
6. Frissítés a **AppDelegate** osztályban adja hozzá a **OpenUrl** metódus túlterhelni a következő:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adja hozzá a következő kódsort a **FinishedLaunching** metódus hívása előtt `LoadApplication()`:

        App.Init(this);

    Ez a kód biztosítja, hogy a hitelesítő inicializálva van, mielőtt az alkalmazás következő betöltésekor.

8. Nyissa meg az info.plist fájlban, és adja hozzá a **URL-cím típusa**. Állítsa be a **azonosítója** megszakíthatja, egy nevet, a **URL-sémákat** a az alkalmazás URL-sémát, és a **szerepkör** None értékre.

9. Építse újra az alkalmazást, futtassa, és jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze, érhessék el az adatokat egy hitelesített felhasználóként.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Hitelesítés hozzáadása a Windows 10 (beleértve a telefonszám) alkalmazás projektek
Ez a szakasz bemutatja, hogyan valósíthat meg a **IAuthenticate** felület a Windows 10-es alkalmazás projektek. Univerzális Windows Platform (UWP) projektek, de a használata a alkalmazni ugyanazokat a lépéseket a **UWP** a projekt (feljegyzett módosításokat). Kihagyhatja ezt a szakaszt, ha nem támogatja a Windows-eszközök.

1. A Visual Studióban kattintson a jobb gombbal a **UWP** projektre, majd **Set as StartUp Project**.
2. Nyomja le az F5 billentyűt a projekt indításához a hibakeresőt, majd győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. A 401-es választ akkor fordul elő, mert csak a jogosult felhasználók hozzáférésének a háttéralkalmazás korlátozódik.
3. Nyissa meg a MainPage.xaml.cs számára a Windows-projektet, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Cserélje le `<your_Portable_Class_Library_namespace>` a névtérhez a hordozható osztálytárat számára.
4. Frissítés a **MainPage** osztályba a **IAuthenticate** felület, a következő:

        public sealed partial class MainPage : IAuthenticate
5. Frissítés a **MainPage** hozzáadásával osztály egy **MobileServiceUser** mezőt és a egy **hitelesítés** módszer, amelyek szükségesek a **IAuthenticate**felület, a következő:

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

    Eltérő Facebook identitásszolgáltatót használja, ha az eltérő értéket választ [MobileServiceAuthenticationProvider][7].

1. Adja hozzá a következő kódsort konstruktorában a **MainPage** hívása előtt osztály `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Cserélje le `<your_Portable_Class_Library_namespace>` a névtérhez a hordozható osztálytárat számára.

3. Ha használ **UWP**, adja hozzá a következő **OnActivated** metódus bírálja felül, ha a **alkalmazás** osztály:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Nyissa meg a Package.appxmanifest, és adjon hozzá egy **protokoll** nyilatkozatot. Állítsa be a **megjelenített név** megszakíthatja, egy nevet, és a **neve** , az URL-séma alkalmazáshoz.

4. Építse újra az alkalmazást, futtassa, és jelentkezzen be a hitelesítési szolgáltatót választotta, és ellenőrizze, érhessék el az adatokat egy hitelesített felhasználóként.

## <a name="next-steps"></a>További lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, vegye figyelembe a folyamatos be az alábbi oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)

  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak a mobilalkalmazás - megtekintését, hozzáadását és módosítását a data - még akkor is, ha nincs hálózati kapcsolat.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
