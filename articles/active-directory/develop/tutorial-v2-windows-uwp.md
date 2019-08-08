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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519a86bed6a3a09b476bce6435ae666d655dbe03
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852267"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph API meghívása Univerzális Windows-platform alkalmazásból (XAML)

> [!div renderon="docs"]

Ez az útmutató azt ismerteti, hogy egy natív Univerzális Windows-platform (UWP) alkalmazás hogyan igényelhet hozzáférési jogkivonatot, majd meghívja Microsoft Graph API-t. Az útmutató más API-kra is vonatkozik, amelyek hozzáférési jogkivonatokat igényelnek a Microsoft Identity platform végpontján.

Az útmutató végén az alkalmazás személyes fiókok használatával meghívja a védett API-t. Ilyenek például a outlook.com, a live.com és mások. Az alkalmazás a munkahelyi és iskolai fiókokat is meghívja bármely olyan vállalattól vagy szervezettől, amely Azure Active Directory (Azure AD).

>[!NOTE]
> Ehhez az útmutatóhoz a Visual Studio 2017-es verzióra van szükség Univerzális Windows-platform-fejlesztéssel. Az Univerzális Windows-platform alkalmazások fejlesztéséhez tekintse [meg a bevezetés](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) a Visual Studio letöltésére és konfigurálására vonatkozó utasításokat.

## <a name="how-this-guide-works"></a>Az útmutató működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ez az útmutató egy minta UWP-alkalmazást hoz létre, amely lekérdezi Microsoft Graph API-t vagy egy webes API-t, amely a Microsoft Identity platform végpontból fogad jogkivonatokat. Ebben az esetben a rendszer egy jogkivonatot ad hozzá a HTTP-kérelmekhez az engedélyezési fejlécen keresztül. A Microsoft Authentication Library (MSAL) kezeli a tokenek beszerzését és megújítását.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagokat használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen ismerteti a Windows asztali .NET-alkalmazások (XAML) integrálását a Microsofttalvaló bejelentkezéssel. Ezután lekérdezheti a tokent igénylő webes API-kat, például Microsoft Graph API-t.

Ez az útmutató egy olyan alkalmazást hoz létre, amely egy Graph API, egy kijelentkezési gomb és a hívások eredményét megjelenítő szövegmezők lekérdezését jeleníti meg.

