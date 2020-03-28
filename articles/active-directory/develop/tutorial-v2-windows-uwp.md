---
title: Microsoft identity platform UWP első lépések | Azure
description: Hogyan hívhatja meg az Univerzális Windows Platform-alkalmazások (UWP) a Microsoft identity platform végpontja által hozzáférési jogkivonatokat igénylő API-t?
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 68473ff5a3faddd36bd4299dfdc882f679acd068
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129883"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph API hívása univerzális Windows Platform alkalmazásból (XAML)

> [!div renderon="docs"]

Ez az útmutató bemutatja, hogy egy natív univerzális Windows-platform (UWP) alkalmazás hogyan kérhet hozzáférési jogkivonatot. Az alkalmazás ezután meghívja a Microsoft Graph API-t. Az útmutató más API-kra is vonatkozik, amelyek hozzáférési jogkivonatokat igényelnek a Microsoft identity platform végpontjáról.

Az útmutató végén az alkalmazás meghívja a védett API-t személyes fiókok használatával. Ilyenpéldául outlook.com, live.com és mások. Az alkalmazás is meghívja a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory (Azure AD).

>[!NOTE]
> Ez az útmutató a Visual Studio számára univerzális Windows platformfejlesztést igényel. A Visual Studio univerzális Windows-platformalkalmazások fejlesztésére vonatkozó letöltésére és konfigurálására vonatkozó tudnivalók beállítása a [beállításról.](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)

