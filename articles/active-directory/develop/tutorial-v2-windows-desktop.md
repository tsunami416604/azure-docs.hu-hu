---
title: Ismerkedés a Microsoft identity platformmal Windows asztal
description: Hogyan kaphat egy Windows Asztali .NET (XAML) alkalmazás egy hozzáférési jogkivonatot, és hogyan hívhatja meg a Microsoft identitásplatform által védett API-t.
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
ms.openlocfilehash: ba4afa31a1ed7b6e2ddf43787ca32a06e97455ce
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533768"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>A Microsoft Graph API hívása Windows Asztali alkalmazásból

Ez az útmutató bemutatja, hogy egy natív Windows Desktop .NET (XAML) alkalmazás hogyan használja a Microsoft Graph API-t egy hozzáférési jogkivonat ot. Az alkalmazás más API-kat is hozzáférhet, amelyek hozzáférési jogkivonatokat igényelnek egy Microsoft identity platformról a fejlesztők 2.0-s végpontja számára. Ez a platform korábban Azure AD volt.

Miután befejezte az útmutatót, az alkalmazás képes lesz meghívni egy védett API-t, amely személyes fiókokat (beleértve a outlook.com, live.com és másokat) használ. Az alkalmazás az Azure Active Directoryt használó bármely vállalat vagy szervezet munkahelyi és iskolai fiókjait is használja.

