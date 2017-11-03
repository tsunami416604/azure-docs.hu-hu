---
title: "Ismerkedés a Mobile Apps a Xamarin Android hitelesítés"
description: "Útmutató a Mobile Apps segítségével hitelesíti a felhasználókat az identitás-szolgáltatóktól, beleértve az aad-ben, a Google, a Facebook, a Twitter és a Microsoft számos Xamarin Android-alkalmazás."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 8f9a1109018c708d52cdcb7b8bce43861cecd31c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Hitelesítés hozzáadása Xamarin.Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan hitelesítheti a Mobile Apps, az ügyfélalkalmazás felhasználóit. Ebben az oktatóanyagban hitelesítés használata az Azure Mobile Apps által támogatott identitásszolgáltató gyorsútmutató-projekt hozzáadhat. Miután sikeresen alatt hitelesítése és engedélyezése a mobileszköz-alkalmazás, a felhasználói azonosító értéke jelenik meg.

Ez az oktatóanyag a Mobile Apps gyors üzembe helyezés alapul. Először is el kell végeznie az oktatóanyag [Xamarin.Android-alkalmazás létrehozása]. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hitelesítési kiterjesztés csomag fel a projektbe. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service szolgáltatások
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Biztonságos hitelesítéshez az szükséges, hogy az alkalmazás adja meg egy új URL-sémát. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma használjuk _appname_ egész. Bármely választja URL-sémát is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgáló oldalán engedélyezése:

1. Válassza ki az App Service az [Azure-portálon].

2. Kattintson a **hitelesítési / engedélyezési** menüjét.

3. Az a **engedélyezett külső átirányítási URL-t**, adja meg `url_scheme_of_your_app://easyauth.callback`.  A **url_scheme_of_your_app** ezt a karakterláncot a rendszer az URL-séma, a mobilalkalmazás.  Akkor érdemes követnie normál URL-cím meghatározása (használata betűket és számokat csak, és betűvel kezdődik) protokoll.  Meg kell győződnie, jegyezze fel a karakterláncot, amely úgy dönt, mivel úgy, hogy az URL-séma több helyen a mobilalkalmazás kódot kell.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A Visual Studio és Xamarin Studio futtatható az ügyfélprojekt egy eszközt vagy emulátort. Győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. Ez akkor fordul elő, mert az alkalmazás megpróbál hozzáférni a mobil-háttéralkalmazást, nem hitelesített felhasználónak. A *TodoItem* tábla most hitelesítést igényel.

A következő később frissíteni az ügyfélalkalmazás az erőforrásokat a Mobile Apps-háttéralkalmazás a hitelesített felhasználók.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
A felhasználók koppintson frissítette az alkalmazást a **bejelentkezés** gombra, és hitelesíti a, mielőtt adatok jelennek meg.

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
   
    Ezzel létrehoz egy új módszer a felhasználók és a metódus kezelő hitelesítésére az új **bejelentkezés** gombra. A fenti példa kód a felhasználó hitelesítése egy Facebook-bejelentkezési használatával. A párbeszédpanel segítségével egyszer hitelesített felhasználói azonosító megjelenítése.
   
   > [!NOTE]
   > Ha eltérő Facebook identitásszolgáltató használ, módosítsa az értéket, átadott **LoginAsync** fent a következőhöz: *MicrosoftAccount*, *Twitter*, *Google*, vagy *WindowsAzureActiveDirectory*.
   > 
   > 
2. Az a **OnCreate** metódus, törlése vagy a következő kódsort a Megjegyzés kibővített:
   
        OnRefreshItemsSelected ();
3. A Activity_To_Do.axml fájlban adja hozzá a következő *LoginUser* -definíciót, mielőtt a meglévő gomb *additem metódust* gombra:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. A következő elem hozzáadása a Strings.xml erőforrások fájlhoz:
   
        <string name="login_button_text">Sign in</string>
5. Nyissa meg az AndroidManifest.xml fájlhoz, adja hozzá a következő kódot `<application>` XML-elem:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. A Visual Studio és Xamarin Studio a ügyfélprojekt futtatnak egy eszközt vagy emulátort, és jelentkezzen be a választott identitásszolgáltató. Amikor sikeresen bejelentkezett, az alkalmazás megjelenik a bejelentkezési azonosító és a teendőlista elemeinek listáját, és frissíthetik az adatokat.

<!-- URLs. -->
[Xamarin.Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md