>[!NOTE]
> Ha most ismeri a Microsoft identity platformot, javasoljuk, hogy kezdje [a Microsoft Graph hívása API-t egy univerzális Windows platform (UWP) alkalmazásból.](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>Az útmutató működése

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ez az útmutató létrehoz egy minta UWP-alkalmazást, amely lekérdezi a Microsoft Graph API-t. Ebben a forgatókönyvben egy jogkivonat ot ad hozzá a HTTP-kérelmekhez az engedélyezési fejléc használatával. A Microsoft Authentication Library (MSAL) kezeli a tokenbeszerzéseket és -megújításokat.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet csomagot használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Identity.Ügyfél](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz lépésenként bemutatja, hogy miként integrálható a Windows asztali .NET alkalmazás (XAML) a Bejelentkezés és a Microsoft alkalmazásba. Ezután az alkalmazás lekérdezheti a jogkivonatot igénylő webes API-kat, például a Microsoft Graph API-t.

Ez az útmutató létrehoz egy alkalmazást, amely megjelenít egy gombot, amely lekérdezi a Graph API-t és egy kijelentkezési gombot. Olyan szövegdobozokat is tartalmaz, amelyek tartalmazzák a hívások eredményét.

> [!NOTE]
> Letölti a minta Visual Studio-projektjét ahelyett, hogy létrehozna? [Töltsön le egy projektet,](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) és ugorjon az [alkalmazásregisztrációs](#register-your-application "alkalmazás regisztrációs lépése") lépésre a kódminta futtatás előtti konfigurálásához.

### <a name="create-your-application"></a>Az alkalmazás létrehozása

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a Blank App **(Universal Windows)** for C# lehetőséget, majd kattintson a **Tovább**gombra.
1. Az **Új projekt konfigurálása**csoportban nevezze el az alkalmazást, és válassza a **Létrehozás lehetőséget.**
1. Ha a rendszer kéri, az **Új univerzális Windows-platformprojekt**programban válassza ki bármelyik verziót a **Cél** és **a Minimális** verziók közül, és kattintson az **OK**gombra.

   ![Minimális és célverziók](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft hitelesítési tár hozzáadása a projekthez

1. A Visual Studio alkalmazásban válassza az **Eszközök** > **NuGet csomagkezelő csomagkezelő** > **konzolját**.
1. Másolja és illessze be a következő parancsot a **Csomagkezelő konzol** ablakába:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Ez a parancs telepíti a [Microsoft Authentication Library programot.](https://aka.ms/msal-net) Az MSAL beszerzi, gyorsítótárazza és frissíti azokat a felhasználói jogkivonatokat, amelyek a Microsoft identity platform által védett API-khoz férnek hozzá.

### <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói felületének létrehozása

A Visual Studio a Project sablon részeként hozza létre a *MainPage.xaml* fájlt. Nyissa meg ezt a fájlt, majd cserélje le az alkalmazás **Grid** csomópontját a következő kódra:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Token bekéselése a Microsoft Graph API-hoz az MSAL segítségével

Ez a szakasz bemutatja, hogyan használhatja az MSAL-t a Microsoft Graph API tokenjének lekéréséhez. Módosítsa a *MainPage.xaml.cs* fájlt.

1. A *MainPage.xaml.cs*adja hozzá a következő hivatkozásokat:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Cserélje `MainPage` le az osztályt a következő kódra:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése<a name="more-information"></a>

A `AcquireTokenInteractive` metódus egy ablakot eredményez, amely a felhasználók bejelentkezését kéri. Az alkalmazások általában megkövetelik a felhasználóktól, hogy először jelentkezzenek be interaktívan egy védett erőforrás eléréséhez. Előfordulhat, hogy be kell jelentkezniük, ha egy jogkivonat beszerzésére irányuló csendes művelet sikertelen. Ilyen például a felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` módszer kezeli a tokenek beszerzéseés megújítások felhasználói beavatkozás nélkül. Az `AcquireTokenInteractive` első futtatás után, és kéri a felhasználó `AcquireTokenSilent` hitelesítő adatokat, használja a módszert, hogy kérje a jogkivonatokat a későbbi hívásokhoz. Ez a metódus csendben szerzi be a jogkivonatokat. Az MSAL kezeli a tokengyorsítótárat és a megújítást.

Végül, `AcquireTokenSilent` a módszer nem sikerül. A hiba okai közé tartozik az a felhasználó, aki egy másik eszközön kijelentkezett vagy megváltoztatta a jelszavát. Ha az MSAL azt észleli, hogy a `MsalUiRequiredException` probléma interaktív műveletet igényel, kivételt okoz. Az alkalmazás kétféleképpen tudja kezelni ezt a kivételt:

* Az alkalmazás `AcquireTokenInteractive` azonnal hív. Ez a hívás a felhasználó bejelentkezésének megkérése. Általában használja ezt a módszert az olyan online alkalmazásokhoz, ahol nincs elérhető offline tartalom a felhasználó számára. Az irányított beállítás által létrehozott minta követi a mintát. A minta első futtatásakor működés közben láthatja.

   Mivel egyetlen felhasználó sem `accounts.FirstOrDefault()` használta az alkalmazást, null `MsalUiRequiredException` értéket tartalmaz, és kivételt okoz.

   A mintában lévő kód ezután `AcquireTokenInteractive`a hívással kezeli a kivételt. Ez a hívás a felhasználó bejelentkezésének megkérése.

* Az alkalmazás vizuális jelzést ad a felhasználóknak, hogy be kell jelentkezniük. Ezután kiválaszthatják a megfelelő időpontot a bejelentkezéshez. Az alkalmazás később `AcquireTokenSilent` újrapróbálkozhat. Akkor használja ezt a módszert, ha a felhasználók más alkalmazásfunkciókat is használhatnak megszakítás nélkül. Ilyen például az, ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók eldönthetik, hogy mikor szeretnének bejelentkezni. Az alkalmazás újrapróbálkozhat, `AcquireTokenSilent` miután a hálózat átmenetileg nem volt elérhető.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API hívása az imént beszerzett token használatával

A következő új módszerrel *MainPage.xaml.cs:*

   ```csharp
   /// <summary>
   /// Perform an HTTP GET request to a URL using an HTTP Authorization header
   /// </summary>
   /// <param name="url">The URL</param>
   /// <param name="token">The token</param>
   /// <returns>String containing the results of the GET operation</returns>
   public async Task<string> GetHttpContentWithToken(string url, string token)
   {
       var httpClient = new System.Net.Http.HttpClient();
       System.Net.Http.HttpResponseMessage response;
       try
       {
           var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
           // Add the token in Authorization header
           request.Headers.Authorization = 
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
           response = await httpClient.SendAsync(request);
           var content = await response.Content.ReadAsStringAsync();
           return content;
       }
       catch (Exception ex)
        {
           return ex.ToString();
       }
    }
   ```

 Ez a `GET` módszer egy `Authorization` fejléc használatával kéri a Graph API-t.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a REST-hívásról egy védett API-val szemben

Ebben a mintaalkalmazásban a `GetHttpContentWithToken` `GET` metódus http-kérelmet egy jogkivonatot igénylő védett erőforrással szemben. Ezután a metódus visszaadja a tartalmat a hívónak. Ez a módszer hozzáadja a beszerzett jogkivonatot a **HTTP-engedélyezési** fejlécben. Ebben a mintában az erőforrás a Microsoft Graph API **me** végpont, amely megjeleníti a felhasználó profiladatait.

### <a name="add-a-method-to-sign-out-the-user"></a>A felhasználó kijelentkeztetési módjának hozzáadása

A felhasználó kijelentkeztetéséhez adja hozzá a következő módszert *a MainPage.xaml.cs:*

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET aszinkron módszereket használ a tokenek megszerzéséhez vagy a fiókok kezeléséhez. Támogatnia kell a felhasználói felület műveleteket a felhasználói felület szál. Ez az oka `Dispatcher.RunAsync` a hívásnak és `ConfigureAwait(false)`a hívnivaló óvintézkedéseknek.

#### <a name="more-information-about-signing-out"></a>További információ a kijelentkezésről<a name="more-information-on-sign-out"></a>

A `SignOutButton_Click` metódus eltávolítja a felhasználót az MSAL felhasználói gyorsítótárából. Ez a módszer hatékonyan utasítja az MSAL-t, hogy felejtse el az aktuális felhasználót. A jogkivonat megszerzésére irányuló jövőbeli kérelem csak akkor sikeres, ha interaktív.

Az ebben a mintában szereplő alkalmazás egyetlen felhasználót támogat. Az MSAL olyan eseteket támogat, amelyekben a felhasználó több fiókba is bejelentkezhet. Ilyen például egy e-mail alkalmazás, ahol a felhasználó több fiókkal rendelkezik.

### <a name="display-basic-token-information"></a>Alapvető tokenadatok megjelenítése

Adja hozzá a következő módszert *a MainPage.xaml.cs* a jogkivonatalapvető információinak megjelenítéséhez:

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
   /// </summary>
   private void DisplayBasicTokenInfo(AuthenticationResult authResult)
   {
       TokenInfoText.Text = "";
       if (authResult != null)
       {
           TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
           TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
       }
   }
   ```

#### <a name="more-information"></a>További információk<a name="more-information-1"></a>

Az **OpenID Connect** használatával beszerzett azonosító tokenek a felhasználó számára vonatkozó információk egy kis részét is tartalmazzák. `DisplayBasicTokenInfo`a tokenben található alapvető információkat jeleníti meg. Ez az információ tartalmazza a felhasználó megjelenítendő nevét és azonosítóját. Azt is tartalmazza a lejárati dátuma a jogkivonat és a karakterlánc, amely a hozzáférési jogkivonatmagát jelöli. Ha többször is bejelöli a **Microsoft Graph API hívása** gombot, látni fogja, hogy ugyanazt a jogkivonatot újra felhasználták a későbbi kérelmekhez. A lejárati dátum is meghosszabbodhat, amikor az MSAL úgy dönt, hogy itt az ideje a jogkivonat megújításának.

### <a name="display-message"></a>Üzenet megjelenítése

A következő új módszerrel *MainPage.xaml.cs:*

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most regisztrálnia kell jelentkezését:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Azure Active Directory** > **alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.** Adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például *UWP-App-calling-MSGraph*.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox),** majd a folytatáshoz válassza a **Regisztráció** lehetőséget.
1. Az áttekintő lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és másolja azt. Lépjen vissza a Visual Studio-ba, nyissa `ClientId` meg *a MainPage.xaml.cs,* és cserélje le ezzel az értékkel az értékét.

Az alkalmazás hitelesítésének konfigurálása:

1. Az [Azure Portalon](https://portal.azure.com)a **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. A nyilvános | **ügyfelek (mobil, asztali) átirányítása javasolt URI-k** **átirányítása**szakaszban jelölje be a . **https://login.microsoftonline.com/common/oauth2/nativeclient**
1. Kattintson a **Mentés** gombra.

API-engedélyek konfigurálása az alkalmazáshoz:

1. A **Kezelés csoportban**válassza az **API-engedélyek lehetőséget.**
1. Válassza **az Engedély hozzáadása lehetőséget,** majd győződjön meg arról, hogy a Microsoft **API-k**at választotta.
1. Válassza a **Microsoft Graph**lehetőséget.
1. Válassza **a Delegált engedélyek lehetőséget,** keresse meg a *User.Read* elemet, és ellenőrizze, hogy a **User.Read** ki van-e jelölve.
1. Ha módosításokat hajtott végre, válassza az **Engedélyek hozzáadása** lehetőséget a mentéshez.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)

A Windows-integrált hitelesítés engedélyezéséhez, ha egy összevont Azure AD-tartománnyal használják, az alkalmazásjegyzéknek további képességeket kell engedélyeznie. Lépjen vissza az alkalmazáshoz a Visual Studióban.

1. *Nyissa meg a Package.appxmanifest fájlt.*
1. Válassza a **Képességek lehetőséget,** és engedélyezze a következő beállításokat:

   * **Vállalati hitelesítés**
   * **Privát hálózatok (ügyfél& kiszolgáló)**
   * **Megosztott felhasználói tanúsítványok**

> [!IMPORTANT]
> [Az integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa) alapértelmezés szerint nincs konfigurálva ehhez a mintához. A kérésre `Shared User Certificates` vagy képességekre jelentkező `Enterprise Authentication` alkalmazások esetében a Windows Áruház nak magasabb szintű ellenőrzést kell végzette. Továbbá nem minden fejlesztő szeretné elvégezni a magasabb szintű ellenőrzést. Csak akkor engedélyezze ezt a beállítást, ha a Windows integrált hitelesítésre van szüksége összevont Azure AD-tartománnyal.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás teszteléséhez válassza az F5 lehetőséget a projekt Visual Studióban való futtatásához. Megjelenik a főablak:

![Az alkalmazás felhasználói felülete](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Amikor készen áll a tesztelésre, válassza **a Microsoft Graph API hívása**lehetőséget. Ezután egy Azure AD szervezeti fiók vagy egy Microsoft-fiók, például live.com vagy outlook.com, a bejelentkezéshez. Az első alkalommal, amikor egy felhasználó futtatja ezt, az alkalmazás megjelenik egy ablak, amely a felhasználót a bejelentkezésre kéri.

### <a name="consent"></a>Beleegyezés

Amikor először jelentkezik be az alkalmazásba, az alábbihoz hasonló jóváhagyási képernyő jelenik meg. Válassza az **Igen** lehetőséget a hozzáférés kifejezett beleegyezéséhez:

![Hozzáférés hozzájárulási képernyője](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Várt eredmények

A Microsoft Graph API-hívás által visszaadott felhasználói profiladatok az API hívásának eredménye képernyőn **jelennek meg:**

![API-hívás eredménye képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

A tokenről a Token Info `AcquireTokenInteractive` mezőben keresztül vagy `AcquireTokenSilent` a **tokeninfo** mezőben is alapvető információkat talál:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|`Username` |`user@domain.com` |A felhasználót azonosító felhasználónév.|
|`Token Expires` |`DateTime` |A token lejáratának időpontja. Az MSAL szükség esetén meghosszabbítja a lejárati dátumot a jogkivonat megújításával.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph `user.read` API-nak szüksége van a hatókörre a felhasználói profil olvasásához. Ez a hatókör alapértelmezés szerint az alkalmazásregisztrációs portálon regisztrált minden alkalmazáshoz hozzá van adva. A Microsoft Graph egyéb API-k és a háttérkiszolgáló egyéni API-jai további hatóköröket igényelhetnek. A Microsoft Graph API `Calendars.Read` például megköveteli a hatókört a felhasználó naptárainak listázásához.

Ha egy alkalmazás környezetében szeretné elérni a felhasználó `Calendars.Read` naptárait, adja hozzá a delegált engedélyt az alkalmazás regisztrációs adataihoz. Ezután `Calendars.Read` adja hozzá `acquireTokenSilent` a hatókört a híváshoz.

> [!NOTE]
> A felhasználók további jóváhagyásokat kérhetnek a hatókörök számának növelése kor.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1. probléma

Az alábbi hibaüzenetek egyike jelenik meg, amikor bejelentkezik az alkalmazásba egy összevont Azure AD-tartományban:

* Nem található érvényes ügyféltanúsítvány a kérelemben.
* Nem található érvényes tanúsítvány a felhasználó tanúsítványtárolójában.
* Próbálkozzon újra egy másik hitelesítési módszer kiválasztásával.

Ok: A vállalati és tanúsítványképességek nincsenek engedélyezve.

Megoldás: Kövesse az [Integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)](#enable-integrated-authentication-on-federated-domains-optional)című.

### <a name="issue-2"></a>2. probléma

Engedélyezi [az integrált hitelesítést az összevont tartományokon,](#enable-integrated-authentication-on-federated-domains-optional) és megpróbál a Windows Hello használatával Windows 10-es számítógépen bejelentkezni egy többtényezős hitelesítéssel konfigurált környezetbe. A tanúsítványok listája bemutatásra kerül. Ha azonban a PIN-kód használatát választja, a PIN-kód ablak soha nem jelenik meg.

Ok: Ez a probléma a Windows 10 asztalon futó UWP-alkalmazások webes hitelesítési brókerének ismert korlátozása. Ez szerkezet finom -ra Windows 10 Mozgatható.

Megoldás: Válassza **a Bejelentkezés más beállításokkal lehetőséget.** Ezután válassza **a Bejelentkezés felhasználónévvel és jelszóval**lehetőséget. Válassza **a Jelszó megadása**lehetőséget. Ezután menjen végig a telefon hitelesítési folyamatán.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével:

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
