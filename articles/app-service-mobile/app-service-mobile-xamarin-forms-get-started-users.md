---
title: A hitelesítés első lépései a Xamarin Forms alkalmazásban
description: Ismerje meg, hogyan hitelesítheti a Xamarin Forms alkalmazás felhasználóit olyan identitásszolgáltatókkal, mint az AAD, a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458766"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Hitelesítés hozzáadása a Xamarin Forms alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti az App Service Mobile-alkalmazás felhasználóit az ügyfélalkalmazásból. Ebben az oktatóanyagban az App Service által támogatott identitásszolgáltató használatával adja hozzá a hitelesítést a Xamarin Forms gyorsindítási projekthez. Miután a mobilalkalmazás sikeresen hitelesítette és engedélyezte, megjelenik a felhasználói azonosító értéke, és hozzáférhet a korlátozott táblaadatokhoz.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag legjobb eredménye érdekében azt javasoljuk, hogy először fejezze be a [Xamarin-űrlapok létrehozása alkalmazást][1] oktatóanyagot. Miután befejezte ezt az oktatóanyagot, lesz egy Xamarin Forms projektje, amely egy többplatformos TodoList alkalmazás.

Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia a hitelesítési bővítménycsomagot a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.][2]

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Az alkalmazás regisztrálása hitelesítésre és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba. Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk. Azonban bármilyen URL-sémát használhat. Meg kell egyedi a mobil alkalmazás. Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon][8]válassza ki az App Service.In the Azure Portal , select your App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `url_scheme_of_your_app://easyauth.callback`  A **karakterlánc url_scheme_of_your_app** a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

4. Kattintson az **OK** gombra.

5. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Hitelesítés hozzáadása a hordozható osztálytárhoz
A Mobile Apps a [MobileServiceClient][4] [LoginAsync][3] bővítménymetódusát használja az App Service-hitelesítéssel rendelkező felhasználó bejelentkezéséhez. Ez a minta egy kiszolgáló által felügyelt hitelesítési folyamatot használ, amely megjeleníti a szolgáltató bejelentkezési felületét az alkalmazásban. További információt a [Kiszolgáló által felügyelt hitelesítés][5]című témakörben talál. Az éles alkalmazás jobb felhasználói élményének biztosítása érdekében fontolja meg [az ügyfél által felügyelt hitelesítés][6]használatát.

A Xamarin Forms projekttel való hitelesítéshez definiáljon egy **IAuthenticate** felületet az alkalmazás Portable Class Library könyvtárában. Ezután adjon hozzá egy **bejelentkezési** gombot a Hordozható osztálytárban definiált felhasználói felülethez, amelyre a hitelesítés elindításához kattint. Az adatok a sikeres hitelesítés után a mobilalkalmazás háttérrendszeréből töltődnek be.

Valósítsa meg az **IAuthenticate** felületet az alkalmazás által támogatott minden platformhoz.

1. A Visual Studio vagy a Xamarin Studio alkalmazásban nyissa meg a **App.cs** a projektből `using` a Portable nevet, amely a Portable Class Library projekt, majd adja hozzá a következő utasítást:

        using System.Threading.Tasks;
2. A App.cs adja `IAuthenticate` hozzá a következő `App` felületdefiníciót közvetlenül az osztálydefiníció előtt.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. A felület platformspecifikus implementációval történő inicializálásához adja hozzá a következő statikus tagokat az **alkalmazásosztályhoz.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Nyissa meg a TodoList.xaml fájlt a Portable Class Library projektből, és a meglévő gomb után adja hozzá a következő **gombelemet** a *buttonsPanel* elrendezési eleméhez:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ez a gomb elindítja a kiszolgáló által felügyelt hitelesítést a mobilalkalmazás-háttérrendszerrel.
5. Nyissa meg TodoList.xaml.cs a Portable Class Library projektből, majd adja hozzá a következő mezőt az `TodoList` osztályhoz:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Cserélje le az **OnAppearing metódust** a következő kódra:

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
7. Adja hozzá a **Kattintásra szolgáló** esemény következő kezelőjét a **TodoList** osztályhoz:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Mentse a módosításokat, és építse újra a Portable Class Library projektet, amely nem tartalmaz hibákat.

## <a name="add-authentication-to-the-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazáshoz
Ez a szakasz bemutatja, hogyan valósítható meg az **IAuthenticate** felület az Android alkalmazásprojektben. Hagyja ki ezt a szakaszt, ha nem támogatja az Android-eszközöket.

1. A Visual Studio vagy a Xamarin Studio alkalmazásban kattintson a jobb gombbal a **droid** projektre, majd **állítsa be startup projektként**.
2. Nyomja le az F5 billentyűt a projekt hibakeresőben való elindításához, majd ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. A 401-es kód azért jön létre, mert a háttérrendszer hozzáférése csak a jogosult felhasználók számára engedélyezett.
3. Nyissa meg MainActivity.cs az Android-projektben, és adja hozzá a következő `using` állításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítse a **MainActivity** osztályt az **IAuthenticate** felület megvalósításához, az alábbiak szerint:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Frissítse a **MainActivity** osztályt egy **MobileServiceUser** mező és egy **Authenticate** metódus hozzáadásával, amelyre az **IAuthenticate** felületnek szüksége van, az alábbiak szerint:

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

    Ha nem a Facebookot használ, válasszon másik értéket a [MobileServiceAuthenticationProvider eszközhöz.][7]

6. Frissítse az **AndroidManifest.xml** fájlt a következő `<application>` XML hozzáadásával az elembe:

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
    Cserélje `{url_scheme_of_your_app}` le az URL-sémát.
