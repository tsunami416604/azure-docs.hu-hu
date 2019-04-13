---
title: A Microsoft identity platform UWP-első lépések |} Az Azure
description: Univerzális Windows-Platformos alkalmazások (UWP) hogyan meghívhatja az API-k, a Microsoft identity platform végpont által hozzáférési jogkivonatok igénylő.
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bd57b3d41ad7c670b5423f10a9c93b55e87d757
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522792"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>A Microsoft Graph API meghívása (XAML) az univerzális Windows Platform-alkalmazásból

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ez az útmutató azt ismerteti, hogyan univerzális Windows Platform (UWP) natív alkalmazások hozzáférési jogkivonat kérése és majd a Microsoft Graph API meghívása. Az útmutató más API-k a Microsoft identity platform végpontról hozzáférési jogkivonatok igénylő is vonatkozik.

Ez az útmutató végén az alkalmazás meghívja a védett API személyes fiókok használatával. Példák:, Outlook.com-os, live.com és mások. Az alkalmazás is meghívja a munkahelyi és iskolai fiókok, bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directory (Azure AD).

>[!NOTE]
> Ez az útmutató a Visual Studio 2017 az univerzális Windows Platform fejlesztési telepítve van szükség. Lásd: [beállításához](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) az utasításokat követve töltse le és az univerzális Windows-Platformos alkalmazások fejlesztése a Visual Studio konfigurálása.

## <a name="how-this-guide-works"></a>Az útmutató működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ez az útmutató létrehoz egy minta UWP-alkalmazás, amely a Microsoft Graph API vagy egy webes API-t, amely a Microsoft identity platform végpontról jogkivonatokat fogad. Ebben a forgatókönyvben egy token hozzá az engedélyezési fejléc via HTTP-kérelmekre. A Microsoft-hitelesítési tár (MSAL) token beszerzését és a megújítások kezeli.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Kódtár|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban egy Windows asztali .NET-alkalmazás (XAML) integrálása részletesen bemutatja a *Bejelentkezés Microsoft-*. Ezután lekérdezheti a webes API-k, amelyek egy jogkivonatot, például a Microsoft Graph API-t igényelnek.

Ez az útmutató létrehoz egy alkalmazás, amely egy gomb megjeleníti a lekérdezéseket a Graph API, a Kijelentkezés gombbal és a szövegmezők, amely a hívások eredményeinek megjelenítéséhez.

