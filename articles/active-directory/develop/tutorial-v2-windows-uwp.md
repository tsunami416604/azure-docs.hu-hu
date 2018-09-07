---
title: Ismerkedés az Azure AD v2 UWP |} A Microsoft Docs
description: Hogyan univerzális Windows-Platformos alkalmazások (UWP) meghívhat egy API-t, amely szerint az Azure Active Directory v2 végpontot hozzáférési jogkivonatok igényel
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: cd6cf2e94b032408fd6c3b298294d84837e102a6
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024748"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>A Microsoft Graph API meghívása (XAML) az univerzális Windows Platform-alkalmazásból

Ez az útmutató azt ismerteti, hogyan univerzális Windows Platform (UWP) natív alkalmazások hozzáférési jogkivonat kérése és majd a Microsoft Graph API meghívása. Az útmutató más API-k az Azure Active Directory v2 végpontot a hozzáférési jogkivonatok igénylő is vonatkozik.

Ez az útmutató végén az alkalmazás meghívja a védett API személyes fiókok használatával. Példák:, Outlook.com-os, live.com és mások. Az alkalmazás is meghívja a munkahelyi és iskolai fiókok, bármely vállalat vagy szervezet, amely rendelkezik az Azure Active Directoryban.

>[!NOTE]
> Ez az útmutató a Visual Studio 2017 az univerzális Windows Platform fejlesztési telepítve van szükség. Lásd: [beállításához](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) az utasításokat követve töltse le és az univerzális Windows-Platformos alkalmazások fejlesztése a Visual Studio konfigurálása.

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Hogyan működik ez az útmutató a graph](./media/tutorial-v2-windows-uwp/uwp-intro.png)

Ez az útmutató létrehoz egy minta UWP-alkalmazás, amely a Microsoft Graph API vagy egy webes API-t, amely az Azure Active Directory v2 végpontot származó jogkivonatokat fogad el. Ebben a forgatókönyvben egy token hozzá az engedélyezési fejléc via HTTP-kérelmekre. A Microsoft-hitelesítési tár (MSAL) token beszerzését és a megújítások kezeli.

### <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|A Microsoft-hitelesítési tár|


## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban egy Windows asztali .NET-alkalmazás (XAML) integrálása részletesen bemutatja a *Bejelentkezés Microsoft-*. Ezután lekérdezheti a webes API-k, amelyek egy jogkivonatot, például a Microsoft Graph API-t igényelnek.

Ez az útmutató létrehoz egy alkalmazás, amely egy gomb megjeleníti a lekérdezéseket a Graph API, a Kijelentkezés gombbal és a szövegmezők, amely a hívások eredményeinek megjelenítéséhez.

