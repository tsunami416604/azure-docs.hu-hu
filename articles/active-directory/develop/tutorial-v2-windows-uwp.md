---
title: Microsoft Identity platform UWP – első lépések | Azure
description: Hogyan Univerzális Windows-platform alkalmazások (UWP-k) olyan API-t hívhatnak meg, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform végpontja számára.
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129883"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph API meghívása Univerzális Windows-platform alkalmazásból (XAML)

> [!div renderon="docs"]

Ez az útmutató azt ismerteti, hogyan kérhető le egy natív Univerzális Windows-platform (UWP) alkalmazás hozzáférési tokent. Az alkalmazás ezután meghívja a Microsoft Graph API-t. Az útmutató más API-kra is vonatkozik, amelyek hozzáférési jogkivonatokat igényelnek a Microsoft Identity platform végpontján.

Az útmutató végén az alkalmazás személyes fiókok használatával meghívja a védett API-t. Ilyenek például a outlook.com, a live.com és mások. Az alkalmazás a munkahelyi és iskolai fiókokat is meghívja bármely olyan vállalattól vagy szervezettől, amely Azure Active Directory (Azure AD).

>[!NOTE]
> Ehhez az útmutatóhoz telepíteni kell a Visual studiót Univerzális Windows-platform-fejlesztéssel. Az Univerzális Windows-platform alkalmazások fejlesztéséhez tekintse [meg a bevezetés](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) a Visual Studio letöltésére és konfigurálására vonatkozó utasításokat.

>[!NOTE]
> Ha még nem ismeri a Microsoft Identity platformot, javasoljuk, hogy kezdje a [Microsoft Graph API meghívásával egy univerzális Windows-platform (UWP) alkalmazás](quickstart-v2-uwp.md)rövid útmutatójában.

## <a name="how-this-guide-works"></a>Az útmutató működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ez az útmutató egy minta UWP-alkalmazást hoz létre, amely lekérdezi Microsoft Graph API-t. Ebben az esetben a rendszer egy jogkivonatot ad hozzá a HTTP-kérelmekhez az engedélyezési fejléc használatával. A Microsoft Authentication Library (MSAL) kezeli a tokenek beszerzését és megújítását.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagot használja:

|Kódtár|Leírás|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen ismerteti a Windows asztali .NET-alkalmazások (XAML) integrálását a Microsofttal való bejelentkezéssel. Ezután az alkalmazás olyan webes API-kat tud lekérdezni, amelyek jogkivonatot igényelnek, például Microsoft Graph API-t.

Ez az útmutató egy olyan alkalmazást hoz létre, amely lekérdezi Graph API és egy gombot a kijelentkezéshez. Emellett a hívások eredményét tartalmazó szövegmezőket is megjeleníti.

