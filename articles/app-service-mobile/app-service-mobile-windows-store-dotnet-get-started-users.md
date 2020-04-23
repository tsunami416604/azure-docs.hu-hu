---
title: Hitelesítés hozzáadása a UWP-alkalmazáshoz
description: Ismerje meg, hogyan használhatja a Azure App Service Mobile Appst a Univerzális Windows-platform (UWP) alkalmazás felhasználóinak hitelesítésére, például a HRE, a Google, a Facebook, a Twitter és a Microsoft identitásával.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458936"
---
# <a name="add-authentication-to-your-windows-app"></a>Hitelesítés hozzáadása a Windows-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Áttekintés
Ebből a témakörből megtudhatja, hogyan adhat felhőalapú hitelesítést a Mobile apps szolgáltatáshoz. Ebben az oktatóanyagban a Azure App Service által támogatott identitás-szolgáltató használatával adja hozzá a hitelesítést a Univerzális Windows-platform (UWP) gyors üzembe helyezési projekthez Mobile Apps. Miután a Mobile apps-háttér sikeresen elvégezte a hitelesítést és az engedélyt, megjelenik a felhasználói azonosító értéke.

Ez az oktatóanyag a Mobile Apps rövid útmutatóján alapul. Először el kell végeznie az oktatóanyag első [lépéseinek Mobile apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben. Használhat azonban bármely kiválasztott URL-címet. Egyedinek kell lennie a Mobile-alkalmazás számára. Az átirányítás engedélyezése a kiszolgálóoldali oldalon:

1. A [Azure Portal](https://ms.portal.azure.com)válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Az **engedélyezett külső átirányítási URL**-címek `url_scheme_of_your_app://easyauth.callback`mezőben adja meg a értéket.  A karakterláncban szereplő **url_scheme_of_your_app** a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

4. Kattintson a **Save** (Mentés) gombra.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttérbeli névtelen hozzáférés le van-e tiltva. A UWP alkalmazás projekt indítási projektként való beállításakor telepítse és futtassa az alkalmazást; Ellenőrizze, hogy az alkalmazás elindítása után nem kezelt kivétel történt-e az 401 (nem engedélyezett) állapotkóddal. Ennek az az oka, hogy az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a Mobile App Code-hoz, de a *TodoItem* táblához már hitelesítés szükséges.

Ezután frissítenie kell az alkalmazást a felhasználók hitelesítéséhez, mielőtt erőforrásokat kérne a App Service.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. A UWP alkalmazás projektfájl MainPage.xaml.cs, és adja hozzá a következő kódrészletet:
   
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
   
    Ez a kód egy Facebook-bejelentkezéssel hitelesíti a felhasználót. Ha a Facebooktól eltérő identitás-szolgáltatót használ, módosítsa a fenti **MobileServiceAuthenticationProvider** értékét a szolgáltató értékére.
2. Cserélje le a **OnNavigatedTo ()** metódust a MainPage.XAML.cs-ben. Ezután egy **bejelentkezési** gombot fog hozzáadni az alkalmazáshoz, amely elindítja a hitelesítést.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adja hozzá a következő kódrészletet a MainPage.xaml.cs:
   
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
4. Nyissa meg a Főoldal. XAML projektfájlt, keresse meg a Save ( **Mentés** ) gombot meghatározó elemet, és cserélje le a következő kódra:
   
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
5. Adja hozzá a következő kódrészletet a App.xaml.cs:

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
6. Nyissa meg a Package. appxmanifest fájlt, navigáljon az **elérhető deklarációk** legördülő listából, válassza a **protokoll** lehetőséget, majd **kattintson a** **Hozzáadás** gombra. Most konfigurálja a **protokoll** deklarációjának **tulajdonságait** . A **megjelenítendő név mezőben**adja meg az alkalmazás felhasználói számára megjeleníteni kívánt nevet. A **név mezőben**adja hozzá a (z) {Url_scheme_of_your_app} nevet.
7. Az alkalmazás futtatásához nyomja le az F5 billentyűt, kattintson a **Bejelentkezés** gombra, és jelentkezzen be az alkalmazásba a kiválasztott identitás-szolgáltatóval. A bejelentkezés sikeres végrehajtása után az alkalmazás hibák nélkül fut, és lekérdezheti a háttérrendszer adatait, és frissítheti azokat.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>A hitelesítési jogkivonat tárolása az ügyfélen
Az előző példában egy szabványos bejelentkezés látható, amely megköveteli, hogy az ügyfél felvegye a kapcsolatot az identitás-szolgáltatóval és az App Service minden alkalommal, amikor az alkalmazás elindul. Nem csak ez a módszer nem hatékony, a használattal kapcsolatos problémákba ütközik, ha sok ügyfél megpróbál egyszerre elindítani az alkalmazást. A jobb módszer a App Service által visszaadott engedélyezési jogkivonat gyorsítótárazása, és az első használata a szolgáltató-alapú bejelentkezés használata előtt.

> [!NOTE]
> A App Services által kiállított tokent gyorsítótárazhatja, függetlenül attól, hogy az ügyfél által felügyelt vagy a szolgáltatás által felügyelt hitelesítést használja-e. Ez az oktatóanyag szolgáltatás által felügyelt hitelesítést használ.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy elvégezte ezt az alapszintű hitelesítési oktatóanyagot, tekintse át a következő oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
