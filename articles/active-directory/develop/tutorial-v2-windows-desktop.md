---
title: Ismerkedés a Microsoft Identity platform Windows asztalával
description: Egy Windowsos asztali .NET-(XAML-) alkalmazás hozzáférési jogkivonatának beszerzése és a Microsoft Identity platform által védett API meghívása.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a865bab690c79288bdffcd7cebe424d1bb1969c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181536"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API meghívása egy Windowsos asztali alkalmazásból

Ez az útmutató azt mutatja be, hogy a natív Windowsos asztali .NET-(XAML-) alkalmazások hogyan használják a hozzáférési jogkivonatot a Microsoft Graph API meghívásához. Az alkalmazás más API-khoz is hozzáférhet, amelyek hozzáférési jogkivonatokat igényelnek a Microsoft Identity platform for Developers 2.0-s végpontján. Ezt a platformot korábban Azure AD-nek nevezték.

Az útmutató befejezése után az alkalmazás képes lesz meghívni egy védett API-t, amely személyes fiókokat (például outlook.com, live.com és másokat) használ. Az alkalmazás a Azure Active Directoryt használó vállalattól vagy szervezettől származó munkahelyi és iskolai fiókokat is használni fogja.

> [!NOTE]
> Az útmutatóhoz a Visual Studio 2015 Update 3, a Visual Studio 2017 vagy a Visual Studio 2019 szükséges. Nem rendelkezik ezekkel a verziókkal? [Töltse le ingyen a Visual Studio 2019](https://www.visualstudio.com/downloads/)-es verzióját.

>[!NOTE]
> Ha még nem ismeri a Microsoft Identity platformot, javasoljuk, hogy kezdje a [token beszerzése és a Microsoft Graph API meghívása egy Windows asztali alkalmazásból](quickstart-v2-windows-desktop.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Az ezzel az útmutatóval létrehozott minta alkalmazás lehetővé teszi egy Windows asztali alkalmazás számára, amely lekérdezi a Microsoft Graph API-t vagy egy webes API-t, amely a Microsoft Identity-platform végpontból fogad jogkivonatokat. Ebben a forgatókönyvben egy jogkivonatot ad hozzá a HTTP-kérelmekhez az engedélyezési fejlécen keresztül. A Microsoft Authentication Library (MSAL) kezeli a tokenek beszerzését és megújítását.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>A jogkivonatok beszerzése a védett webes API-k eléréséhez

A felhasználó hitelesítése után a minta alkalmazás tokent kap, amellyel lekérdezheti Microsoft Graph API-t vagy a Microsoft Identity platform által védett webes API-t a fejlesztők számára.

Az API-k, például a Microsoft Graph jogkivonatot igényelnek az adott erőforrásokhoz való hozzáférés engedélyezéséhez. Például egy jogkivonat szükséges a felhasználói profil olvasásához, a felhasználó naptárának eléréséhez vagy e-mailek küldéséhez. Az alkalmazás az API-hatókörök megadásával kérhet hozzáférési jogkivonatot a MSAL használatával az erőforrások eléréséhez. A rendszer ezt a hozzáférési jogkivonatot adja hozzá a HTTP-engedélyezési fejléchez a védett erőforráson végrehajtott összes híváshoz.

A MSAL kezeli a gyorsítótárazást és a hozzáférési jogkivonatok frissítését, így az alkalmazásnak nem kell.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagokat használja:

|Kódtár|Leírás|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban egy új projektet hoz létre, amely bemutatja, hogyan integrálhat egy Windows asztali .NET-alkalmazást (XAML) a *Microsofttal való bejelentkezéssel* , hogy az alkalmazás képes legyen lekérdezni a tokent igénylő webes API-kat.

Az ezzel az útmutatóval létrehozott alkalmazás egy olyan gombot jelenít meg, amely egy gráf meghívására szolgál, egy olyan területen, amely megjeleníti az eredményeket a képernyőn, valamint egy kijelentkezési gombot.

> [!NOTE]
> Inkább a minta Visual Studio-projektjét szeretné letölteni? [Töltsön le egy projektet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip), és ugorjon a [konfigurációs lépéshez](#register-your-application) a kód minta konfigurálásához a végrehajtás előtt.
>

Az alkalmazás létrehozásához tegye a következőket:

1. A Visual Studióban válassza a **fájl** > **új** > **projekt**lehetőséget.
2. A **sablonok**területen válassza a **Visual C#** elemet.
3. A használt Visual Studio-verziótól függően válassza a **WPF-alkalmazás (.NET-keretrendszer)** lehetőséget.

## <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez

1. A Visual Studióban válassza az **eszközök** > **NuGet Package**> Manager**csomagkezelő konzolt**.
2. A csomagkezelő konzol ablakban illessze be a következő Azure PowerShell parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Ez a parancs telepíti a Microsoft hitelesítési tárat. A MSAL kezeli a Azure Active Directory v 2.0 által védett API-k eléréséhez használt felhasználói jogkivonatok beszerzését, gyorsítótárazását és frissítését.
    >

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Az alkalmazást kétféleképpen is regisztrálhatja.

### <a name="option-1-express-mode"></a>1. lehetőség: expressz mód

Az alkalmazást gyorsan regisztrálhatja a következő módon:
1. Nyissa meg a [Azure Portal-alkalmazás regisztrációját](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.

### <a name="option-2-advanced-mode"></a>2. lehetőség: speciális mód

Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `Win-App-calling-MsGraph`).
   - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
   1. Az **átirányítási URI** -k szakasz átirányítási URI-k listájában:
   1. A **típus** oszlopban válassza a **nyilvános ügyfél/natív (mobil & asztali)** lehetőséget.
   1. Az **átirányítási URI** oszlopban adja meg a`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Nyissa meg a Visual studiót *App.xaml.cs* , nyissa meg a app.XAML.cs `Enter_the_Application_Id_here` fájlt, majd cserélje le az alábbi kódrészletet az imént regisztrált és MÁSOLt alkalmazás-azonosítóra.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>A kód hozzáadása a MSAL inicializálásához

Ebben a lépésben egy olyan osztályt hoz létre, amely kezeli a MSAL való interakciót, például a jogkivonatok kezelését.

1. Nyissa meg a *app.XAML.cs* fájlt, majd adja hozzá a MSAL-referenciát a osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Az alkalmazás osztályának frissítése a következőre:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Az alkalmazás felhasználói felületének létrehozása

Ez a szakasz azt mutatja be, hogyan lehet egy alkalmazás lekérdezni egy védett háttér-kiszolgálót, például Microsoft Graph.

A *MainWindow. XAML* fájlt automatikusan létre kell hozni a projekt sablonjának részeként. Nyissa meg ezt a fájlt, majd cserélje le az alkalmazás * \<Grid>* csomópontját a következő kódra:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A MSAL használata a Microsoft Graph API-hoz tartozó jogkivonat lekéréséhez

Ebben a szakaszban a MSAL használatával kap tokent a Microsoft Graph API-hoz.

1. A *MainWindow.XAML.cs* fájlban adja hozzá a MSAL hivatkozást a osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Cserélje le `MainWindow` az osztály kódját a következőre:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

### <a name="more-information"></a>További információ

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A `AcquireTokenInteractive` metódus meghívásával egy olyan ablak jelenik meg, amely felszólítja a felhasználókat, hogy jelentkezzenek be. Az alkalmazások általában megkövetelik, hogy a felhasználóknak interaktívan kell bejelentkezniük a védett erőforrásokhoz való első bejelentkezéshez. Előfordulhat, hogy be kell jelentkezniük, amikor egy jogkivonat beszerzésére irányuló csendes művelet meghiúsul (például ha a felhasználó jelszava lejárt).

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. Az `AcquireTokenInteractive` első futtatása után az a szokásos módszer `AcquireTokenSilent` , amellyel a rendszer lekéri a védett erőforrásokhoz hozzáférő jogkivonatokat a további hívásokhoz, mert a kérések vagy a megújítási tokenek csendesen történnek.

Végül a `AcquireTokenSilent` metódus sikertelen lesz. A hiba oka az lehet, hogy a felhasználó vagy más eszközön kijelentkezett vagy módosította a jelszavát. Ha a MSAL észleli, hogy a probléma megoldásához interaktív műveletre van szükség, `MsalUiRequiredException` kivételt okoz. Az alkalmazás két módon tudja kezelni ezt a kivételt:

* A hívás `AcquireTokenInteractive` azonnal elvégezhető. Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be. Ezt a mintát általában olyan online alkalmazásokban használják, ahol nincs elérhető offline tartalom a felhasználó számára. Az irányított telepítő által generált minta követi ezt a mintát, amelyet a művelet első futtatásakor láthat.

* Mivel egyetlen felhasználó sem használta az alkalmazást, `PublicClientApp.Users.FirstOrDefault()` null értéket tartalmaz, és `MsalUiRequiredException` kivétel keletkezik.

* A mintában szereplő kód a kivételt a hívásával `AcquireTokenInteractive`kezeli, ami azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be.

* Ehelyett olyan vizuális jelzést jelenthet a felhasználóknak, akik interaktív bejelentkezést igényelnek, így kiválaszthatják a megfelelő időt a bejelentkezéshez. Vagy az alkalmazás később is `AcquireTokenSilent` újrapróbálkozhat. Ezt a mintát gyakran használják, ha a felhasználók más alkalmazás-funkciókat is használhatnak megszakítás nélkül – például ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók dönthetnek arról, hogy mikor szeretnének bejelentkezni a védett erőforrás eléréséhez vagy az elavult információk frissítéséhez. Másik lehetőségként az alkalmazás is dönthet úgy `AcquireTokenSilent` , hogy újrapróbálkozik, ha a hálózat helyreáll, miután átmenetileg nem érhető el.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API meghívása az imént beszerzett token használatával

Adja hozzá a következő új metódust `MainWindow.xaml.cs`a alkalmazáshoz. A metódus a Graph APIre `GET` irányuló kérések engedélyezésére szolgál az engedélyezési fejléc használatával:

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
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Ebben a példában a `GetHttpContentWithToken` METÓDUS használatával http `GET` -kérést hajthat végre egy olyan védett erőforráson, amely egy jogkivonatot igényel, majd visszaküldi a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a HTTP-engedélyezési fejlécben. Ebben a példában az erőforrás a Microsoft Graph API *Me* -végpontja, amely megjeleníti a felhasználó profiljának adatait.

## <a name="add-a-method-to-sign-out-a-user"></a>Metódus hozzáadása egy felhasználó kijelentkezéséhez

Egy felhasználó kijelentkezéséhez adja hozzá a következő metódust a `MainWindow.xaml.cs` fájlhoz:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>További információ a felhasználói kijelentkezésről

A `SignOutButton_Click` metódus eltávolítja a felhasználókat a MSAL felhasználói gyorsítótárból, ami gyakorlatilag azt jelzi, hogy a MSAL elfelejtette az aktuális felhasználót, hogy a token beszerzésére irányuló jövőbeli kérések csak akkor legyenek sikeresek, ha interaktívak lesznek.

Bár az ebben a példában szereplő alkalmazás egyetlen felhasználót támogat, a MSAL olyan forgatókönyveket támogat, amelyekben egyszerre több fiók is regisztrálható. Ilyen például egy e-mail-alkalmazás, amelyben a felhasználók több fiókkal rendelkeznek.

## <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

A jogkivonattal kapcsolatos alapvető információk megjelenítéséhez adja hozzá a következő metódust a *MainWindow.XAML.cs* -fájlhoz:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>További információ

A Microsoft Graph API meghívásához használt hozzáférési tokenen kívül a felhasználó bejelentkezése után a MSAL szintén azonosító jogkivonatot kap. Ez a jogkivonat a felhasználókra vonatkozó információk kis részhalmazát tartalmazza. A `DisplayBasicTokenInfo` metódus megjeleníti a jogkivonatban található alapvető információkat. Például megjeleníti a felhasználó megjelenített nevét és AZONOSÍTÓját, valamint a jogkivonat lejárati dátumát és a hozzáférési jogkivonatot jelölő karakterláncot. Többször is kiválaszthatja a *Call Microsoft Graph API* -gombot, és láthatja, hogy ugyanazt a tokent használták újra a későbbi kérelmekhez. Azt is láthatja, hogy a lejárati dátum kiterjeszthető, amikor a MSAL úgy dönt, hogy ideje megújítani a jogkivonatot.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