>[!NOTE]
> Szeretné ezt a mintát a Visual Studio-projekt letöltése helyett? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) , és ugorjon a [alkalmazásregisztráció](#register-your-application "alkalmazás lépésben") lépéssel konfigurálhatja a kódmintát, futtatásuk előtt.


### <a name="create-your-application"></a>Az alkalmazás létrehozása
1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**.
2. A **sablonok**válassza **Visual C#**.
3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet.
4. Nevezze el az alkalmazást, és válassza ki **OK**.
5. Ha a rendszer kéri, válassza ki bármelyik verziója **cél** és **minimális** verziókat, és válassza ki **OK**.

    >![Minimális és a cél-verziók](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft-hitelesítési tár hozzáadása a projekthez
1. A Visual Studióban válassza ki a **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console**.
2. Másolja és illessze be a következő parancsot a **Package Manager Console** ablakban:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

> [!NOTE]
> Ez a parancs telepíti [Microsoft-hitelesítési tár](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). Az MSAL beszerzi, gyorsítótárazza, és frissíti a felhasználói jogkivonatokhoz, amelyek védi az Azure Active Directory v2 API-k elérésére.

> [!NOTE]
> Ebben az oktatóanyagban viszont nem használható, de az MSAL.NET, legújabb verzióját, de azt frissítése folyamatban van.

## <a name="initialize-msal"></a>Az MSAL inicializálása
Ebben a lépésben segítségével hozhat létre egy osztályt, amely kezeli a interakció MSAL, például a jogkivonatok kezelése.

1. Nyissa meg a **App.xaml.cs** fájlt, és adja hozzá a hivatkozást az MSAL az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Adja hozzá az alábbi két sort az alkalmazás osztályhoz (belül <code>sealed partial class App : Application</code> letiltása):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

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
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
    }
    ```

### <a name="more-information"></a>További információ
#### <a name="get-a-user-token-interactively"></a>A felhasználó interaktív token beszerzése
Hívása a `AcquireTokenAsync` módszer eredményezi egy ablak, amely felkéri a felhasználót, hogy jelentkezzen be. Alkalmazások általában felhasználóktól interaktívan jelentkezik be először egy védett erőforrás eléréséhez szükséges. Akkor is előfordulhat, hogy kell bejelentkezni, amikor egy token beszerzéséhez a beavatkozás nélküli művelet sikertelen. Ez például akkor, amikor a felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése
A `AcquireTokenSilentAsync` metódus kezeli a token beszerzését és a megújítások felhasználói beavatkozás nélkül. Után `AcquireTokenAsync` hajtja végre az első alkalommal kéri a felhasználót, a hitelesítő adatokat, és a `AcquireTokenSilentAsync` metódus használandó jogkivonatokat későbbi hívások kérelmet, mert azt szerzi be a jogkivonatokat beavatkozás nélkül. Az MSAL tokengyorsítótárral és -megújítás fogja kezelni.

Végül a `AcquireTokenSilentAsync` metódus sikertelen. A hiba oka lehet, hogy felhasználók rendelkezik-e vagy kijelentkeztetése, vagy módosítani a jelszavát egy másik eszközön. Ha az MSAL észleli, hogy a probléma megoldhatók egy interaktív intézkedést kér, akkor aktiválódik egy `MsalUiRequiredException` kivétel. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

* Azt is ellenőrizze, egy hívást kell végrehajtanunk `AcquireTokenAsync` azonnal. Ez a hívás eredménye kéri a felhasználót, hogy jelentkezzen be. Általában ez a minta az online alkalmazások használják, ha a felhasználó nem érhető el kapcsolat nélküli tartalom. A minta az interaktív telepítés által létrehozott mintát követi. A minta futtatása művelet az első alkalommal megjelenik. 
    * Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null, és a egy `MsalUiRequiredException` kivétel történt.
    * A kód a minta ezután kezeli a kivételt meghívásával `AcquireTokenAsync`. Ez a hívás eredménye kéri a felhasználót, hogy jelentkezzen be.

* Vagy ehelyett megadja egy vizuális jelzés a felhasználók számára, hogy egy interaktív bejelentkezési megadása kötelező. Ezután és kiválaszthatja a megfelelő időben való bejelentkezéshez. Vagy az alkalmazás megpróbálhatja `AcquireTokenSilentAsync` később. Ezt a mintát gyakran, használatos, amikor a felhasználók használhatják a más megszakítása nélkül az alkalmazás funkciói. Ilyen például, ha offline tartalom érhető el, az alkalmazás. Ebben az esetben felhasználók megadhatja, hogy szeretne hozzáférni a védett erőforrásokhoz, vagy frissítse az elavult adatokat. Ellenkező esetben az alkalmazás dönt, hogy próbálkozzon újra, vagy `AcquireTokenSilentAsync` amikor a hálózat visszaállítása után, átmenetileg nem érhető el.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API meghívása éppen megszerzett jogkivonattal használatával

* Adja hozzá a következő új metódust **MainPage.xaml.cs**. Győződjön meg arról, hogy ezt a módszert használják egy `GET` kérelmet Graph API-[engedélyezés] fejléc használatával:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API REST-hívást gondoskodik

A mintaalkalmazásban a `GetHttpContentWithToken` , hogy a HTTP módszert `GET` jogkivonatot igénylő védett erőforrás kérelmet. Ezután a metódus a hívónak a tartalmat adja vissza. Ez a metódus hozzáadja a beszerzett lexikális elem szerepel a **HTTP engedélyezési** fejléc. Ebben a példában az erőforrás a Microsoft Graph API **me** végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adjon meg egy metódust a felhasználó kijelentkeztetése

* Jelentkezzen ki a felhasználót, adja hozzá a következő metódust **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>További információ a Kijelentkezés

A `SignOutButton_Click` módszer eltávolítja a felhasználót az MSAL felhasználók gyorsítótárból. Ez a módszer gyakorlatilag megmondja a MSAL az aktuális felhasználó törlésére. Ezután egy token beszerzéséhez jövőbeli kérést csak akkor, ha azt interaktív tett sikeres lesz.
Ebben a példában az alkalmazás támogatja az egy-egy felhasználóhoz. Azonban a MSAL forgatókönyvek, ahol egynél több fiókot is bejelentkezhet a egyszerre. Ilyen például az e-mail alkalmazást, amelyhez a felhasználónak van-e a különböző fiókok.

## <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

* Adja hozzá a következő metódust **MainPage.xaml.cs** a token alapvető információit jeleníti meg:

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

### <a name="more-information"></a>További információ

Azonosító-jogkivonatokat keresztül beszerzett **OpenID Connect** részhalmazát a felhasználó a profiljával kapcsolatos információkat is tartalmaznak. `DisplayBasicTokenInfo` a jogkivonatban található alapvető információkat jelenít meg. Példák: a felhasználó megjelenített neve és azonosítója, lejárati dátuma a jogkivonatot, és a karakterlánc, amely a hozzáférési jogkivonat maga jelöli. Ha a **Microsoft Graph API meghívása** többször gombot, látni fogja, hogy további kérésekhez fel lett-e újra ugyanezt a tokent. Megtekintheti a lejárati dátum, amikor MSAL úgy dönt, hogy a token megújításának ideje kiterjesztett is.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most szüksége az alkalmazás regisztrálása a Microsoft alkalmazásregisztrációs portálon:
1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app) kell regisztrálni egy alkalmazást.
2. Adjon meg egy nevet az alkalmazásnak.
3. Győződjön meg arról, hogy a kívánt beállítást **interaktív telepítés** van *nincs kiválasztva*.
4. Válassza ki **hozzáadása platformok**, jelölje be **natív alkalmazás**, majd válassza ki **mentése**.
5. Másolja a GUID **Alkalmazásazonosító**, lépjen vissza a Visual Studio, a nyílt **App.xaml.cs**, és cserélje le `your_client_id_here` az imént regisztrált alkalmazás azonosítójával:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Összevont tartományok (nem kötelező) az integrált hitelesítés engedélyezése

Integrált Windows-hitelesítés engedélyezéséhez az Azure Active Directory összevont tartományt használatakor az alkalmazásjegyzékben engedélyeznie kell a további funkciókat:

1. Kattintson duplán a **Package.appxmanifest**.
2. Válassza ki a **képességek** lapra, és győződjön meg arról, hogy engedélyezve vannak-e a következő beállításokat:

    - Vállalati hitelesítési
    - Magánhálózatok (ügyfél és kiszolgáló)
    - Megosztott felhasználói tanúsítványok

3. Nyissa meg **App.xaml.cs** , és adja hozzá a következő sort az alkalmazás a konstruktorban:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Integrált Windows-hitelesítés nincs konfigurálva ehhez a mintához alapértelmezés szerint. Az alkalmazásokat, amelyek a kérelem *vállalati hitelesítési* vagy *megosztott felhasználói tanúsítványok* lehetőségekhez szükség magasabb szintű ellenőrzés a Windows Store. Emellett nem minden fejlesztő kíván végrehajtani, a magasabb szintű ellenőrzést. Engedélyezi ezt a beállítást csak akkor, ha Azure Active Directory összevont tartományt a integrált Windows-hitelesítés szükséges.


## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás teszteléséhez válassza ki az F5 billentyűt a projekt futtatása a Visual Studióban. A fő ablakban jelenik meg:

![Alkalmazás felhasználói felületén](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Amikor elkészült, teszteléséhez, válassza ki a **Microsoft Graph API meghívása**. A Microsoft Azure Active Directory szervezeti fiókkal vagy egy Microsoft-fiók, például live.com vagy Outlook.com-os, majd szokott bejelentkezni. Ha most először, megjelenik egy ablak, amely kéri a felhasználót, hogy jelentkezzen be:

![Bejelentkezési oldal](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Hozzájárulás
Az első alkalommal jelentkezik be az alkalmazás megjelenik a jóváhagyást kérő képernyőt, az alábbihoz hasonló. Válassza ki **Igen** explicit módon jóváhagyást eléréséhez:

![Hozzáférés beleegyezést kérő oldalon](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Várt eredmény
Felhasználói profil adatait a Microsoft Graph API-hívás által visszaadott láthatja a **API-hívási eredmények** képernyőjén:

![API-hívási eredmények képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

A jogkivonat-n keresztül beszerzett alapvető adatait is megjelenik `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` a a **Tártoken adatainak** mezőbe:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |A felhasználó teljes neve|A felhasználó vezetékneve és nevét.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely azonosítja a felhasználót.|
|**Jogkivonat lejár** |DateTime |Az az időpont, amikor a jogkivonat érvényessége lejár. Az MSAL szükség szerint a token megújítása kiterjeszti a lejárati dátumot.|
|**Hozzáférési jogkivonat** |Sztring |A jogkivonat-karakterláncot, amelyet elküld a HTTP-kérelmek, amely esetében egy *engedélyeztetési fejléc*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Nézze meg, mi az a hozzáférési jogkivonat (nem kötelező)
Szükség esetén másolja az értéket **Access Token** , és illessze be a https://jwt.ms dekódolni, és a jogcímek listája.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált engedélyek

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben automatikusan hozzáadódik alapértelmezés szerint a minden alkalmazás, amely regisztrálva van az alkalmazásregisztrációs portálon. Más Microsoft Graph API-k, és az egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban listázása.

A felhasználók naptáraiban keretén belül egy alkalmazás eléréséhez, adja hozzá a *Calendars.Read* delegált engedéllyel az alkalmazás regisztrációs adatok. Majd adja hozzá a *Calendars.Read* a hatókört a `acquireTokenSilent` hívja. 

> [!NOTE]
> Előfordulhat, hogy rendszer kér a felhasználótól további címtárbérlőhöz hatókörök számának növelésével.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1 probléma
Jelenhet meg a következő hibaüzeneteket egyikét az alkalmazás Azure Active Directory összevont tartományt a bejelentkezéskor:
 - Nem található a kérelemben érvényes ügyféltanúsítványt.
 - Nem a felhasználó tanúsítványtárolójában található érvényes tanúsítvány.
 - Válasszon egy másik hitelesítési módszert.

**OK:** nincsenek engedélyezve a vállalati és a tanúsítvány képességeket.

**Megoldás:** kövesse a [integrált hitelesítést az összevont tartományok](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>2 probléma
Engedélyezi a [integrált hitelesítést az összevont tartományok](#enable-integrated-authentication-on-federated-domains-optional) , és próbálja meg a Windows Hello egy Windows 10 rendszerű számítógépre való bejelentkezéshez használt az adott környezetben, a konfigurált többtényezős hitelesítést. Tanúsítványok listája jelenik meg. Azonban ha a PIN-kód használatát választja, a PIN-kód ablak soha nem jelenik meg.

**OK:** probléma a webeshitelesítés-szervező UWP-alkalmazásokban, a Windows 10 asztali verzióját futtató egy ismert korlátozás. Windows 10 Mobile jól működik.

**Megkerülő megoldás:** kiválasztása **jelentkezzen be az egyéb lehetőségek**. Válassza ki **jelentkezzen be egy felhasználónevet és jelszót**. Válassza ki **adja meg a jelszót**. Folytassa a telefonos hitelesítési folyamat.