> [!NOTE]
> Szeretné letölteni a minta Visual Studio-projektjét a létrehozása helyett? [Töltsön le egy projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) , és ugorjon az [alkalmazás regisztrálása](#register-your-application "alkalmazás regisztrációs lépése") lépésre, és állítsa be a kód mintáját a futtatása előtt.

### <a name="create-your-application"></a>Az alkalmazás létrehozása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza az **üres alkalmazás (univerzális Windows)** lehetőséget, és kattintson a C# **Tovább gombra**.
1. Az **új projekt konfigurálása**lapon adja meg az alkalmazás nevét, majd válassza a **Létrehozás**lehetőséget.
1. Ha a rendszer kéri, az **új univerzális Windows-platform projektben**válassza ki a **cél** és a **minimális** verziók bármely verzióját, és kattintson **az OK gombra**.

   ![Minimális és a célként megadott verziók](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft hitelesítési függvénytár hozzáadása a projekthez

1. A Visual Studióban válassza a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemet.
1. Másolja és illessze be a következő parancsot a **Package Manager konzol** ablakában:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Ez a parancs telepíti a [Microsoft hitelesítési tárat](https://aka.ms/msal-net). A MSAL a Microsoft Identity platform által védett API-khoz hozzáférő felhasználói jogkivonatok beszerzését, gyorsítótárazását és frissítését végzi.

### <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói felületének létrehozása

A Visual Studio a projekt sablonjának részeként létrehozza a *Főoldal. XAML* -t. Nyissa meg ezt a fájlt, majd cserélje le az alkalmazás **rács** csomópontját a következő kódra:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Token beszerzése Microsoft Graph API-hoz a MSAL használatával

Ez a szakasz bemutatja, hogyan használható a MSAL a Microsoft Graph API-hoz tartozó jogkivonat lekéréséhez. Módosítsa a *MainPage.XAML.cs* -fájlt.

1. A *MainPage.XAML.cs*-ben adja hozzá az alábbi hivatkozásokat:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Cserélje le a `MainPage` osztályt a következő kódra:

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

#### Felhasználói jogkivonat interaktív beszerzése<a name="more-information"></a>

A `AcquireTokenInteractive` metódus egy olyan ablakot eredményez, amely felszólítja a felhasználókat, hogy jelentkezzenek be. Az alkalmazások általában megkövetelik, hogy a felhasználók interaktívan jelentkezzenek be az első alkalommal egy védett erőforráshoz való hozzáféréshez. Előfordulhat, hogy be kell jelentkezniük, amikor a token beszerzésére irányuló csendes művelet meghiúsul. Ilyen például, ha egy felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. Ha az első alkalommal `AcquireTokenInteractive` fut, és felszólítja a felhasználót a hitelesítő adatok megadására, használja a `AcquireTokenSilent` metódust a későbbi hívások jogkivonatának igényléséhez. Ez a metódus csendes módon szerzi be a jogkivonatokat. A MSAL kezeli a jogkivonat gyorsítótárát és megújítását.

Végül a `AcquireTokenSilent` metódus sikertelen lesz. A hiba okai közé tartozik az a felhasználó, aki kijelentkezett vagy módosította a jelszavát egy másik eszközön. Ha a MSAL észleli, hogy a probléma interaktív műveletet igényel, `MsalUiRequiredException` kivételt jelez. Az alkalmazás két módon tudja kezelni ezt a kivételt:

* Az alkalmazás meghívja `AcquireTokenInteractive` azonnal. Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be. Ezt a módszert általában olyan online alkalmazásokhoz használja, amelyeken nincs elérhető offline tartalom a felhasználó számára. Az irányított telepítő által generált minta követi a mintát. A minta első futtatásakor ez a művelet jelenik meg.

   Mivel egyetlen felhasználó sem használta az alkalmazást, `accounts.FirstOrDefault()` null értéket tartalmaz, és `MsalUiRequiredException` kivételt dob.

   A mintában szereplő kód a `AcquireTokenInteractive`meghívásával kezeli a kivételt. Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be.

* Az alkalmazás vizuálisan jelzi a felhasználóknak, hogy be kell jelentkezniük. Ezután kiválaszthatják a megfelelő időpontot a bejelentkezéshez. Az alkalmazás később is újra `AcquireTokenSilent`. Ezt a módszert akkor használja, ha a felhasználók más alkalmazás-funkciókat is használhatnak megszakítás nélkül. Ilyen eset például, ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók dönthetnek arról, hogy mikor szeretnének bejelentkezni. Az alkalmazás újrapróbálkozhat `AcquireTokenSilent` miután a hálózat átmenetileg nem volt elérhető.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Microsoft Graph API meghívása az imént beszerzett token használatával

Adja hozzá a következő új metódust a *MainPage.XAML.cs*:

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

 Ez a módszer egy `Authorization` fejléc használatával kezdeményezi a Graph API `GET` kérését.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Ebben a példában a `GetHttpContentWithToken` metódus HTTP-`GET` kérelmet tesz elérhetővé egy olyan védett erőforráson, amely jogkivonatot igényel. Ezután a metódus visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a **http-engedélyezési** fejlécben. Ebben a példában az erőforrás a Microsoft Graph API **Me** -végpontja, amely megjeleníti a felhasználó profiljának adatait.

### <a name="add-a-method-to-sign-out-the-user"></a>Metódus hozzáadása a felhasználó kijelentkezéséhez

A felhasználó kijelentkezéséhez adja hozzá a következő metódust a *MainPage.XAML.cs*:

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
> A MSAL.NET aszinkron metódusokat használ a jogkivonatok beszerzéséhez vagy a fiókok kezeléséhez. A felhasználói FELÜLETi szálban támogatni kell a felhasználói felületi műveleteket. Ennek az az oka, hogy a `Dispatcher.RunAsync` hívást és a `ConfigureAwait(false)`hívásához szükséges óvintézkedéseket.

#### További információ a kijelentkezésről<a name="more-information-on-sign-out"></a>

A `SignOutButton_Click` metódus eltávolítja a felhasználót a MSAL felhasználói gyorsítótárból. Ez a módszer gyakorlatilag azt jelzi, hogy a MSAL elfelejtette az aktuális felhasználót. A jogkivonat beszerzésére vonatkozó jövőbeli kérések csak akkor lesznek sikeresek, ha az interaktív.

Az ebben a példában szereplő alkalmazás egyetlen felhasználót támogat. A MSAL olyan forgatókönyveket támogat, amelyekben a felhasználó több fiókon is bejelentkezhet. Ilyen például egy e-mail-alkalmazás, amelyben a felhasználók több fiókkal rendelkeznek.

### <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

Adja hozzá a következő metódust a *MainPage.XAML.cs* a jogkivonat alapvető adatainak megjelenítéséhez:

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

#### További információ<a name="more-information-1"></a>

Az **OpenID Connect** használatával beszerzett azonosító tokenek a felhasználóhoz tartozó információk kis részhalmazát is tartalmazzák. `DisplayBasicTokenInfo` a jogkivonatban található alapszintű információkat jeleníti meg. Ez az információ tartalmazza a felhasználó megjelenítendő nevét és AZONOSÍTÓját. Tartalmazza továbbá a jogkivonat lejárati dátumát és a hozzáférési jogkivonatot jelölő karakterláncot is. Ha többször is bejelöli a **hívás Microsoft Graph API** -t, látni fogja, hogy ugyanazt a tokent használták újra a későbbi kérésekhez. A lejárat dátumát is megtekintheti, ha a MSAL úgy dönt, hogy ideje megújítani a jogkivonatot.

### <a name="display-message"></a>Üzenet megjelenítése

Adja hozzá a következő új metódust a *MainPage.XAML.cs*:

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

Most regisztrálnia kell az alkalmazást:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza ki **Azure Active Directory** > **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget. Adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára, például UWP- *app-Calling-MSGraph*.
1. A **támogatott fióktípus**területen válassza a **fiókok lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox)** , majd a folytatáshoz válassza a **regisztráció** lehetőséget.
1. Az Áttekintés oldalon keresse meg az **alkalmazás (ügyfél) azonosítójának** értékét, és másolja. Lépjen vissza a Visual studióba, nyissa meg a *MainPage.XAML.cs*, és cserélje le a `ClientId` értékét ezzel az értékkel.

Az alkalmazás hitelesítésének konfigurálása:

1. A [Azure Portal](https://portal.azure.com)vissza a **kezelés**alatt válassza a **hitelesítés**lehetőséget.
1. Az **átirányítási URI** - **k | a nyilvános ügyfelek számára javasolt átirányítási URI-k (mobil, asztali)** szakaszban keresse meg a **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Kattintson a **Mentés** gombra.

API-engedélyek konfigurálása az alkalmazáshoz:

1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. Válassza az **engedély hozzáadása** lehetőséget, és győződjön meg arról, hogy a **Microsoft API-kat**választotta.
1. Válassza a **Microsoft Graph**lehetőséget.
1. Válassza a **delegált engedélyek**lehetőséget, keresse meg a *felhasználót. olvassa el* , és ellenőrizze, hogy a **felhasználó. Read** van-e kiválasztva.
1. Ha módosította a módosításokat, válassza az **engedélyek hozzáadása** lehetőséget a mentéshez.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)

Ha egy összevont Azure AD-tartománnyal együtt használja a Windows-integrációs hitelesítést, az alkalmazás jegyzékfájljának engedélyeznie kell a további képességeket. Térjen vissza az alkalmazáshoz a Visual Studióban.

1. Nyissa meg a *Package. appxmanifest*.
1. Válassza a **képességek** lehetőséget, és engedélyezze a következő beállításokat:

   * **Vállalati hitelesítés**
   * **Magánhálózatok (ügyfél & kiszolgáló)**
   * **Megosztott felhasználói tanúsítványok**

> [!IMPORTANT]
> Az [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa) alapértelmezés szerint nincs konfigurálva ehhez a mintához. A `Enterprise Authentication` vagy `Shared User Certificates` képességeket kérő alkalmazások magasabb szintű ellenőrzést igényelnek a Windows áruházban. Emellett nem minden fejlesztő szeretné végrehajtani a magasabb szintű ellenőrzést. Ezt a beállítást csak akkor engedélyezze, ha egy összevont Azure AD-tartománnyal rendelkező integrált Windows-hitelesítésre van szüksége.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás teszteléséhez válassza az F5 billentyűt a projekt futtatásához a Visual Studióban. Megjelenik a fő ablak:

![Alkalmazás felhasználói felülete](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Ha készen áll a tesztelésre, válassza a **Microsoft Graph API meghívása**lehetőséget. Ezután jelentkezzen be egy Azure AD-beli szervezeti fiókkal vagy egy Microsoft-fiók, például live.com vagy outlook.com használatával. Amikor a felhasználó először futtatja ezt, az alkalmazás megjeleníti a felhasználót a bejelentkezést kérő ablakot.

### <a name="consent"></a>Hozzájárulása

Amikor először jelentkezik be az alkalmazásba, a következőhöz hasonló beleegyezési képernyő jelenik meg. Válassza az **Igen** lehetőséget a hozzáféréshez való explicit beleegyezéshez:

![Hozzáférési beleegyezési képernyő](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Várt eredmények

A Microsoft Graph API-hívás által visszaadott felhasználói profil adatait az **API-hívás eredményei** képernyőn láthatja:

![API-hívás eredményei képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

A jogkivonat **információi** mezőben a `AcquireTokenInteractive` vagy `AcquireTokenSilent` által beszerzett jogkivonatra vonatkozó alapvető információk is megjelennek:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|`Username` |`user@domain.com` |A felhasználót azonosító Felhasználónév.|
|`Token Expires` |`DateTime` |A jogkivonat lejárati ideje. A MSAL a jogkivonatot szükség szerint megújítva kiterjeszti a lejárati dátumot.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a `user.read` hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Ezt a hatókört alapértelmezés szerint a rendszer minden olyan alkalmazásban hozzáadja, amely az alkalmazás regisztrációs portálján van regisztrálva. A Microsoft Graph és az egyéni API-k egyéb API-jai a háttér-kiszolgáló számára további hatókörökre is szükség lehet. A Microsoft Graph API-nak például a `Calendars.Read` hatókört kell megadnia a felhasználó naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a `Calendars.Read` delegált engedélyt az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a `Calendars.Read` hatókört a `acquireTokenSilent` híváshoz.

> [!NOTE]
> A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználóknak.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1\. probléma

Az alábbi hibaüzenetek egyike jelenik meg, amikor bejelentkezik az alkalmazásba egy összevont Azure AD-tartományban:

* Nem található érvényes ügyféltanúsítvány a kérelemben.
* A felhasználó tanúsítványtárolójában nem található érvényes tanúsítvány.
* Próbálkozzon újra egy másik hitelesítési módszer kiválasztásával.

Ok: a vállalati és a tanúsítvány képességei nem engedélyezettek.

Megoldás: kövesse az [integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)](#enable-integrated-authentication-on-federated-domains-optional)című témakör lépéseit.

### <a name="issue-2"></a>2\. probléma

Az [integrált hitelesítést az összevont tartományokon](#enable-integrated-authentication-on-federated-domains-optional) engedélyezheti, és a Windows Hello használatával a Windows 10 rendszerű számítógépeken is megpróbálhatja bejelentkezni egy olyan környezetre, amelyen a multi-Factor Authentication konfigurálva van. A tanúsítványok listája jelenik meg. Ha azonban úgy dönt, hogy a PIN-kódot használja, a PIN-kód ablak soha nem jelenik meg.

Ok: Ez a probléma a webes hitelesítési ügynök ismert korlátozása a Windows 10 asztali verzióján futó UWP-alkalmazásokban. Jól működik a Windows 10 Mobile-ban.

Megkerülő megoldás: válassza a **Bejelentkezés más beállításokkal lehetőséget**. Ezután válassza a **Bejelentkezés felhasználónévvel és jelszóval**lehetőséget. Válassza **a jelszó megadása**lehetőséget. Ezután folytassa a telefonos hitelesítési folyamaton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával:

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
