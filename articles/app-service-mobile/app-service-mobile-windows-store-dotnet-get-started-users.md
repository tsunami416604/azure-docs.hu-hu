---
title: "Hitelesítés hozzáadása az univerzális Windows Platform (UWP-) alkalmazás |} Microsoft Docs"
description: "Azure App Service Mobile Apps használata az univerzális Windows Platform (UWP-) alkalmazás Identitásszolgáltatók, beleértve a különböző felhasználók hitelesítésére: aad-ben, a Google, a Facebook, a Twitter és a Microsoft."
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-windows-app"></a>Hitelesítés hozzáadása a Windows-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan adhat felhőalapú hitelesítés a mobilalkalmazás. Ebben az oktatóanyagban, hitelesítés hozzáadása az univerzális Windows Platform (UWP) gyorsútmutató-projekt Mobile Apps-alkalmazáshoz az Azure App Service által támogatott identitásszolgáltató használatával. Miután sikeresen alatt hitelesítése és engedélyezése a mobil-háttéralkalmazás által, a felhasználói azonosító értéke jelenik meg.

Ez az oktatóanyag a Mobile Apps gyors üzembe helyezés alapul. Először el kell végeznie az oktatóanyag [Ismerkedés a Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és az App Service konfigurálása
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

Most ellenőrizheti, hogy a háttér névtelen hozzáférés le van tiltva. Az UWP-alkalmazásprojektet állítsa be indítási projektként telepítheti és futtathatja az alkalmazást; Győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után. Ez akkor fordul elő, mert az alkalmazás megpróbál hozzáférni a Mobile App kód nem hitelesített felhasználóként, de a *TodoItem* tábla most hitelesítést igényel.

A következő később frissíteni az alkalmazásnak, hogy a felhasználók hitelesítésére előtti erőforrások kér az App Service.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Az UWP az alkalmazás projektre a MainPage.xaml.cs fájlban, és adja hozzá a következő kódrészletet:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Ez a kód hitelesíti a felhasználót, a Facebook-bejelentkezéskor. Ha eltérő Facebook identitásszolgáltató használ, módosítsa **MobileServiceAuthenticationProvider** fent a szolgáltató értékre.
2. Cserélje le a **OnNavigatedTo()** MainPage.xaml.cs metódust. Ezután hozzáadhat egy **bejelentkezés** az alkalmazást, amely elindítja a hitelesítés gombot.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. A MainPage.xaml.cs adja hozzá a következő kódrészletet:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Nyissa meg a MainPage.xaml projektfájlt, keresse meg az elem, amely meghatározza a **mentése** gombra, és cserélje le a következő kódot:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Az App.xaml.cs adja hozzá a következő kódrészletet:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Package.appxmanifest fájl megnyitásához lépjen **nyilatkozatok**, a **elérhető nyilatkozatok** legördülő listában válassza ki **protokoll** kattintson **Hozzáadás** gombra. Mostantól konfigurálhatja a **tulajdonságok** , a **protokoll** nyilatkozatot. A **megjelenített név**, adja hozzá a kívánt megjelenítheti a felhasználóknak az alkalmazás nevét. A **neve**, vegye fel a {url_scheme_of_your_app}.
7. Nyomja le az F5 billentyűt az alkalmazás futtatását, kattintson a **bejelentkezés** gombra, és jelentkezzen be a választott identitásszolgáltató az alkalmazásban. A bejelentkezés befejezését követően az alkalmazás hiba nélkül fut, és tudja lekérdezni a háttér, és hogy adatok.

## <a name="tokens"></a>A hitelesítési jogkivonat tárolja az ügyfélen
Az előző példában bemutatta szabványos bejelentkezéshez, ami megköveteli, hogy az ügyfél számára, hogy forduljon az identitásszolgáltató, mind az App Service minden alkalommal, az alkalmazás indítása. Nem csak nem hatékony, futtathatja az ezzel a módszerrel történő használat-konfigurációelemmel kapcsolatos problémák sok ügyfél próbálkozzon az alkalmazás kezdjék egyszerre. A jobb megoldás, az engedélyezési jogkivonatot az App Service által visszaadott gyorsítótárazza, majd próbálja meg használni az első olyan szolgáltató alapú bejelentkezés használata előtt.

> [!NOTE]
> A függetlenül használja-e ügyfél által felügyelt vagy a szolgáltatás által kezelt hitelesítési alkalmazásszolgáltatások által kiállított jogkivonat képes gyorsítótárazni. Ez az oktatóanyag a szolgáltatás által kezelt hitelesítést használ.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, fontolja meg valamelyik az alábbi oktatóanyagok folytatása:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