> [!NOTE]
> Szeretné letölteni ezt a mintát a Visual Studio-projektből? [Töltsön le egy projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) , és ugorjon az [alkalmazás regisztrációs](#register-your-application "alkalmazás regisztrációja") lépésre, és állítsa be a kód mintáját a Futtatás előtt.

### <a name="create-your-application"></a>Az alkalmazás létrehozása

1. A Visual Studióban válassza a **fájl** > **új** > **projekt**lehetőséget.
2. A **sablonok**területen válassza **a C#vizualizáció** elemet.
3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet.
4. Adja meg az alkalmazás nevét, majd kattintson **az OK gombra**.
5. Ha a rendszer kéri, válassza ki a kívánt verziót a **cél** és a **minimum** verziók számára, majd kattintson **az OK gombra**.

    >![Minimális és a célként megadott verziók](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft hitelesítési függvénytár hozzáadása a projekthez
1. A Visual Studióban válassza a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemet.
2. Másolja és illessze be a következő parancsot a **Package Manager konzol** ablakában:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Ez a parancs telepíti a [Microsoft hitelesítési tárat](https://aka.ms/msal-net). A MSAL a Microsoft Identity platform által védett API-khoz hozzáférő felhasználói jogkivonatok beszerzését, gyorsítótárazását és frissítését végzi.

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói felületének létrehozása

A rendszer automatikusan létrehoz egy **Főoldal. XAML** fájlt a projekt sablonjának részeként. Nyissa meg ezt a fájlt, majd kövesse az utasításokat:

* Cserélje le az alkalmazás **Grid** csomópontját a következő kódra:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Token beszerzése Microsoft Graph API-hoz a MSAL használatával

Ez a szakasz bemutatja, hogyan használható a MSAL a Microsoft Graph API-hoz tartozó jogkivonat lekéréséhez.

1.  A **MainPage.XAML.cs**-ben adja hozzá a MSAL hivatkozást a osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Cserélje le az <code>MainPage</code> osztály kódját a következő kódra:

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
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
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
    ```

### <a name="more-information"></a>További információ

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A `AcquireTokenInteractive` metódus hívása olyan ablakot eredményez, amely felszólítja a felhasználókat, hogy jelentkezzenek be. Az alkalmazások általában megkövetelik, hogy a felhasználóknak interaktívan kell bejelentkezniük a védett erőforrásokhoz való első bejelentkezéshez. Előfordulhat, hogy be kell jelentkezniük, amikor a token beszerzésére irányuló csendes művelet meghiúsul. Ilyen eset például, ha egy felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. Miután `AcquireTokenInteractive` első alkalommal futtatja a felhasználót, és a rendszer kéri a felhasználótól a hitelesítő adatokat, a `AcquireTokenSilent` metódust kell használnia a további hívások jogkivonatának igénylésére, mert a tokeneket csendesen szerzi. A MSAL a jogkivonat-gyorsítótárat és a megújítást fogja kezelni.

A `AcquireTokenSilent` metódus végül sikertelen lesz. A hiba oka az lehet, hogy a felhasználók vagy kijelentkezve vagy más eszközön módosították a jelszavukat. Ha a MSAL észleli, hogy a probléma megoldásához interaktív műveletre van szükség, `MsalUiRequiredException` kivételt okoz. Az alkalmazás két módon tudja kezelni ezt a kivételt:

* A hívás `AcquireTokenInteractive` azonnal elvégezhető. Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be. Ezt a mintát általában olyan online alkalmazásokban használják, ahol nincs elérhető offline tartalom a felhasználó számára. Az irányított telepítő által generált minta követi a mintát. A minta első futtatásakor ez a művelet jelenik meg.
  * Mivel egyetlen felhasználó sem használta az alkalmazást, `accounts.FirstOrDefault()` null értéket tartalmaz, `MsalUiRequiredException` és kivétel keletkezik.
  * A mintában szereplő kód a kivételt a hívásával `AcquireTokenInteractive`kezeli. Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be.

* Ehelyett olyan vizualizációs jelzést jelenít meg a felhasználóknak, akik interaktív bejelentkezést igényelnek. Ezután kiválaszthatják a megfelelő időpontot a bejelentkezéshez. Vagy az alkalmazás később is `AcquireTokenSilent` újrapróbálkozhat. Ez a minta gyakran használatos, ha a felhasználók más alkalmazás-funkciókat is használhatnak megszakítás nélkül. Ilyen eset például, ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók dönthetnek arról, hogy mikor szeretnének bejelentkezni a védett erőforrás eléréséhez vagy az elavult információk frissítéséhez. Ellenkező esetben az alkalmazás úgy is dönthet, `AcquireTokenSilent` hogy újra próbálkozik, amikor a hálózat helyreállt, miután átmenetileg nem volt elérhető.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Microsoft Graph API meghívása az imént beszerzett token használatával

* Adja hozzá a következő új metódust a **MainPage.XAML.cs**. Ezzel a módszerrel a `GET` Graph API egy `Authorization` fejléc használatával lehet kérelmet készíteni:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Ebben a példában a `GetHttpContentWithToken` metódus használatával http `GET` -kérést lehet végezni egy olyan védett erőforráson, amely jogkivonatot igényel. Ezután a metódus visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a **http-engedélyezési** fejlécben. Ebben a példában az erőforrás a Microsoft Graph API **Me** -végpontja, amely megjeleníti a felhasználó profiljának adatait.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Metódus hozzáadása a felhasználó kijelentkezéséhez

* A felhasználó kijelentkezéséhez adja hozzá a következő metódust a **MainPage.XAML.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> A MSAL.net aszinkron metódusokat használ a jogkivonatok beszerzéséhez vagy a fiókok kezeléséhez, ezért ügyelnie kell arra, hogy a felhasználói felületi szálban `Dispatcher.RunAsync`műveleteket hajtson végre a felhasználói felületen, ezért a`ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>További információ a kijelentkezésről

A `SignOutButton_Click` metódus eltávolítja a felhasználót a MSAL felhasználói gyorsítótárból. Ez a módszer gyakorlatilag azt jelzi, hogy a MSAL elfelejtette az aktuális felhasználót. Ezután a jogkivonat beszerzésére irányuló jövőbeli kérések csak akkor lesznek sikeresek, ha interaktív.
Az ebben a példában szereplő alkalmazás egyetlen felhasználót támogat. A MSAL azonban olyan forgatókönyveket támogat, amelyekben egyszerre több fiók is regisztrálható. Ilyen például egy e-mail-alkalmazás, amelyben a felhasználók több fiókkal rendelkeznek.

## <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

* Adja hozzá a következő metódust a **MainPage.XAML.cs** a jogkivonat alapvető adatainak megjelenítéséhez:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
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

### <a name="more-information"></a>További információ

Az **OpenID Connect** használatával beszerzett azonosító tokenek a felhasználóhoz tartozó információk kis részhalmazát is tartalmazzák. `DisplayBasicTokenInfo`a jogkivonatban található alapvető információkat jeleníti meg. Ilyenek például a felhasználó megjelenítendő neve és azonosítója, a jogkivonat lejárati dátuma, valamint a hozzáférési jogkivonatot jelölő sztring. Ha többször is bejelöli a **hívás Microsoft Graph API** -t, látni fogja, hogy ugyanazt a jogkivonatot használta-e újra a további kérésekhez. A lejárat dátumát is megtekintheti, ha a MSAL úgy dönt, hogy ideje megújítani a jogkivonatot.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most regisztrálnia kell az alkalmazását a Microsoft Application Registration portálon:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja egynél több Azure ad-bérlőn található, válassza `Directory + Subscription` a lap tetején található menü jobb felső sarkát, és váltson a portál munkamenetére a kívánt Azure ad-bérlőre.
1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `UWP-App-calling-MSGraph`).
   - A **támogatott fióktípus** szakaszban válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)** .
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Lépjen vissza a Visual studióba, nyissa meg a **MainPage.XAML.cs**, és cserélje le a ClientId értéket az imént regisztrált alkalmazás-azonosítóra:
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
   1. Az **átirányítási URI** -k szakasz átirányítási URI-k listájában:
   1. A **típus** oszlopban válassza a **nyilvános ügyfél (mobil & asztali)** lehetőséget.
   1. Adja `urn:ietf:wg:oauth:2.0:oob` meg az **átirányítási URI** oszlopot.
1. Kattintson a **Mentés** gombra.
1. Az alkalmazáshoz tartozó lapok listájában válassza az **API-engedélyek** lehetőséget.
   - Kattintson az **engedély hozzáadása** gombra, majd
   - Győződjön meg arról, hogy a **Microsoft API** lap van kijelölve
   - A *gyakran használt Microsoft API* -k szakaszban kattintson **Microsoft Graph**
   - A **delegált engedélyek** szakaszban győződjön meg arról, hogy a megfelelő engedélyek be vannak jelölve: **Felhasználó. Read**. Ha szükséges, használja a keresőmezőt.
   - Válassza az **engedélyek hozzáadása** gombot

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)