> [!NOTE]
> Az útmutatóhoz a Visual Studio 2015 3-as, Visual Studio 2017-es vagy Visual Studio 2019-es frissítése szükséges. Nincs ilyen verziója? [Töltse le ingyen a Visual Studio 2019-et.](https://www.visualstudio.com/downloads/)

>[!NOTE]
> Ha most ismeri a Microsoft identitásplatformot, javasoljuk, hogy kezdje a Token beszerzése és a [Microsoft Graph API hívása windowsos asztali alkalmazásból](quickstart-v2-windows-desktop.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Az útmutatóval létrehozott mintaalkalmazás lehetővé teszi, hogy egy Windows Asztali alkalmazás lekérdezi a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a Microsoft identity-platform végponttokenjeit. Ebben a forgatókönyvben egy jogkivonatot ad hozzá a HTTP-kérelmekhez az engedélyezési fejlécen keresztül. A Microsoft Authentication Library (MSAL) kezeli a tokenek beszerzését és megújítását.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Jogkivonat-beszerzés kezelése a védett webes API-k eléréséhez

A felhasználó hitelesítése után a mintaalkalmazás kap egy jogkivonatot, amely lekérdezheti a Microsoft Graph API-t, vagy egy webes API-t, amelyet a Microsoft identity platform a fejlesztők számára biztosít.

Api-k, például a Microsoft Graph szükség van egy jogkivonatot, hogy hozzáférést biztosítson az adott erőforrásokhoz. Például egy jogkivonat szükséges a felhasználó profiljának olvasásához, a felhasználó naptárának eléréséhez vagy e-mail küldéséhez. Az alkalmazás kérhet egy hozzáférési jogkivonatot az MSAL használatával az erőforrások eléréséhez API-hatókörök megadásával. Ez a hozzáférési jogkivonat ezután hozzáadódik a HTTP-engedélyezési fejléchez a védett erőforrás ellen irányuló minden híváshoz.

Az MSAL kezeli a gyorsítótárazást és a hozzáférési jogkivonatok frissítését, így az alkalmazásnak nem kell.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet csomagokat használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Identity.Ügyfél](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft hitelesítési könyvtár (MSAL.NET)|

## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban egy új projektet hoz létre, amely bemutatja, hogyan integrálható egy Windows Asztali .NET alkalmazás (XAML) *a Bejelentkezés a Microsoft,* hogy az alkalmazás lekérdezheti webes API-k, amelyek megkövetelik a jogkivonatot.

Az alkalmazás, amely et hoz létre ezzel az útmutatóval megjeleníti a gombot, amely a diagram hívására szolgál, egy terület az eredmények megjelenítésére a képernyőn, és egy kijelentkezési gombot.

> [!NOTE]
> Inkább a minta Visual Studio-projektjét szeretné letölteni? [Töltsön le egy projektet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip), és ugorjon a [Konfiguráció lépésre](#register-your-application) a kódminta konfigurálásához a végrehajtás előtt.
>

Az alkalmazás létrehozásához tegye a következőket:

1. A Visual Studióban válassza az**Új** > **projekt** **fájlja** > lehetőséget.
2. A **Sablonok csoportban**válassza a **Visual C# lehetőséget.**
3. Válassza a **WPF App (.NET Framework)** lehetőséget a Visual Studio által használt verziótól függően.

## <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez

1. A Visual Studio alkalmazásban válassza az **Eszközök** > **NuGet csomagkezelő csomagkezelő**> **konzolját**.
2. A Csomagkezelő konzol ablakában illessze be a következő Azure PowerShell parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Ez a parancs telepíti a Microsoft Authentication Library programot. Az MSAL kezeli az Azure Active Directory 2.0-s virtuális gépével védett API-k eléréséhez használt felhasználói jogkivonatok beszerzését, gyorsítótárazását és frissítését
    >

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Jelentkezését kétféleképpen regisztrálhatja.

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód

A jelentkezést gyorsan regisztrálhatja az alábbi módon:
1. Nyissa meg az [Azure Portal – Alkalmazásregisztráció](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális mód

Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
1. Válassza **az Új regisztráció lehetőséget.**
   - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `Win-App-calling-MsGraph`).
   - A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
   - Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás oldalainak listájában válassza a **Hitelesítés** elemet.
   1. Az **Átirányítás URI-k** szakaszát, az Átirányítás URI-k listájában:
   1. A **TÍPUS oszlopban** válassza a **Nyilvános ügyfél/natív (mobil & asztali) lehetőséget.**
   1. Az **ÁTIRÁNYÍTÁS URI** oszlopban írja be a`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Nyissa meg a Visual *App.xaml.cs* Studiót, `Enter_the_Application_Id_here` nyissa meg a App.xaml.cs fájlt, majd cserélje le az alábbi kódrészletet az imént regisztrált és másolt alkalmazásazonosítóra.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>A kód hozzáadása az MSAL inicializálásához

Ebben a lépésben hozzon létre egy osztályt az MSAL-lal való interakció kezelésére, például a jogkivonatok kezelésére.

1. Nyissa meg a *App.xaml.cs* fájlt, majd adja hozzá az MSAL hivatkozását az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Frissítse az alkalmazásosztályt a következőkre:

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

Ez a szakasz azt mutatja be, hogy egy alkalmazás hogyan kérdezhet le egy védett háttérkiszolgálót, például a Microsoft Graphot.

A *MainWindow.xaml* fájl automatikusan létrejön a projektsablon részeként. Nyissa meg ezt a fájlt, majd cserélje le az alkalmazás * \<Grid>* csomópontját a következő kódra:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Token bekéselése a Microsoft Graph API-hoz az MSAL segítségével

Ebben a szakaszban az MSAL segítségével lekérésre egy jogkivonatot a Microsoft Graph API-t.

1. A *MainWindow.xaml.cs* fájlban adja hozzá az MSAL hivatkozását az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Cserélje `MainWindow` le az osztálykódot a következőkre:

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

<!--start-collapse-->
### <a name="more-information"></a>További információ

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A `AcquireTokenInteractive` metódus hívása egy ablakot eredményez, amely a felhasználók bejelentkezését kéri. Az alkalmazások általában megkövetelik a felhasználóktól, hogy interaktívan jelentkezzenek be, amikor először kell hozzáférniük egy védett erőforráshoz. Előfordulhat, hogy akkor is be kell jelentkezniük, ha egy jogkivonat beszerzésére irányuló csendes művelet sikertelen (például ha egy felhasználó jelszava lejárt).

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` módszer kezeli a tokenek beszerzéseés megújítások felhasználói beavatkozás nélkül. Miután `AcquireTokenInteractive` végre az első `AcquireTokenSilent` alkalommal, a szokásos módszer a jogkivonatok beszerzése, amelyek hozzáférést biztosítanak a védett erőforrások a későbbi hívások, mert a kérelmek kérése vagy megújítása jogkivonatok csendes en történik.

Végül, `AcquireTokenSilent` a módszer akarat megbukik. A hiba oka lehet, hogy a felhasználó kijelentkezett, vagy megváltoztatta a jelszavát egy másik eszközön. Ha az MSAL azt észleli, hogy a probléma megoldható egy interaktív művelet megkövetelésével, kivételt indít `MsalUiRequiredException` el. Az alkalmazás kétféleképpen tudja kezelni ezt a kivételt:

* `AcquireTokenInteractive` Azonnal ellenhívhat. Ez a hívás a felhasználó bejelentkezésének megkérése. Ezt a mintát általában olyan online alkalmazásokban használják, ahol a felhasználó számára nincs elérhető offline tartalom. Az irányított beállítás által létrehozott minta ezt a mintát követi, amely a minta első végrehajtásakor működés közben látható.

* Mivel egyetlen felhasználó sem `PublicClientApp.Users.FirstOrDefault()` használta az alkalmazást, `MsalUiRequiredException` null értéket tartalmaz, és kivétel tetszikel.

* A mintában lévő kód ezután `AcquireTokenInteractive`kezeli a kivételt a hívással, ami a felhasználó bejelentkezésének megírását eredményezi.

* Ehelyett vizuális jelzést adhat a felhasználóknak arról, hogy interaktív bejelentkezésre van szükség, hogy kiválaszthassák a megfelelő időpontot a bejelentkezéshez. Vagy az alkalmazás `AcquireTokenSilent` később újrapróbálkozhat. Ezt a mintát gyakran használják, ha a felhasználók más alkalmazásfunkciókat is használhatnak megszakítás nélkül – például ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók eldönthetik, hogy mikor szeretnének bejelentkezni a védett erőforrás eléréséhez vagy az elavult adatok frissítéséhez. Másik lehetőségként az alkalmazás dönthet `AcquireTokenSilent` úgy, hogy újra próbálkozik, amikor a hálózat visszaáll, miután átmenetileg nem érhető el.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Hívja meg a Microsoft Graph API-t az imént beszerzett token használatával

Adja hozzá a következő `MainWindow.xaml.cs`új módszert a hoz. A módszer segítségével kérelmet `GET` a Graph API-val egy authorize fejléc használatával:

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információ a REST-hívásról egy védett API-val szemben

Ebben a mintaalkalmazásban `GetHttpContentWithToken` a módszer segítségével `GET` http-kérelmet egy védett erőforrás, amely megköveteli a jogkivonatot, és majd visszaadja a tartalmat a hívó. Ez a módszer hozzáadja a beszerzett jogkivonatot a HTTP-engedélyezési fejlécben. Ebben a mintában az erőforrás a Microsoft Graph API *me* végpont, amely megjeleníti a felhasználó profiladatait.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Felhasználó kijelentkezési módjának hozzáadása

Felhasználó kijelentkeztetéséhez adja hozzá `MainWindow.xaml.cs` a következő módszert a fájlhoz:

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>További információ a felhasználói kijelentkezésről

A `SignOutButton_Click` módszer eltávolítja a felhasználókat az MSAL felhasználói gyorsítótárából, ami hatékonyan utasítja az MSAL-t, hogy felejtse el az aktuális felhasználót, így a token megszerzésére irányuló jövőbeli kérés csak akkor lesz sikeres, ha interaktívvá válik.

Bár az alkalmazás ebben a mintában támogatja az egyes felhasználók, MSAL támogatja a forgatókönyveket, ahol több fiók is bejelentkezett egy időben. Ilyen például egy e-mail alkalmazás, ahol a felhasználó több fiókkal rendelkezik.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Alapvető tokenadatok megjelenítése

A jogkivonatalapvető információinak megjelenítéséhez adja hozzá a következő módszert a *MainWindow.xaml.cs* fájlhoz:

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

<!--start-collapse-->
### <a name="more-information"></a>További információ

A Microsoft Graph API hívásához használt hozzáférési jogkivonaton kívül, miután a felhasználó bejelentkezik, az MSAL egy azonosító jogkivonatot is beszerez. Ez a jogkivonat a felhasználók számára releváns információk egy kis részét tartalmazza. A `DisplayBasicTokenInfo` metódus megjeleníti a jogkivonatban található alapvető információkat. Például megjeleníti a felhasználó megjelenítendő nevét és azonosítóját, valamint a jogkivonat lejárati dátumát és a hozzáférési jogkivonatot képviselő karakterláncot. A Microsoft *Graph API hívása* gombot többször is kiválaszthatja, és láthatja, hogy ugyanazt a jogkivonatot újra felhasználták-e a későbbi kérelmekhez. Azt is láthatja, hogy a lejárati dátum meghosszabbodik, amikor az MSAL úgy dönt, hogy itt az ideje a jogkivonat megújításának.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
