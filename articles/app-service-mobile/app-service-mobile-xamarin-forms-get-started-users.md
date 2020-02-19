---
title: Ismerkedés a Xamarin Forms alkalmazásban való hitelesítéssel
description: Megtudhatja, hogyan használhatja a Mobile Appst a Xamarin Forms-alkalmazások felhasználóinak hitelesítésére, például a HRE, a Google, a Facebook, a Twitter és a Microsoft identitásával.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458766"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Hitelesítés hozzáadása a Xamarin Forms-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti App Service Mobile apps felhasználóit az ügyfélalkalmazás használatával. Ebben az oktatóanyagban a App Service által támogatott Identity Provider használatával hitelesítő adatokkal bővítheti a Xamarin Forms-alapú Gyorsindítás projektet. Miután a Mobile App sikeresen hitelesítette és engedélyezte a hitelesítést, a rendszer megjeleníti a felhasználói azonosító értékét, és hozzáférhet a korlátozott táblák adataihoz.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban a legjobb eredmény érdekében javasoljuk, hogy először fejezze be a [Xamarin Forms alkalmazás létrehozása][1] oktatóanyagot. Az oktatóanyag elvégzése után egy Xamarin Forms-projekt lesz, amely egy többplatformos TodoList-alkalmazás.

Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia a hitelesítési bővítmény csomagját a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben. Használhat azonban bármely kiválasztott URL-címet. Egyedinek kell lennie a Mobile-alkalmazás számára. Az átirányítás engedélyezése a kiszolgálóoldali oldalon:

1. A [Azure Portal][8]válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `url_scheme_of_your_app://easyauth.callback`.  A karakterláncban szereplő **url_scheme_of_your_app** a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Hitelesítés hozzáadása a hordozható osztály könyvtára
Mobile Apps a [LoginAsync][3] bővítmény metódust használja a [MobileServiceClient][4] egy app Service-hitelesítéssel rendelkező felhasználó bejelentkezéséhez. Ez a példa egy kiszolgáló által felügyelt hitelesítési folyamatot használ, amely megjeleníti a szolgáltató bejelentkezési felületét az alkalmazásban. További információ: [kiszolgáló által felügyelt hitelesítés][5]. Ahhoz, hogy jobb felhasználói élményt nyújtson az éles alkalmazásban, érdemes inkább az [ügyfél által felügyelt hitelesítést][6]használni.

A Xamarin űrlapos projekttel való hitelesítéshez Definiáljon egy **IAuthenticate** felületet az alkalmazáshoz tartozó hordozható osztály könyvtára alapján. Ezután vegyen fel egy **bejelentkezési** gombot a hordozható osztály könyvtárában definiált felhasználói felületre, amelyre kattintva megkezdheti a hitelesítést. A sikeres hitelesítés után az adatok betöltődik a Mobile apps-háttérből.

Implementálja az **IAuthenticate** felületet az alkalmazás által támogatott platformokon.

1. A Visual Studióban vagy a Xamarin Studióban nyissa meg a App.cs-t a projektből **a Portable néven** , amely a Portable Class Library projekt, majd adja hozzá a következő `using` utasítást:

        using System.Threading.Tasks;
2. A App.cs-ben adja hozzá a következő `IAuthenticate` Interface definition-definíciót közvetlenül az `App` osztály definíciója előtt.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Ha a felületet egy platform-specifikus implementációval szeretné inicializálni, adja hozzá a következő statikus tagokat az **app** osztályhoz.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Nyissa meg a TodoList. XAML elemet a hordozható osztály könyvtára projektből, adja hozzá a következő **gomb** elemet a *buttonsPanel* elrendezés elemhez a meglévő gomb után:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ez a gomb elindítja a kiszolgáló által felügyelt hitelesítést a Mobile apps-háttérrel.
5. Nyissa meg a TodoList.xaml.cs a hordozható osztály könyvtára projektből, majd adja hozzá a következő mezőt a `TodoList` osztályhoz:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Cserélje le a **OnAppearing** metódust a következő kódra:

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

    Ez a kód gondoskodik arról, hogy az adatok csak a hitelesítés után frissüljenek a szolgáltatásból.
7. Adja hozzá a következő kezelőt a **rákattintott** eseményhez a **TodoList** osztályhoz:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Mentse a módosításokat, és építse újra a hordozható osztály könyvtára projektet, és ellenőrizze, hogy nincsenek-e hibák.

## <a name="add-authentication-to-the-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazáshoz
Ez a szakasz bemutatja, hogyan valósítható meg a **IAuthenticate** felület az Android-alkalmazás projektben. Ugorja át ezt a szakaszt, ha nem támogatja az Android-eszközöket.

1. A Visual Studióban vagy a Xamarin Studióban kattintson a jobb gombbal a **droid** projektre, majd **állítsa be indítási projektként**.
2. Nyomja le az F5 billentyűt a projekt elindításához a hibakeresőben, majd ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401-as (nem engedélyezett) állapotkód miatt. A rendszer a 401 kódot állítja elő, mert a háttérbeli hozzáférés csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg a MainActivity.cs az Android-projektben, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítse a **MainActivity** osztályt az **IAuthenticate** felület megvalósításához a következőképpen:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Frissítse a **MainActivity** osztályt egy **MobileServiceUser** -mező és egy **hitelesítő** módszer hozzáadásával, amely az **IAuthenticate** felület számára szükséges, az alábbiak szerint:

        // Define an authenticated user.
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

    Ha a Facebooktól eltérő identitás-szolgáltatót használ, válasszon másik értéket a [MobileServiceAuthenticationProvider][7].