Ha egy összevont Azure AD-tartománnyal való használat esetén engedélyezni szeretné a Windows-integrációs hitelesítést, az alkalmazás jegyzékfájljának további képességeket kell engedélyeznie:

1. Kattintson duplán a **Package. appxmanifest**elemre.
2. Válassza a **képességek** fület, és győződjön meg arról, hogy a következő beállítások engedélyezve vannak:

    - Vállalati hitelesítés
    - Magánhálózatok (ügyfél & kiszolgáló)
    - Megosztott felhasználói tanúsítványok

> [!IMPORTANT]
> Az [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa) alapértelmezés szerint nincs konfigurálva ehhez a mintához. A *vállalati hitelesítést* vagy *megosztott felhasználói tanúsítványokat* kérő alkalmazások a Windows áruház magasabb szintű ellenőrzését igénylik. Emellett nem minden fejlesztő szeretné végrehajtani a magasabb szintű ellenőrzést. Ezt a beállítást csak akkor engedélyezze, ha egy összevont Azure AD-tartománnyal rendelkező integrált Windows-hitelesítésre van szüksége.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás teszteléséhez válassza az F5 billentyűt a projekt futtatásához a Visual Studióban. Megjelenik a fő ablak:

![Alkalmazás felhasználói felülete](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Ha készen áll a tesztelésre, válassza a **Microsoft Graph API**meghívása lehetőséget. Ezután jelentkezzen be egy Azure AD-beli szervezeti fiókkal vagy egy Microsoft-fiók, például live.com vagy outlook.com használatával. Ha első alkalommal jelenik meg, megjelenik egy ablak, amely arra kéri a felhasználót, hogy jelentkezzen be:

![Bejelentkezési oldal](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Hozzájárulás

Amikor először jelentkezik be az alkalmazásba, a következőhöz hasonló beleegyezési képernyő jelenik meg. Válassza az **Igen** lehetőséget a hozzáféréshez való explicit beleegyezéshez:

![Hozzáférési beleegyezési képernyő](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Várt eredmények

A Microsoft Graph API-hívás által visszaadott felhasználói profil adatait az **API-hívás eredményei** képernyőn láthatja:

![API-hívás eredményei képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

A jogkivonat- `AcquireTokenInteractive` **információ** mezőben a vagy `AcquireTokenSilent` a használatával beszerzett jogkivonatra vonatkozó alapvető információk is megjelennek:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználót azonosító Felhasználónév.|
|**A jogkivonat lejár** |Datetime |A jogkivonat lejárati ideje. A MSAL a jogkivonatot szükség szerint megújítva kiterjeszti a lejárati dátumot.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API- nak a felhasználónak kell olvasnia a hatókört a felhasználói profil olvasásához. A rendszer alapértelmezés szerint automatikusan hozzáadja ezt a hatókört minden olyan alkalmazáshoz, amely az alkalmazás regisztrációs portálján van regisztrálva. A Microsoft Graph többi API-jának és a háttér-kiszolgáló egyéni API-jai további hatóköröket igényelhetnek. Microsoft Graph API-nak a naptárakat kell megadnia *. olvasási* hatókör a felhasználók naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a *naptárakat. olvassa el* a delegált jogosultságokat az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *naptárak. Read* hatókört `acquireTokenSilent` a híváshoz.

> [!NOTE]
> A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználóknak.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1\. probléma

Az alábbi hibaüzenetek egyike jelenik meg, amikor bejelentkezik az alkalmazásba egy összevont Azure AD-tartományban:

* Nem található érvényes ügyféltanúsítvány a kérelemben.
* A felhasználó tanúsítványtárolójában nem található érvényes tanúsítvány.
* Próbálkozzon újra egy másik hitelesítési módszer kiválasztásával.

**Okozhat** A vállalati és a tanúsítvány képességei nem engedélyezettek.

**Megoldás** Kövesse az [integrált hitelesítés](#enable-integrated-authentication-on-federated-domains-optional)lépéseit összevont tartományokon.

### <a name="issue-2"></a>2\. probléma

Az [integrált hitelesítést az összevont tartományokon](#enable-integrated-authentication-on-federated-domains-optional) engedélyezheti, és a Windows Hello használatával a Windows 10 rendszerű számítógépeken is megpróbálhatja bejelentkezni egy olyan környezetre, amelyen a multi-Factor Authentication konfigurálva van. A tanúsítványok listája jelenik meg. Ha azonban úgy dönt, hogy a PIN-kódot használja, a PIN-kód ablak soha nem jelenik meg.

**Okozhat** Ez a probléma a webes hitelesítési ügynök ismert korlátozása a Windows 10 asztali verzióján futó UWP-alkalmazásokban. Jól működik a Windows 10 Mobile-ban.

**Workaround** Válassza **a bejelentkezés más beállításokkal lehetőséget**. Ezután válassza a **Bejelentkezés felhasználónévvel és jelszóval**lehetőséget. Válassza **a jelszó megadása**lehetőséget. Ezután folytassa a telefonos hitelesítési folyamaton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
