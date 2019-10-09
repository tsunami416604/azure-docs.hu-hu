---
title: Ismerkedés a hitelesítéssel Mobile Apps Xamarin Android rendszeren
description: Megtudhatja, hogyan használhatja a Mobile Appst a Xamarin Android-alkalmazás felhasználóinak hitelesítésére különböző identitás-szolgáltatók, például a HRE, a Google, a Facebook, a Twitter és a Microsoft segítségével.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 44a6261819ee8e880d9a5763e92678bc359f5eff
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025095"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Hitelesítés hozzáadása a Xamarin. Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti a Mobile apps felhasználóit az ügyfélalkalmazás használatával. Ebben az oktatóanyagban a gyors üzembe helyezési projekthez egy Azure Mobile Apps által támogatott identitás-szolgáltató használatával ad hozzá hitelesítést. A sikeres hitelesítés és engedélyezés után a rendszer a felhasználói azonosító értékét jeleníti meg.

Ez az oktatóanyag a Mobile App rövid útmutatóján alapul. Először is végre kell hajtania a [Xamarin. Android-alkalmazás létrehozása]című oktatóanyagot. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia a hitelesítési bővítmény csomagját a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben. Használhat azonban bármely kiválasztott URL-címet. Egyedinek kell lennie a Mobile-alkalmazás számára. Az átirányítás engedélyezése a kiszolgálóoldali oldalon:

1. A [Azure Portal] területen válassza ki a App Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `url_scheme_of_your_app://easyauth.callback` értéket.  A karakterláncban szereplő **url_scheme_of_your_app** a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A Visual Studióban vagy a Xamarin Studióban futtassa az ügyfél-projektet egy eszközön vagy emulátoron. Ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401 (nem engedélyezett) állapotkóddal. Ez azért történik, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a Mobile apps-háttérhöz. A *TodoItem* táblához már hitelesítés szükséges.

Ezután frissítenie kell az ügyfélszoftvert, hogy egy hitelesített felhasználótól kérjen erőforrásokat a Mobile apps-háttérből.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
Az alkalmazás frissül, hogy a felhasználók a **Bejelentkezés** gombra koppintanak, és az adatmegjelenítés előtt hitelesítve legyenek.

1. Adja hozzá a következő kódot a **TodoActivity** osztályhoz:
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Ez egy új módszert hoz létre egy felhasználó és egy új **bejelentkezési** gomb metódus-kezelője hitelesítéséhez. A fenti példában szereplő kódot Facebook-bejelentkezéssel hitelesíti a rendszer. A rendszer egy párbeszédpanelt használ a felhasználó AZONOSÍTÓjának a hitelesítéssel történő megjelenítéséhez.
   
   > [!NOTE]
   > Ha a Facebooktól eltérő identitás-szolgáltatót használ, módosítsa az átadott értéket a **LoginAsync** fölé a következők egyikére: *MicrosoftAccount*, *Twitter*, *Google*vagy *WindowsAzureActiveDirectory*.
   > 
   > 
2. A **OnCreate** metódusban törölje vagy véleményezze a következő kódrészletet:
   
        OnRefreshItemsSelected ();
3. A Activity_To_Do. axml fájlban adja hozzá a következő *LoginUser* gomb definícióját a meglévő *additem* gomb előtt:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adja hozzá a következő elemet a Strings. XML-erőforrások fájlhoz:
   
        <string name="login_button_text">Sign in</string>
5. Nyissa meg a AndroidManifest. xml fájlt, adja hozzá a következő kódot `<application>` XML-elemen belül:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. A Visual Studióban vagy a Xamarin Studióban futtassa az ügyfél projektet egy eszközön vagy emulátoron, és jelentkezzen be a kiválasztott identitás-szolgáltatóval. Ha sikeresen bejelentkezett, az alkalmazás megjeleníti a bejelentkezési azonosítót és a ToDo-elemek listáját, és frissítheti az adatokat.

## <a name="troubleshooting"></a>Hibaelhárítás

**Az alkalmazás összeomlott `Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben a támogatási csomagok csak figyelmeztetésként jelennek meg a Visual Studióban, de az alkalmazás a futtatáskor összeomlik ezzel a kivétellel. Ebben az esetben meg kell győződnie arról, hogy a projektben hivatkozott összes támogatási csomag ugyanazzal a verzióval rendelkezik. Az [Azure Mobile Apps NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

<!-- URLs. -->
[Xamarin. Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
