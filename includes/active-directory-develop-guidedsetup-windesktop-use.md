
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft hitelesítési könyvtár (MSAL) segítségével szolgáltatáshitelesítést egy token a Microsoft Graph API-hoz.

Ez a szakasz bemutatja, hogyan MSAL szolgáltatáshitelesítést egy token a Microsoft Graph API segítségével.

1.  A `MainWindow.xaml.cs`, MSAL könyvtár mutató hivatkozás hozzáadása az osztályhoz:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Cserélje le <code>MainWindow</code> osztály kódot:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>További információ
#### <a name="getting-a-user-token-interactive"></a>Egy felhasználói jogkivonat interaktív beolvasása
Hívja a `AcquireTokenAsync` módszer eredményezi egy ablak, jelentkezzen be a felhasználótól. Az alkalmazásoknak általában egy felhasználó a védett erőforrások eléréséhez szükséges először interaktív bejelentkezéshez, vagy ha egy figyelő művelet szerezzen be egy tokent sikertelen (pl. a jelszó lejárt).

#### <a name="getting-a-user-token-silently"></a>A felhasználói token első beavatkozás nélkül
`AcquireTokenSilentAsync`kezeli a token kérése és -megújítás felhasználói beavatkozás nélkül. Után `AcquireTokenAsync` végrehajtása az első alkalommal `AcquireTokenSilentAsync` a szokásos módszer segítségével kéri le a jogkivonatok későbbi hívások - védett erőforrások eléréséhez használt, mivel kérelmezése vagy megújítása jogkivonatok hívásainak beavatkozás nélkül történik.
Végül `AcquireTokenSilentAsync` sikertelen lesz, – például a felhasználó rendelkezik-e kijelentkezteti, vagy egy másik eszközön a jelszó megváltozott. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MsalUiRequiredException`. Az alkalmazás kezeli ezt a kivételt, két módon:

1.  Hívás elleni `AcquireTokenAsync` azonnal, ennek eredményeképpen a felhasználótól jelentkezik be. Ebben a mintában általában az online alkalmazások használják, ha nincs kapcsolat nélküli tartalom az alkalmazásban a felhasználó számára elérhető. Az interaktív telepítő által létrehozott mintánkban Ez a minta: tekintheti meg a minta végrehajtása az első művelet időben: felhasználó nem használta az alkalmazást, mert `PublicClientApp.Users.FirstOrDefault()` fogja tartalmazni null értékű, és egy `MsalUiRequiredException` kivételt fog jelezni. A kód a minta majd kezeli a kivételt meghívásával `AcquireTokenAsync` jelentkezik be a felhasználó megkérdezése eredményez.

2.  Alkalmazások is végezhet egy visual arra utal, hogy a felhasználót, hogy egy interaktív bejelentkezés szükség, hogy a felhasználó kiválaszthatja a megfelelő időben való bejelentkezéshez, vagy az alkalmazás újra `AcquireTokenSilentAsync` egy későbbi időpontban. Ez általában akkor használatos, amikor a felhasználó éppen megszakad nélkül tudja használni az alkalmazás egyéb funkciók – például nincs offline tartalma elérhető az alkalmazásban. Ebben az esetben a felhasználó eldöntheti, ha szeretne jelentkezzen be a védett erőforrások eléréséhez, vagy frissítse a elavult adatait, vagy eldöntheti, hogy az alkalmazást, majd ismételje meg `AcquireTokenSilentAsync` amikor hálózati átmenetileg nem érhető el állapota után állítják vissza.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API használatával csak megszerzett jogkivonattal hívható

1. Adja hozzá az alábbi új metódust a `MainWindow.xaml.cs`. A metódus végrehajtásához használatos a `GET` Graph API-val egy engedélyezés fejlécet kérelmet:

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
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a ellen védett API REST hívás

Ebben a minta az alkalmazásban a `GetHttpContentWithToken` módszer használható HTTP `GET` kérelem ellen védett erőforrás jogkivonat szükséges, és térjen vissza a tartalom a hívó. Ez a módszer hozzáadja a megszerzett lexikális elem szerepel a *HTTP Authorization fejlécet*. Ez a minta az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>A felhasználó kijelentkezik egy olyan metódus hozzáadása

1. Adja hozzá a következő metódust a `MainWindow.xaml.cs` a felhasználó kijelentkezik:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
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
### <a name="more-info-on-sign-out"></a>További információ a kijelentkezési

`SignOutButton_Click`eltávolítja a felhasználót a felhasználói gyorsítótárat MSAL – ez gyakorlatilag megmondja MSAL az aktuális felhasználó törlésére, így a későbbi kérelmek egy jogkivonat csak akkor sikeres, ha kell interaktív teszi.
Bár ez a példa az alkalmazás támogatja az egy-egy felhasználóhoz, MSAL szituációkat Ha egyszerre több fiók lehet bejelentkezve – Ha a felhasználó rendelkezik-e a több fiók például e-mail alkalmazást.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Alapvető lexikális elem adatainak megjelenítése

1. Adja hozzá a következő metódust az a `MainWindow.xaml.cs` a token kapcsolatos alapvető információk megjelenítése:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>További információ

Jogkivonatok keresztül szerzett *OpenID Connect* egy szűk köre a felhasználók számára vonatkozó információkat is tartalmazhat. `DisplayBasicTokenInfo`a token alapvető információkat jeleníti meg: például a felhasználó megjelenített neve és azonosítója, valamint a jogkivonat lejárati dátum és a karakterlánc, amely a hozzáférési token magát. Ez az információ, ezért nehezen olvasható jelenik meg. Is elérte a *Microsoft Graph API hívása* többször gombra, és tekintse meg, hogy ugyanezt a tokent további kérelmeknél lett-e használni. Megtekintheti a lejárati dátum, amikor MSAL úgy dönt, hogy kiterjesztendő az idő a token megújításához.
<!--end-collapse-->

