---
title: Hitelesítés hozzáadása az univerzális Windows Platform (UWP-) alkalmazáshoz |} A Microsoft Docs
description: 'Ismerje meg, hogyan lehet Azure App Service Mobile Apps segítségével hitelesítheti a felhasználókat az identitás-szolgáltatóktól, beleértve a különféle univerzális Windows Platform (UWP) alkalmazás: aad-ben, Google, Facebook, Twitter vagy Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38306574"
---
# <a name="add-authentication-to-your-windows-app"></a>Hitelesítés hozzáadása a Windows-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Ez a témakör bemutatja, hogyan felhőalapú hitelesítés hozzáadása az alkalmazásához. Ebben az oktatóanyagban, hitelesítés hozzáadása az univerzális Windows Platform (UWP) gyorsútmutató-projekt egy identitásszolgáltatóval, amelyet támogat az Azure App Service Mobile Apps-alkalmazáshoz. Folyamatban sikeresen hitelesíti és a Mobile Apps-háttéralkalmazást, után jelenik meg a felhasználói azonosító értékét.

Ez az oktatóanyag a Mobile Apps – első lépések alapján. Először végezze el az oktatóanyag [Mobile Apps használatának első lépései](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service-ben
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

Most ellenőrizheti, hogy a háttérrendszerhez való névtelen hozzáférés le van tiltva. Az UWP-alkalmazásprojektet állítsa be az indítási projektként, és üzembe helyezése, és futtassa az alkalmazást; Győződjön meg arról, hogy a 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel az alkalmazás indítása után jelenik meg. Ez akkor fordul elő, mert az alkalmazást próbál meg hozzáférni a mobilalkalmazás-kód nem hitelesített felhasználóként, de a *TodoItem* táblázat most már hitelesítést igényel.

Ezt követően az alkalmazásnak, hogy a felhasználók hitelesítése előtt erőforrásokat kér az App Service frissíteni.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Az UWP az alkalmazás MainPage.xaml.cs fájlban projektre, és adja hozzá a következő kódrészletet:
   
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
   
    Ez a kód hitelesíti a felhasználót egy Facebook-bejelentkezés. Eltérő Facebook identitásszolgáltatót használja, ha értékének módosítása **MobileServiceAuthenticationProvider** fent a szolgáltató értékére.
2. Cserélje le a **OnNavigatedTo()** metódus a MainPage.xaml.cs. Ezután hozzáadhat egy **jelentkezzen be a** az alkalmazást, amely elindítja a hitelesítés gombot.

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
4. Nyissa meg a következő projektfájlban: MainPage.xaml, keresse meg az elem, amely meghatározza a **mentése** gombra, és cserélje le a következő kódot:
   
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
5. Az App.XAML.cs fájlban adja hozzá a következő kódrészletet:

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
6. Package.appxmanifest fájl megnyitásához keresse meg **nyilatkozatok**, a **elérhető nyilatkozatok** legördülő listában válassza **protokoll** kattintson **hozzáadása** gombra. Mostantól konfigurálhatja az **tulajdonságok** , a **protokoll** nyilatkozatot. A **megjelenítendő név**, adja hozzá a felhasználók számára az alkalmazás megjelenítendő neve. A **neve**, adja hozzá a {url_scheme_of_your_app}.
7. Nyomja le az F5 billentyűt az alkalmazás fut, kattintson a **jelentkezzen be a** gombra, és jelentkezzen be az alkalmazás a választott identitásszolgáltatóval. Miután a bejelentkezés sikeres, az alkalmazás hiba nélkül fut, és a háttérkiszolgáló lekérdezéséhez, és adatok frissítéséhez.

## <a name="tokens"></a>A hitelesítési jogkivonat Store az ügyfélen
Az előző példa bemutatta, szabványos bejelentkezési, ami megköveteli, hogy az ügyfél kapcsolatba az identitásszolgáltató és az App Service-ben minden egyes elindításakor. Nem csak nem hatékony, futtathatja az ezzel a módszerrel történő-kapcsolatos problémákat kell számos ügyfelünk próbálja meg elindítani, alkalmazás egyszerre. Esetleg jobb megközelítés, hogy az engedélyezési jogkivonatra, az App Service által visszaadott gyorsítótárazza, és próbálja meg használni az első olyan szolgáltató alapú bejelentkezés használata előtt.

> [!NOTE]
> Függetlenül attól, hogy a hitelesítési ügyfél által felügyelt vagy a szolgáltatás által kezelt alkalmazás-szolgáltatások által kiállított biztonsági jogkivonat képes gyorsítótárazni. Ebben az oktatóanyagban a szolgáltatás által kezelt hitelesítést használ.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, vegye figyelembe a folyamatos be az alábbi oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
