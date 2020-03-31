---
title: Első lépések a xamarinandroidos hitelesítéssel
description: Ismerje meg, hogyan hitelesítheti a Xamarin Android-alkalmazás felhasználóit olyan identitásszolgáltatókkal, mint az AAD, a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458953"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Hitelesítés hozzáadása a Xamarin.Android alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ez a témakör bemutatja, hogyan hitelesítheti a mobilalkalmazás felhasználóit az ügyfélalkalmazásból. Ebben az oktatóanyagban az Azure Mobile Apps által támogatott identitásszolgáltató használatával adja hozzá a gyorsindítási projekt hitelesítését. Miután sikeresen hitelesítette és engedélyezte a mobilalkalmazásban, megjelenik a felhasználói azonosító értéke.

Ez az oktatóanyag a mobilalkalmazás rövid útmutatóján alapul. Először is el kell végeznie a [Xamarin.Android alkalmazás létrehozása című oktatóanyagot.] Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia a hitelesítési bővítménycsomagot a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## <a name="register-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Az alkalmazás regisztrálása hitelesítésre és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba. Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk. Azonban bármilyen URL-sémát használhat. Meg kell egyedi a mobil alkalmazás. Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon], válassza ki az App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `url_scheme_of_your_app://easyauth.callback`  A **karakterlánc url_scheme_of_your_app** a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

4. Kattintson az **OK** gombra.

5. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A Visual Studio vagy a Xamarin Studio alkalmazásban futtassa az ügyfélprojektet egy eszközön vagy emulátoron. Ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. Ennek az az oka, hogy az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a mobilalkalmazás-háttérrendszerhez. A *TodoItem* tábla most hitelesítést igényel.

Ezután frissíteni fogja az ügyfélalkalmazást, hogy erőforrásokat kérjen a mobilalkalmazás-háttérrendszertől egy hitelesített felhasználóval.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
Az alkalmazás frissül, hogy a felhasználóknak a **Bejelentkezés** gombra koppintva hitelesítsék magukat az adatok megjelenítése előtt.

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
   
    Ez létrehoz egy új módszert a felhasználó hitelesítésére, és egy metóduskezelő egy új **bejelentkezési** gombhoz. A fenti példakódban lévő felhasználó hitelesítése Facebook-bejelentkezéssel történik. A hitelesítést követően egy párbeszédpanel segítségével jelenítheti meg a felhasználói azonosítót.
   
   > [!NOTE]
   > Ha nem a Facebookot használ identitásszolgáltatót, módosítsa a fenti **LoginAsync** értékre átadott értéket a következők egyikére: *MicrosoftAccount*, *Twitter,* *Google*vagy *WindowsAzureActiveDirectory*.
   > 
   > 
2. Az **OnCreate** metódusban törölje vagy megjegyzéssel tegye ki a következő kódsort:
   
        OnRefreshItemsSelected ();
3. A Activity_To_Do.axml fájlban adja hozzá a következő *LoginUser* gombdefiníciót a meglévő *AddItem* gomb elé:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adja hozzá a következő elemet a Strings.xml erőforrásfájlhoz:
   
        <string name="login_button_text">Sign in</string>
5. Nyissa meg az AndroidManifest.xml fájlt, `<application>` és adja hozzá a következő kódot az XML-elembe:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. A Visual Studio vagy a Xamarin Studio alkalmazásban futtassa az ügyfélprojektet egy eszközön vagy emulátoron, és jelentkezzen be a kiválasztott identitásszolgáltatóval. Ha sikeresen bejelentkezett, az alkalmazás megjeleníti a bejelentkezési azonosítóját és a teendők listáját, és frissítheti az adatokat.

## <a name="troubleshooting"></a>Hibaelhárítás

**Az alkalmazás összeomlott a`Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben ütközések a támogatási csomagokban csak figyelmeztetésként jelennek meg a Visual stúdióban, de az alkalmazás futásidőben összeomlik ezzel a kivétellel. Ebben az esetben meg kell győződnie arról, hogy a projektben hivatkozott összes támogatási csomag azonos verzióval rendelkezik. Az [Azure Mobile Apps NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

<!-- URLs. -->
[Xamarin.Android alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
