---
title: Ismerkedés a Xamarin Android Mobile Apps hitelesítése
description: Ismerje meg, hogyan hitelesítheti a felhasználókat az identitás-szolgáltatóktól, többek között az aad-ben, Google, Facebook, Twitter és a Microsoft számos Xamarin Android-alkalmazás a Mobile Apps segítségével.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: d496801894560310a4225eae8a32fced52bcc428
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063540"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Hitelesítés hozzáadása Xamarin.Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan hitelesítheti az ügyfélalkalmazás egy Mobile Apps felhasználóit. Ebben az oktatóanyagban hozzáadja hitelesítés a példaprojekthez az identitásszolgáltató az Azure Mobile Apps által támogatott. Miután sikeresen folyamatban hitelesítése és engedélyezése a Mobile App, a felhasználói azonosító értéke jelenik meg.

Ez az oktatóanyag a Mobile alkalmazás – rövid útmutató alapján történik. Először is kell végeznie az oktatóanyag [Xamarin.Android-alkalmazás létrehozása]. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a hitelesítési kiterjesztés csomagot a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítés és az App Services konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Hitelesítésre van szükség, hogy az alkalmazás egy új URL-séma meghatározása. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejeződése után. Ebben az oktatóanyagban azt használja az URL-séma _appname_ során. Bármely URL-séma választja is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgálói oldalon az átirányítás engedélyezése:

1. Az [Azure Portalon] válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüpont.

3. Az a **engedélyezett külső átirányítási URL-címeket**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** Ez a karakterlánc a következő az URL-séma esetében.  Normál URL-cím-specifikáció (használata betűket és csak számokból állhat, és betűvel kezdődhet) protokoll, érdemes követnie.  Meg kell jegyezze fel a karakterlánc, amely úgy dönt, mert szüksége lesz a mobilalkalmazás-kód az URL-séma több helyen való beállításához.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A Visual Studióban vagy a Xamarin Studióban futtassa az eszközt vagy emulátort a. Győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. Ennek oka az, az alkalmazást próbál meg hozzáférni egy nem hitelesített felhasználóként a mobilalkalmazás háttérrendszerének. A *TodoItem* táblázat most már hitelesítést igényel.

Ezután az ügyfélalkalmazás szeretne frissíteni erőforrás-kérelmek a Mobile Apps-háttéralkalmazást a hitelesített felhasználóval.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
Koppintson a felhasználóknak kötelező frissítette az alkalmazást a **jelentkezzen be a** gombra, és hitelesíti a, mielőtt adatai jelennek meg.

1. Adja hozzá a következő kódot a **TodoActivity** osztály:
   
        // Define a authenticated user.
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
   
    Ez létrehoz egy új módszer az új felhasználó és a egy metódust kezelő hitelesítéséhez **jelentkezzen be a** gombra. A fenti példa kódban a felhasználó hitelesítése egy Facebook-bejelentkezés használatával. Egy párbeszédpanel a felhasználói azonosító után megjelenítésére szolgál.
   
   > [!NOTE]
   > Eltérő Facebook identitásszolgáltatót használja, módosítsa az átadott érték **LoginAsync** felett, a következők egyikét: *MicrosoftAccount*, *Twitter*,  *Google*, vagy *WindowsAzureActiveDirectory*.
   > 
   > 
2. Az a **OnCreate** metódus, törölje vagy tegyen Megjegyzés jelzést az alábbi kódsort:
   
        OnRefreshItemsSelected ();
3. A Activity_To_Do.axml fájlban adja hozzá a következő *LoginUser* definíciót, mielőtt a meglévő gombra *AddItem* gombra:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adja hozzá a következő elem a Strings.xml erőforrások fájlba:
   
        <string name="login_button_text">Sign in</string>
5. Nyissa meg az AndroidManifest.xml fájlhoz, adja hozzá a következő kódot a `<application>` XML-elem:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. A Visual Studióban vagy a Xamarin Studióban futtassa az eszközt vagy emulátort a, és jelentkezzen be a választott identitásszolgáltató. Ha sikeresen bejelentkezett, az alkalmazás megjelenik a bejelentkezési azonosító és a teendőlista elemeinek listáját, és az adatok lehetővé teszi a frissítések.

## <a name="troubleshooting"></a>Hibaelhárítás

**Az alkalmazás az összeomlott `Java.Lang.NoSuchMethodError: No static method startActivity`**

Bizonyos esetekben ütközik a támogatási csomagok, csak a Visual studio, de ehhez a kivételhez futásidőben az alkalmazás-összeomlásokat figyelmeztetés jelenik meg. Ebben az esetben kell győződjön meg arról, hogy a projekt hivatkozott összes támogatási csomag verziójával rendelkezik-e. A [Azure Mobile Apps NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) rendelkezik `Xamarin.Android.Support.CustomTabs` függőséget az Android platformhoz, így ha a projekt újabb használja a támogatási csomagok, a csomag telepítéséhez szükséges verziójú ütközések elkerülése érdekében közvetlenül kell.

<!-- URLs. -->
[Xamarin.Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