6. Frissítse a **AndroidManifest. XML** fájlt úgy, hogy hozzáadja a következő XML-t a `<application>` elemhez:

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
    Cserélje le a `{url_scheme_of_your_app}`t az URL-sémára.
7. Adja hozzá a következő kódot a **MainActivity** osztály **OnCreate** metódusához a `LoadApplication()`hívása előtt:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ez a kód biztosítja a hitelesítő inicializálását az alkalmazás betöltése előtt.
8. Hozza létre újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítési szolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatbázishoz hitelesített felhasználóként.

### <a name="troubleshooting"></a>Hibakeresés

**Az alkalmazás összeomlott `Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben a támogatási csomagok csak figyelmeztetésként jelennek meg a Visual Studióban, de az alkalmazás a futtatáskor összeomlik ezzel a kivétellel. Ebben az esetben meg kell győződnie arról, hogy a projektben hivatkozott összes támogatási csomag ugyanazzal a verzióval rendelkezik. Az [Azure Mobile Apps NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

## <a name="add-authentication-to-the-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazáshoz
Ez a szakasz bemutatja, hogyan valósítható meg a **IAuthenticate** felület az iOS-alkalmazás projektben. Ugorja át ezt a szakaszt, ha nem támogatja az iOS-eszközöket.

1. A Visual Studióban vagy a Xamarin Studióban kattintson a jobb gombbal az **iOS** -projektre, majd **állítsa be indítási projektként**.
2. Nyomja le az F5 billentyűt a projekt elindításához a hibakeresőben, majd ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401-as (nem engedélyezett) állapotkód miatt. Az 401-es válasz azért jön létre, mert a háttérbeli hozzáférés csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg a AppDelegate.cs az iOS-projektben, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítse a **AppDelegate** osztályt az **IAuthenticate** felület megvalósításához a következőképpen:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Frissítse a **AppDelegate** osztályt egy **MobileServiceUser** -mező és egy **hitelesítő** módszer hozzáadásával, amely az **IAuthenticate** felület számára szükséges, az alábbiak szerint:

        // Define an authenticated user.
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
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Ha a Facebooktól eltérő identitás-szolgáltatót használ, válasszon másik értéket a következőhöz: [MobileServiceAuthenticationProvider].
    
6. Frissítse a **AppDelegate** osztályt úgy, hogy hozzáadja a **OpenUrl** metódus túlterhelését az alábbiak szerint:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adja hozzá a következő sort a **FinishedLaunching** metódushoz a `LoadApplication()`hívása előtt:

        App.Init(this);

    Ez a kód biztosítja a hitelesítő inicializálását az alkalmazás betöltése előtt.

8. Nyisson meg egy info. plist fájlt, és adjon hozzá egy **URL-típust**. Állítsa be az **azonosítót** a választott névre, az alkalmazás URL-sémájának **URL-** sémájára, valamint a **szerepkört** a none értékre.

9. Hozza létre újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítési szolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatbázishoz hitelesített felhasználóként.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Hitelesítés hozzáadása a Windows 10-es (beleértve a telefonos) alkalmazások projektjeihez
Ez a szakasz bemutatja, hogyan valósítható meg a **IAuthenticate** felület a Windows 10 alkalmazás projektjeiben. Ugyanezek a lépések érvényesek Univerzális Windows-platform-(UWP-) projektekre, de a **UWP** -projekt használatával (a megjegyezett módosításokkal). Ugorja át ezt a szakaszt, ha nem támogatja a Windows-eszközöket.

1. A Visual Studióban kattintson a jobb gombbal a **UWP** projektre, majd **állítsa be indítási projektként**.
2. Nyomja le az F5 billentyűt a projekt elindításához a hibakeresőben, majd ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401-as (nem engedélyezett) állapotkód miatt. Az 401-es válasz azért fordul elő, mert a háttérbeli hozzáférés csak a jogosult felhasználókra korlátozódik.
3. Nyissa meg a MainPage.xaml.cs a Windows-alkalmazás projekthez, és adja hozzá a következő `using` utasításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Cserélje le a `<your_Portable_Class_Library_namespace>`t a hordozható osztály-függvénytár névterére.
4. Frissítse a **Főoldal** osztályt a **IAuthenticate** felület megvalósításához a következőképpen:

        public sealed partial class MainPage : IAuthenticate
5. Frissítse a főkiszolgálói **osztályt** egy **MobileServiceUser** -mező és egy **hitelesítő** módszer hozzáadásával, amely az **IAuthenticate** felület számára szükséges, az alábbiak szerint:

        // Define an authenticated user.
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

    Ha a Facebooktól eltérő identitás-szolgáltatót használ, válasszon másik értéket a [MobileServiceAuthenticationProvider][7].

1. A `LoadApplication()`hívása előtt adja hozzá a következő kódrészletet a **(** z) Főosztály konstruktorához:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Cserélje le a `<your_Portable_Class_Library_namespace>`t a hordozható osztály-függvénytár névterére.

3. Ha a **UWP**-t használja, adja hozzá a következő **OnActivated** -metódus felülbírálását az **app** osztályhoz:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Nyissa meg a Package. appxmanifest, és adjon hozzá egy **protokoll** -deklarációt. Állítsa a **megjelenítendő nevet** a választott névre, és adja meg az alkalmazás URL-sémájának **nevét** .

4. Hozza létre újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítési szolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatbázishoz hitelesített felhasználóként.

## <a name="next-steps"></a>Következő lépések
Most, hogy elvégezte ezt az alapszintű hitelesítési oktatóanyagot, tekintse át a következő oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)

  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy az adatmegjelenítéssel,-hozzáadással vagy-módosítással, még akkor is, ha nincs hálózati kapcsolat.

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
