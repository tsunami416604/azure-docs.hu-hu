---
title: Hitelesítés hozzáadása az UWP alkalmazáshoz
description: Ismerje meg, hogyan hitelesítheti az Univerzális Windows-platform (UWP) alkalmazás felhasználóit olyan identitásszolgáltatókkal, mint az AAD, a Google, a Facebook, a Twitter és a Microsoft.
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
Ez a témakör bemutatja, hogyan adhat hozzá felhőalapú hitelesítést a mobilalkalmazásához. Ebben az oktatóanyagban az Azure App Service által támogatott identitásszolgáltató használatával adja hozzá a hitelesítést az univerzális Windows-platform (UWP) gyorsindítási projektjéhez a mobilalkalmazásokhoz. Miután a mobilalkalmazás-háttérrendszer sikeresen hitelesítette és engedélyezte, a felhasználói azonosító értéke megjelenik.

Ez az oktatóanyag a mobilalkalmazások rövid útmutatóján alapul. Először be kell fejeznie az oktatóanyagot [A mobilalkalmazások első lépései .](app-service-mobile-windows-store-dotnet-get-started.md)

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Az alkalmazás regisztrálása hitelesítésre és az App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba. Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk. Azonban bármilyen URL-sémát használhat. Meg kell egyedi a mobil alkalmazás. Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon](https://ms.portal.azure.com)válassza ki az App Service.In the Azure Portal , select your App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `url_scheme_of_your_app://easyauth.callback`  A **karakterlánc url_scheme_of_your_app** a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

4. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttérrendszerhez való névtelen hozzáférés le van-e tiltva. Ha az UWP alkalmazásprojekt be van állítva induló projektként, telepítse és futtassa az alkalmazást; ellenőrizze, hogy az alkalmazás indítása után egy 401-es állapotkóddal (Nem engedélyezett) rendelkező kezeletlen kivétel jelenik-e meg. Ennek az az oka, hogy az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a mobilalkalmazáskódjához, de a *TodoItem* tábla hitelesítést igényel.

Ezután frissíteni fogja az alkalmazást a felhasználók hitelesítéséhez, mielőtt erőforrásokat kérne az App Service-től.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Az UWP alkalmazás projektfájljában MainPage.xaml.cs, és adja hozzá a következő kódrészletet:
   
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
   
    Ez a kód facebookos bejelentkezéssel hitelesíti a felhasználót. Ha a Facebooktól eltérő identitásszolgáltatót használ, módosítsa a fenti **MobileServiceAuthenticationProvider** értékét a szolgáltató jarábbra.
2. Cserélje le az **OnNavigatedTo() metódust** MainPage.xaml.cs. Ezután hozzáad egy **Bejelentkezésgombot** az alkalmazáshoz, amely elindítja a hitelesítést.

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
4. Nyissa meg a MainPage.xaml projektfájlt, keresse meg a **Mentés** gombot meghatározó elemet, és cserélje le a következő kódra:
   
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
6. Nyissa meg a Package.appxmanifest fájlt, keresse meg a **Deklarációk**lehetőséget a **Rendelkezésre álló nyilatkozatok** legördülő listában, válassza a **Protokoll** lehetőséget, és kattintson a **Hozzáadás** gombra. Most konfigurálja a **Protokoll** deklaráció **tulajdonságait.** A **Megjelenítendő név**mezőbe adja hozzá az alkalmazás felhasználóinak megjeleníteni kívánt nevet. A **Név**mezőbe adja hozzá a(z) {url_scheme_of_your_app} elemet.
7. Nyomja le az F5 billentyűt az alkalmazás futtatásához, kattintson a **Bejelentkezés** gombra, és jelentkezzen be az alkalmazásba a kiválasztott identitásszolgáltatóval. Miután a bejelentkezés sikeres, az alkalmazás hiba nélkül fut, és lekérdezheti a háttérrendszer, és az adatok frissítése.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>A hitelesítési jogkivonat tárolása az ügyfélen
Az előző példa egy szabványos bejelentkezést mutatott, amely megköveteli, hogy az ügyfél az alkalmazás indításakor az identitásszolgáltatóval és az app szolgáltatással is kapcsolatba lépjen. Ez a módszer nem csak nem hatékony, hanem használattal kapcsolatos problémákba is belefuthat, ha sok ügyfél egyszerre próbálja elindítani az alkalmazást. Egy jobb módszer az, hogy gyorsítótárazza az App Service által visszaadott engedélyezési jogkivonatot, és először próbálja meg használni ezt a szolgáltató-alapú bejelentkezés előtt.

> [!NOTE]
> Az App Services által kibocsátott jogkivonat gyorsítótárazhatja, függetlenül attól, hogy ügyfél által kezelt vagy szolgáltatás által felügyelt hitelesítést használ.You can cache the token issued by App Services regardless you are using client-managed or service-managed authentication. Ez az oktatóanyag szolgáltatás által felügyelt hitelesítést használ.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy befejezte ezt az alapszintű hitelesítési oktatóanyagot, fontolja meg az alábbi oktatóanyagok egyikének folytatását:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