7. Adja hozzá a következő kódot a **MainActivity** osztály `LoadApplication()` **OnCreate** metódusához a hívás előtt:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ez a kód biztosítja, hogy a hitelesítő inicializálása az alkalmazás betöltése előtt.
8. Építse újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítésszolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatokhoz hitelesített felhasználóként.

### <a name="troubleshooting"></a>Hibaelhárítás

**Az alkalmazás összeomlott a`Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben ütközések a támogatási csomagokban csak figyelmeztetésként jelennek meg a Visual stúdióban, de az alkalmazás futásidőben összeomlik ezzel a kivétellel. Ebben az esetben meg kell győződnie arról, hogy a projektben hivatkozott összes támogatási csomag azonos verzióval rendelkezik. Az [Azure Mobile Apps NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

## <a name="add-authentication-to-the-ios-app"></a>Hitelesítés hozzáadása az iOS-alkalmazáshoz
Ez a szakasz bemutatja, hogyan valósítható meg az **IAuthenticate** felület az iOS-alkalmazásprojektben. Hagyja ki ezt a szakaszt, ha nem támogatja az iOS-eszközöket.

1. A Visual Studio vagy a Xamarin Studio alkalmazásban kattintson a jobb gombbal az **iOS-projektre,** majd **állítsa be a StartUp Project programot.**
2. Nyomja le az F5 billentyűt a projekt hibakeresőben való elindításához, majd ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. A 401-es válasz azért jön létre, mert a háttérrendszer hozzáférése csak a jogosult felhasználók számára engedélyezett.
3. Nyissa meg AppDelegate.cs az iOS-projektben, és adja hozzá a következő `using` állításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Frissítse az **AppDelegate** osztályt az **IAuthenticate** felület megvalósításához, az alábbiak szerint:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Frissítse az **AppDelegate** osztályt egy **MobileServiceUser** mező és egy **Authenticate** metódus hozzáadásával, amelyre az **IAuthenticate** felületnek szüksége van, az alábbiak szerint:

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

    Ha nem a Facebookot használ, válasszon másik értéket a [MobileServiceAuthenticationProvider] számára.
    
6. Frissítse az **AppDelegate** osztályt az **OpenUrl** metódus túlterhelésének hozzáadásával, az alábbiak szerint:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. A hívás előtt adja hozzá a következő kódsort a **FinishedLaunch** metódushoz: `LoadApplication()`

        App.Init(this);

    Ez a kód biztosítja, hogy a hitelesítő inicializálása az alkalmazás betöltése előtt.

8. Nyissa meg az Info.plist webhelyet, és adjon hozzá egy **URL-típust**. Állítsa be az **azonosítót** az Ön által választott névre, az **alkalmazás URL-séma URL-sémákra,** és a **Szerepkör nincs** re.

9. Építse újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítésszolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatokhoz hitelesített felhasználóként.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Hitelesítés hozzáadása a Windows 10 -hez (beleértve a Telefont is) alkalmazásprojektekhez
Ez a szakasz bemutatja, hogyan valósítható meg az **IAuthenticate** felület a Windows 10 alkalmazásprojektekben. Ugyanezek a lépések vonatkoznak az univerzális Windows-platform (UWP) projektekre is, de az **UWP-projekt** használatával (a jelzett módosításokkal). Hagyja ki ezt a szakaszt, ha nem támogatja a Windows-eszközöket.

1. A Visual Studio alkalmazásban **UWP** kattintson a jobb gombbal az UWP-projektre, majd **állítsa be indítási projektként**.
2. Nyomja le az F5 billentyűt a projekt hibakeresőben való elindításához, majd ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. A 401-es válasz azért történik, mert a háttérrendszer hozzáférése csak a jogosult felhasználók számára engedélyezett.
3. Nyissa meg a Windows alkalmazásprojekt `using` MainPage.xaml.cs, és adja hozzá a következő állításokat:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Cserélje `<your_Portable_Class_Library_namespace>` le a hordozható osztálytár névterére.
4. Frissítse a **MainPage** osztályt az **IAuthenticate** felület megvalósításához, az alábbiak szerint:

        public sealed partial class MainPage : IAuthenticate
5. Frissítse a **MainPage** osztályt egy **MobileServiceUser** mező és egy **Authenticate** metódus hozzáadásával, amelyre az **IAuthenticate** felületnek szüksége van, az alábbiak szerint:

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

    Ha nem a Facebookot használ, válasszon másik értéket a [MobileServiceAuthenticationProvider eszközhöz.][7]

1. A hívás előtt adja hozzá a **MainPage** osztály konstruktorához `LoadApplication()`a következő kódsort:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Cserélje `<your_Portable_Class_Library_namespace>` le a hordozható osztálytár névterére.

3. Ha **UWP-t**használ, adja hozzá a következő **OnActivated** metódus-felülbírálást az **alkalmazásosztályhoz:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Nyissa meg a Package.appxmanifest fájlt, és adjon hozzá egy protokolldeklarációt. **Protocol** Állítsa be a **Megjelenítendő nevet** az Ön által választott névre, és a **Nevet** az Ön URL-sémába.

4. Építse újra az alkalmazást, futtassa, majd jelentkezzen be a kiválasztott hitelesítésszolgáltatóval, és ellenőrizze, hogy képes-e hozzáférni az adatokhoz hitelesített felhasználóként.

## <a name="next-steps"></a>További lépések
Most, hogy befejezte ezt az alapszintű hitelesítési oktatóanyagot, fontolja meg az alábbi oktatóanyagok egyikének folytatását:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)

  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy interakcióba lépjenek egy mobilalkalmazással – adatok megtekintése, hozzáadása vagy módosítása – még akkor is, ha nincs hálózati kapcsolat.

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
