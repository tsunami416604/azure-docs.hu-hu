
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft Graph API a szolgáltatáshitelesítést egy token MSAL segítségével

Ebben a szakaszban a MSAL jogkivonat lekérése a Microsoft Graph API használhatja.

1.  Az a *MainWindow.xaml.cs* fájlt, a hivatkozás hozzáadása a osztály MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Cserélje le a `MainWindow` osztályban az alábbi kódot:

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
#### <a name="get-a-user-token-interactively"></a>A felhasználó interaktív token beszerzése
Hívja a `AcquireTokenAsync` módszer eredményezi egy ablak, amely felszólítja a felhasználók jelentkezhetnek be. Alkalmazások általában a felhasználóknak az első alkalommal védett erőforrás eléréséhez szükséges interaktív bejelentkezéshez. Előfordulhat, hogy is szükségük jelentkezzen be, amikor egy csendes jogkivonat megszerzése sikertelen (például, ha a jelszó lejárt).

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése
A `AcquireTokenSilentAsync` metódus kezeli a token kérése és megújításokat felhasználói beavatkozás nélkül. Miután `AcquireTokenAsync` végrehajtása az első alkalommal `AcquireTokenSilentAsync` lekérdezni a jogkivonatokat, amelyek további hívások, a védett erőforrások eléréséhez, vagy megújítása jogkivonatok hívások csendes válnak, mert a szokásos módszer.

Végül a `AcquireTokenSilentAsync` metódus sikertelen lesz. A hiba oka lehet, hogy a felhasználó rendelkezik-e vagy kijelentkezteti, vagy módosítani a jelszavát egy másik eszközön. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MsalUiRequiredException` kivétel. Az alkalmazás kezeli ezt a kivételt, két módon:

* Az elleni hívás kezdeményezése `AcquireTokenAsync` azonnal. A hívás eredménye jelentkezzen be a felhasználótól. Ebben a mintában általában az online alkalmazások használják, amennyiben a felhasználó nem érhető el kapcsolat nélküli tartalmat. A minta az interaktív telepítő által létrehozott követi, ebben a mintában a művelet az első alkalommal a minta végrehajtása meg. 
    * Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` null értéket tartalmaz, és egy `MsalUiRequiredException` kivétel történt. 
    * A kód a minta majd kezeli a kivételt meghívásával `AcquireTokenAsync`, ennek eredményeképpen a felhasználótól a bejelentkezéshez.

* Azt is inkább jelenthet vizuális jelzést felhasználók számára, amelyek egy interaktív bejelentkezés szükség, hogy a megfelelő időben való bejelentkezéshez választhatja. Vagy az alkalmazás újra `AcquireTokenSilentAsync` később. Ezt a mintát gyakran használt, amikor a felhasználók használhatják más alkalmazás működésének szüneteltetése--nélkül például, ha helyben tárolt tartalom érhető el az alkalmazást. Ebben az esetben felhasználók dönthet arról szeretne bejelentkezni a védett erőforrások eléréséhez, vagy az elavult adatok frissítése. Másik lehetőségként az alkalmazás dönt, hogy újra `AcquireTokenSilentAsync` amikor állítják vissza. a hálózati elvégzése után átmenetileg nem érhető el.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API hívása csak megszerzett jogkivonattal használatával

Adja hozzá a következő új metódust a `MainWindow.xaml.cs`. A metódus végrehajtásához használatos a `GET` Graph API kérelmet az engedélyezés fejléc használatával:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információt a REST-hívást ellen védett API

A mintaalkalmazás, használhatja a `GetHttpContentWithToken` módszer HTTP `GET` kérelem ellen védett erőforrás jogkivonat szükséges, és térjen vissza a tartalom a hívó. Ez a módszer a megszerzett token hozzáadja a HTTP-hitelesítési fejlécéhez. Ez a minta az erőforrás a Microsoft Graph API *me* végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>A felhasználó kijelentkezik egy olyan metódus hozzáadása

A felhasználó kijelentkezik, adja hozzá az alábbi metódust a `MainWindow.xaml.cs` fájlt:

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
### <a name="more-information-about-user-sign-out"></a>További információ a felhasználó kijelentkezési

A `SignOutButton_Click` metódus eltávolítja azokat a felhasználókat, a felhasználó az aktuális felhasználó törlésére, így a későbbi kérelmek egy jogkivonat akkor sikeres, csak akkor, ha történik, hogy interaktív MSAL gyakorlatilag megmondja MSAL gyorsítótárból.

Bár ez a példa az alkalmazás egyetlen felhasználót támogat, MSAL támogatja a forgatókönyvekben, ahol több fiók is bejelentkezhet a egyszerre. Példa: Ha a felhasználó rendelkezik-e a több fiók e-mail alkalmazást.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Alapvető lexikális elem adatainak megjelenítése

A token alapvető információ megjelenítéséhez adja hozzá az alábbi metódust a *MainWindow.xaml.cs* fájlt:

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

Mellett a hozzáférési jogkivonat, amellyel a Microsoft Graph API hívása után a felhasználó bejelentkezik MSAL is beolvassa az ID-tokent. Ez a token egy szűk köre a felhasználók számára vonatkozó információkat tartalmaz. A `DisplayBasicTokenInfo` metódus a jogkivonat az alapvető információkat jeleníti meg. Például azt jeleníti meg a felhasználó megjelenített neve és azonosítója, valamint a jogkivonat lejárati dátum és a karakterlánc, amely a hozzáférési jogkivonat magát. Kiválaszthatja a *Microsoft Graph API hívása* többször gombra, és tekintse meg, hogy ugyanezt a tokent további kérelmeknél lett-e használni. Megtekintheti a lejárati dátum, amikor MSAL úgy dönt, hogy kiterjesztendő az idő a token megújításához.
<!--end-collapse-->