> [!NOTE]
> Szeretné ezt a mintát a Visual Studio-projekt letöltése helyett? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) , és ugorjon a [alkalmazásregisztráció](#register-your-application "alkalmazás lépésben") lépéssel konfigurálhatja a kódmintát, futtatásuk előtt.

### <a name="create-your-application"></a>Az alkalmazás létrehozása

1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**.
2. A **sablonok**válassza **Visual C#**.
3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet.
4. Nevezze el az alkalmazást, és válassza ki **OK**.
5. Ha a rendszer kéri, válassza ki bármelyik verziója **cél** és **minimális** verziókat, és válassza ki **OK**.

    >![Minimális és a cél-verziók](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft-hitelesítési tár hozzáadása a projekthez
1. A Visual Studióban válassza a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemet.
2. Másolja és illessze be a következő parancsot a **Package Manager Console** ablakban:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Ez a parancs telepíti [Microsoft-hitelesítési tár](https://aka.ms/msal-net). Az MSAL beszerzi, gyorsítótárazza, és frissíti a felhasználói jogkivonatokhoz, amelyek a Microsoft identity platform által védett API-k elérésére.

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói felület létrehozása

A **MainPage.xaml** fájl jön létre automatikusan a projektsablon részeként. Nyissa meg ezt a fájlt, és kövesse az utasításokat:

* Cserélje le az alkalmazás **rács** csomópont a következő kóddal:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Használható az MSAL egy token beszerzése a Microsoft Graph API-hoz

Ez a szakasz bemutatja, hogyan használható az MSAL egy token beszerzése a Microsoft Graph API-hoz.

1.  A **MainPage.xaml.cs**, adja hozzá a hivatkozást az MSAL az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Cserélje le a kódot a <code>MainPage</code> osztályban az alábbi kódra:

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
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
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

Hívása a `AcquireTokenInteractive` módszer eredményezi egy ablak, amely felkéri a felhasználót, hogy jelentkezzen be. Alkalmazások általában felhasználóktól interaktívan jelentkezik be először egy védett erőforrás eléréséhez szükséges. Akkor is előfordulhat, hogy kell bejelentkezni, amikor egy token beszerzéséhez a beavatkozás nélküli művelet sikertelen. Ez például akkor, amikor a felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus kezeli a token beszerzését és a megújítások felhasználói beavatkozás nélkül. Után `AcquireTokenInteractive` hajtja végre az első alkalommal kéri a felhasználót, a hitelesítő adatokat, és a `AcquireTokenSilent` metódus használandó jogkivonatokat későbbi hívások kérelmet, mert a jogkivonatok csendes megvásárolja. Az MSAL tokengyorsítótárral és -megújítás fogja kezelni.

Végül a `AcquireTokenSilent` metódus sikertelen. A hiba oka lehet, hogy felhasználók rendelkezik-e vagy kijelentkeztetése, vagy módosítani a jelszavát egy másik eszközön. Ha az MSAL észleli, hogy a probléma megoldhatók egy interaktív intézkedést kér, akkor aktiválódik egy `MsalUiRequiredException` kivétel. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

* Azt is ellenőrizze, egy hívást kell végrehajtanunk `AcquireTokenInteractive` azonnal. Ez a hívás eredménye kéri a felhasználót, hogy jelentkezzen be. Általában ez a minta az online alkalmazások használják, ha a felhasználó nem érhető el kapcsolat nélküli tartalom. A minta az interaktív telepítés által létrehozott mintát követi. A minta futtatása művelet az első alkalommal megjelenik.
  * Mivel a felhasználó nem használta az alkalmazás `accounts.FirstOrDefault()` obsahuje hodnotu null, és a egy `MsalUiRequiredException` kivétel történt.
  * A kód a minta ezután kezeli a kivételt meghívásával `AcquireTokenInteractive`. Ez a hívás eredménye kéri a felhasználót, hogy jelentkezzen be.

* Vagy ehelyett megadja egy vizuális jelzés a felhasználók számára, hogy egy interaktív bejelentkezési megadása kötelező. Ezután és kiválaszthatja a megfelelő időben való bejelentkezéshez. Vagy az alkalmazás megpróbálhatja `AcquireTokenSilent` később. Ezt a mintát gyakran, használatos, amikor a felhasználók használhatják a más megszakítása nélkül az alkalmazás funkciói. Ilyen például, ha offline tartalom érhető el, az alkalmazás. Ebben az esetben felhasználók megadhatja, hogy szeretne hozzáférni a védett erőforrásokhoz, vagy frissítse az elavult adatokat. Ellenkező esetben az alkalmazás dönt, hogy próbálkozzon újra, vagy `AcquireTokenSilent` amikor a hálózat visszaállítása után, átmenetileg nem érhető el.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API meghívása éppen megszerzett jogkivonattal használatával

* Adja hozzá a következő új metódust **MainPage.xaml.cs**. Győződjön meg arról, hogy ezt a módszert használják egy `GET` kérelmet Graph API használatával egy `Authorization` fejléc:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API REST-hívást gondoskodik

A mintaalkalmazásban a `GetHttpContentWithToken` , hogy a HTTP módszert `GET` jogkivonatot igénylő védett erőforrás kérelmet. Ezután a metódus a hívónak a tartalmat adja vissza. Ez a metódus hozzáadja a beszerzett lexikális elem szerepel a **HTTP engedélyezési** fejléc. Ebben a példában az erőforrás a Microsoft Graph API **me** végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adjon meg egy metódust a felhasználó kijelentkeztetése

* Jelentkezzen ki a felhasználót, adja hozzá a következő metódust **MainPage.xaml.cs**:

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
> MSAL.NET aszinkron módszereket használ a tokenek hardvervásárlásra és-fiókok kezelése, és ezért kell gondoskodik a felhasználói felület szála, ezért a UI-ed-műveletek végrehajtása a `Dispatcher.RunAsync`, és a óvintézkedéseket meghívásához `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>További információ a Kijelentkezés

A `SignOutButton_Click` módszer eltávolítja a felhasználót az MSAL felhasználók gyorsítótárból. Ez a módszer gyakorlatilag megmondja a MSAL az aktuális felhasználó törlésére. Ezután egy token beszerzéséhez jövőbeli kérést csak akkor, ha azt interaktív tett sikeres lesz.
Ebben a példában az alkalmazás támogatja az egy-egy felhasználóhoz. Azonban a MSAL forgatókönyvek, ahol egynél több fiókot is bejelentkezhet a egyszerre. Ilyen például az e-mail alkalmazást, amelyhez a felhasználónak van-e a különböző fiókok.

## <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

* Adja hozzá a következő metódust **MainPage.xaml.cs** a token alapvető információit jeleníti meg:

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

Azonosító-jogkivonatokat keresztül beszerzett **OpenID Connect** részhalmazát a felhasználó a profiljával kapcsolatos információkat is tartalmaznak. `DisplayBasicTokenInfo` a jogkivonatban található alapvető információkat jelenít meg. Példák: a felhasználó megjelenített neve és azonosítója, lejárati dátuma a jogkivonatot, és a karakterlánc, amely a hozzáférési jogkivonat maga jelöli. Ha a **Microsoft Graph API meghívása** többször gombot, látni fogja, hogy további kérésekhez fel lett-e újra ugyanezt a tokent. Megtekintheti a lejárati dátum, amikor MSAL úgy dönt, hogy a token megújításának ideje kiterjesztett is.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most szüksége az alkalmazás regisztrálása a Microsoft alkalmazásregisztrációs portálon:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiók több Azure AD-bérlőben található, válassza ki a `Directory + Subscription` : a menüben a lapot, és a kapcsoló felül a jobb felső sarokban a portál munkamenet a kívánt Azure ad-bérlőben.
1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
1. Válassza ki **új regisztrációs**.
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `UWP-App-calling-MSGraph`).
   - Az a **támogatott fióktípusok** szakaszban jelölje be **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)**.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **áttekintése** lapon, keresse meg a **Alkalmazásazonosítót (ügyfél)** értékét, és jegyezze fel későbbi használatra. Lépjen vissza a Visual Studio, a nyílt **MainPage.xaml.cs**, és ClientId értékét cserélje le az imént regisztrált alkalmazás azonosítója:
1. Az alkalmazás oldalak listájából válassza ki **hitelesítési**:
   - Az a **átirányítási URI-k** | **javasolt átirányítási URI-k nyilvános ügyfelek (mobil, asztali verzió)** területen ellenőrzés **urn: ietf:wg:oauth:2.0:oob**
1. Kattintson a **Mentés** gombra.
1. Az alkalmazás oldalak listájából válassza ki **API-engedélyek**
   - Kattintson a **adjon hozzá egy engedélyt** gombra, majd,
   - Ügyeljen arra, hogy a **Microsoft API-k** lap van kiválasztva
   - Az a *leggyakrabban használt Microsoft APIs* területén kattintson a **Microsoft Graph**
   - Az a **delegált engedélyek** területen győződjön meg arról, hogy a rendszer ellenőrzi a megfelelő engedélyekkel: **User.Read**. Ha szükséges, használja a keresőmezőt.
   - Válassza ki a **engedélyek hozzáadása** gomb

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Összevont tartományok (nem kötelező) az integrált hitelesítés engedélyezése

AD-tartományt, az alkalmazás jegyzékfájlja kell Windows-Integrated hitelesítés engedélyezése az összevont Azure-ral használva további képességek engedélyezéséhez:

1. Kattintson duplán a **Package.appxmanifest**.
2. Válassza ki a **képességek** lapra, és győződjön meg arról, hogy engedélyezve vannak-e a következő beállításokat:

    - Vállalati hitelesítési
    - Magánhálózatok (ügyfél és kiszolgáló)
    - Megosztott felhasználói tanúsítványok

> [!IMPORTANT]
> [Integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa) nincs konfigurálva ehhez a mintához alapértelmezés szerint. Az alkalmazásokat, amelyek a kérelem *vállalati hitelesítési* vagy *megosztott felhasználói tanúsítványok* lehetőségekhez szükség magasabb szintű ellenőrzés a Windows Store. Emellett nem minden fejlesztő kíván végrehajtani, a magasabb szintű ellenőrzést. Engedélyezi ezt a beállítást, csak akkor, ha egy összevont Azure-integrált Windows-hitelesítés van szüksége Active Directory-tartománynak.

## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás teszteléséhez válassza ki az F5 billentyűt a projekt futtatása a Visual Studióban. A fő ablakban jelenik meg:

![Alkalmazás felhasználói felületén](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Amikor elkészült, teszteléséhez, válassza ki a **Microsoft Graph API meghívása**. Az Azure AD szervezeti fiókkal vagy egy Microsoft-fiók, például live.com vagy Outlook.com-os, majd szokott bejelentkezni. Ha most először, megjelenik egy ablak, amely kéri a felhasználót, hogy jelentkezzen be:

![Bejelentkezési oldal](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Hozzájárulás

Az első alkalommal jelentkezik be az alkalmazás megjelenik a jóváhagyást kérő képernyőt, az alábbihoz hasonló. Válassza ki **Igen** explicit módon jóváhagyást eléréséhez:

![Hozzáférés beleegyezést kérő oldalon](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Várt eredmények

Felhasználói profil adatait a Microsoft Graph API-hívás által visszaadott láthatja a **API-hívási eredmények** képernyőjén:

![API-hívási eredmények képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

A jogkivonat-n keresztül beszerzett alapvető adatait is megjelenik `AcquireTokenInteractive` vagy `AcquireTokenSilent` a a **Tártoken adatainak** mezőbe:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely azonosítja a felhasználót.|
|**Jogkivonat lejár** |DateTime |Az az időpont, amikor a jogkivonat érvényessége lejár. Az MSAL szükség szerint a token megújítása kiterjeszti a lejárati dátumot.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben automatikusan hozzáadódik alapértelmezés szerint a minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon. Más Microsoft Graph API-k, és az egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a *Calendars.Read* delegált engedéllyel az alkalmazás regisztrációs adatok. Majd adja hozzá a *Calendars.Read* a hatókört a `acquireTokenSilent` hívja.

> [!NOTE]
> Előfordulhat, hogy rendszer kér a felhasználótól további címtárbérlőhöz hatókörök számának növelésével.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1 probléma

Egyike jelenik meg a következő hibaüzenetek az alkalmazás összevont Azure-beli bejelentkezéskor AD-tartomány:

* Nem található a kérelemben érvényes ügyféltanúsítványt.
* Nem a felhasználó tanúsítványtárolójában található érvényes tanúsítvány.
* Válasszon egy másik hitelesítési módszert.

**OK:** Nincsenek engedélyezve a vállalati és a tanúsítvány képességeket.

**Megoldás:** Kövesse a [integrált hitelesítést az összevont tartományok](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>2 probléma

Engedélyezi a [integrált hitelesítést az összevont tartományok](#enable-integrated-authentication-on-federated-domains-optional) , és próbálja meg a Windows Hello egy Windows 10 rendszerű számítógépre való bejelentkezéshez használt az adott környezetben, a konfigurált többtényezős hitelesítéssel. Tanúsítványok listája jelenik meg. Azonban ha a PIN-kód használatát választja, a PIN-kód ablak soha nem jelenik meg.

**OK:** A probléma a webeshitelesítés-szervező UWP-alkalmazásokban, a Windows 10 asztali verzióját futtató egy ismert korlátozás. Windows 10 Mobile jól működik.

**Megkerülő megoldás:** Válassza ki **jelentkezzen be az egyéb lehetőségek**. Válassza ki **jelentkezzen be egy felhasználónevet és jelszót**. Válassza ki **adja meg a jelszót**. Folytassa a telefonos hitelesítési folyamat.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
