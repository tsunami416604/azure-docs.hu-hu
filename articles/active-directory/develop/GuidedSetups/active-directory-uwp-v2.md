---
title: Ismerkedés az Azure AD-v2 UWP |} Microsoft Docs
description: Hogyan univerzális Windows Platform (XAML) alkalmazások Azure Active Directory-v2 végpontja hozzáférési jogkivonatok igénylő API meghívása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4db14bc250bf9d6740380f3c4376f43d6f315b01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>A Microsoft Graph API meghívása egy univerzális Windows Platform (UWP) alkalmazásból

Ez az útmutató ismerteti, hogyan egy natív univerzális Windows Platform (XAML) alkalmazás szereznie egy hozzáférési jogkivonatot és a hozzáférés toke használatával hívható meg Microsoft Graph API vagy egyéb Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő API-k.

Ez az útmutató végén az alkalmazás fogja tudni hívható meg egy védett API használatával személyes fiókok (például outlook.com, live.com és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directory.  

> Ez az útmutató az univerzális Windows Platform fejlesztési telepítve van szükség a Visual Studio 2017. Ennek ellenőrzéséhez [cikk](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "UWP Visual Studio beállítása") útmutatást töltenie és konfigurálnia a Visual Studio univerzális Windows Platform alkalmazások fejlesztéséhez.

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató működése](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Ez az útmutató által létrehozott mintaalkalmazás lehetővé teszi, hogy egy univerzális Windows Platform-alkalmazást a Microsoft Graph API vagy egy webes API, amely az Azure Active Directory v2 végpont jogkivonatokat fogad el. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Token beszerzése és -megújítás kezelése a Microsoft hitelesítési könyvtár (MSAL).

### <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft hitelesítési könyvtár (MSAL)|


## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen arról, hogyan hozzon létre egy új projektet bemutatják, hogyan integrálhatja a Windows asztali .NET-alkalmazás (XAML) rendelkező *jelentkezzen be Microsoft* azt lekérdezhesse jogkivonat igénylő webes API-k.

Ez az útmutató által létrehozott alkalmazás közzététele egy diagramot és az eredmények képernyőn és kijelentkezési gomb megjelenítése gombra.

> Ez a minta Visual Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) és ugorjon a [regisztrációja](#register-your-application "alkalmazás regisztrációs lépésében") lépéssel konfigurálhatja a kódminta végrehajtása előtt.


### <a name="create-your-application"></a>Az alkalmazás létrehozása
1. A Visual Studio: **fájl** > **új** > **projekt**<br/>
2. A *sablonok*, jelölje be **Visual C#**
3. Válassza ki **üres alkalmazás (univerzális Windows)**
4. Adjon neki egy nevet, és kattintson az "Ok" gombra.
5. Ha a rendszer kéri, válassza ki az összes verziót szabad csökkent *cél* és *minimális* verziót, és kattintson az "Ok" gombra:<br/><br/>![Minimális és a cél-verziók](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>A Microsoft hitelesítési könyvtár (MSAL) hozzáadása a projekthez
1. A Visual Studio: **eszközök** > **Nuget-Csomagkezelő** > **Csomagkezelő konzol**
2. Másolja és illessze be a Package Manager Console ablakban a következő parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> A csomag újabb telepíti a [Microsoft hitelesítési könyvtár (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL kezeli az beszerzése, gyorsítótárazás és védi az Azure Active Directory v2 API-k elérésére használt felhasználói jogkivonatokhoz frissítése.

## <a name="initialize-msal"></a>MSAL inicializálása
Ebben a lépésben hozzon létre egy osztályt, például jogkivonatokat kezelésének MSAL könyvtárban való együttműködéshez kezeléséhez nyújt segítséget.

1. Nyissa meg a **App.xaml.cs** fájlt, és a hivatkozás MSAL szalagtár osztályra:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Adja hozzá a következő két sort az alkalmazás osztály (belső <code>sealed partial class App : Application</code> blokk):

    ```csharp
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói Felületüket létrehozni

A **MainPage.xaml** fájl automatikusan létrejöjjön a projekt sablon részeként. Nyissa meg ezt a fájlt, és kövesse az utasításokat:

1.  Cserélje le az alkalmazás **<Grid>** csomópont:

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
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft hitelesítési könyvtár (MSAL) segítségével szolgáltatáshitelesítést egy token a Microsoft Graph API-hoz.

Ez a szakasz bemutatja, hogyan MSAL segítségével a Microsoft Graph API a szolgáltatáshitelesítést egy token.

1.  A **MainPage.xaml.cs**, MSAL könyvtár mutató hivatkozás hozzáadása az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Cserélje le a kódját a <code>MainPage</code> osztály a következőre:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        //Set the scope for API call to user.read
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
Hívja a `AcquireTokenAsync` módszer eredményezi egy ablak, amely felszólítja a felhasználók jelentkezhetnek be. Alkalmazások általában a felhasználóknak az első alkalommal védett erőforrás eléréséhez szükséges interaktív bejelentkezéshez. Előfordulhat, hogy is szükségük jelentkezzen be, amikor egy csendes jogkivonat megszerzése sikertelen (például, ha a jelszó lejárt).

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése
A `AcquireTokenSilentAsync` metódus kezeli a token kérése és megújításokat felhasználói beavatkozás nélkül. Miután `AcquireTokenAsync` végrehajtása az első alkalommal `AcquireTokenSilentAsync` lekérdezni a jogkivonatokat, amelyek további hívások, a védett erőforrások eléréséhez, vagy megújítása jogkivonatok hívások csendes válnak, mert a szokásos módszer.

Végül a `AcquireTokenSilentAsync` metódus sikertelen lesz. A hiba oka lehet, hogy a felhasználó rendelkezik-e vagy kijelentkezteti, vagy módosítani a jelszavát egy másik eszközön. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MsalUiRequiredException` kivétel. Az alkalmazás kezeli ezt a kivételt, két módon:

* Az elleni hívás kezdeményezése `AcquireTokenAsync` azonnal. A hívás eredménye jelentkezzen be a felhasználótól. Ebben a mintában általában az online alkalmazások használják, amennyiben a felhasználó nem érhető el kapcsolat nélküli tartalmat. A minta az interaktív telepítő által létrehozott követi, ebben a mintában a művelet az első alkalommal a minta végrehajtása meg. 
    * Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` null értéket tartalmaz, és egy `MsalUiRequiredException` kivétel történt. 
    * A kód a minta majd kezeli a kivételt meghívásával `AcquireTokenAsync`, ennek eredményeképpen a felhasználótól a bejelentkezéshez.

* Azt is inkább jelenthet vizuális jelzést felhasználók számára, amelyek egy interaktív bejelentkezés szükség, hogy a megfelelő időben való bejelentkezéshez választhatja. Vagy az alkalmazás újra `AcquireTokenSilentAsync` később. Ezt a mintát gyakran használt, amikor a felhasználók használhatják más alkalmazás működésének szüneteltetése--nélkül például, ha helyben tárolt tartalom érhető el az alkalmazást. Ebben az esetben felhasználók dönthet arról szeretne bejelentkezni a védett erőforrások eléréséhez, vagy az elavult adatok frissítése. Másik lehetőségként az alkalmazás dönt, hogy újra `AcquireTokenSilentAsync` amikor állítják vissza. a hálózati elvégzése után átmenetileg nem érhető el.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>A Microsoft Graph API használatával csak megszerzett jogkivonattal hívható

1. Adja hozzá a következő új metódust a **MainPage.xaml.cs**. A metódus végrehajtásához használatos a `GET` Graph API-val egy engedélyezés fejlécet kérelmet:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a ellen védett API REST hívás

Ebben a minta az alkalmazásban a `GetHttpContentWithToken` módszer használható HTTP `GET` kérelem ellen védett erőforrás jogkivonat szükséges, és térjen vissza a tartalom a hívó. Ez a módszer hozzáadja a megszerzett lexikális elem szerepel a *HTTP Authorization fejlécet*. Ez a minta az erőforrás a Microsoft Graph API *me* végpont – amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>A felhasználó kijelentkezik egy olyan metódus hozzáadása

1. A felhasználó kijelentkezik, adja hozzá a következő metódust **MainPage.xaml.cs**:

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

### <a name="more-info-on-sign-out"></a>További információ a kijelentkezési

A metódus `SignOutButton_Click` eltávolítja a felhasználót a felhasználói gyorsítótárat MSAL – ez gyakorlatilag megmondja MSAL az aktuális felhasználó törlésére, így a későbbi kérelmek egy jogkivonat csak is sikeres, ha kell interaktív teszi.
Bár ez a példa az alkalmazás támogatja az egy-egy felhasználóhoz, MSAL szituációkat Ha egyszerre több fiók lehet bejelentkezve – Ha a felhasználó rendelkezik-e a több fiók például e-mail alkalmazást.

## <a name="display-basic-token-information"></a>Alapvető lexikális elem adatainak megjelenítése

1. Adja hozzá a következő metódust a **MainPage.xaml.cs** a token kapcsolatos alapvető információk megjelenítése:

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

Azonosító-jogkivonatokat keresztül szerzett *OpenID Connect* egy szűk köre a felhasználók számára vonatkozó információkat is tartalmazhat. `DisplayBasicTokenInfo` a token alapvető információkat jeleníti meg: például a felhasználó megjelenített neve és azonosítója, valamint a jogkivonat lejárati dátum és a karakterlánc, amely a hozzáférési token magát. Ez az információ, ezért nehezen olvasható jelenik meg. Is elérte a **Microsoft Graph API hívása** többször gombra, és tekintse meg, hogy ugyanezt a tokent további kérelmeknél lett-e használni. Megtekintheti a lejárati dátum, amikor MSAL úgy dönt, hogy kiterjesztendő az idő a token megújításához.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adja meg az alkalmazás nevét 
3. Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4. Kattintson a **hozzáadása platformok**, majd jelölje be **natív alkalmazás** , majd kattintson a Mentés
5. Másolja át a GUID azonosítója, lépjen vissza a Visual Studio, nyissa meg **App.xaml.cs** , és cserélje le `your_client_id_here` az imént regisztrált alkalmazás azonosítójával:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Engedélyezze az integrált hitelesítést összevont tartományt (nem kötelező)

Ahhoz, hogy a Windows-hitelesítés egy összevont Azure Active Directory-tartomány használata esetén, az alkalmazás jegyzékében engedélyeznie kell a további lehetőségeket:

1. Kattintson duplán **Package.appxmanifest**
2. Válassza ki **képességek** lapra, és győződjön meg arról, hogy engedélyezve vannak-e a következő beállításokat:

    - Vállalati hitelesítés
    - Magánhálózatok (ügyfél és kiszolgáló)
    - Megosztott felhasználói tanúsítványok 

3. Ezután nyissa meg **App.xaml.cs**, és adja hozzá a következő alkalmazás konstruktorában:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Integrált Windows-hitelesítés nem értéke alapértelmezés szerint ez a minta, mert a kért alkalmazások a *vállalati hitelesítési* vagy *megosztott felhasználói tanúsítványok* lehetőségekhez szükség egy a Windows áruház verificationby magasabb szintű és nem minden fejlesztők végrehajtandó a magasabb szintű ellenőrzést. Engedélyezze ezt a beállítást csak akkor, ha egy összevont Azure Active Directory-tartomány a Windows-hitelesítés szükséges.


## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás teszteléséhez nyomja le az `F5` a projektet a Visual Studio futtatásához. A fő ablak kell megjelennie:

![Alkalmazás felhasználói felületén](media/active-directory-uwp-v2.md/testapp-ui.png)

Amikor készen áll a tesztelése, kattintson *Microsoft Graph API hívása* és a Microsoft Azure Active Directory (szervezeti fiók) vagy egy Microsoft Account (live.com, outlook.com) fiók használatával jelentkezzen be. Ha először, jelentkezzen be a felhasználó kérő ablak jelenik meg:

![Bejelentkezési oldal](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Hozzájárulás
Az első alkalommal bejelentkezik az alkalmazásról, lehetősége lesz az alábbihoz hasonló hozzájárulási képernyő kell explicit módon fogadja el:

![Hozzájárulás képernyő](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Felhasználói profil adatait a API-hívási eredmények képernyőn a Microsoft Graph API-hívás által visszaadott kell megjelennie:

![Eredmények képernyő](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Emellett meg kell jelennie a token keresztül szerzett alapvető információkat `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` a Token adatait mezőbe:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |Felhasználó teljes neve |A felhasználó nagyapja vezeték- és keresztneve.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely a felhasználó azonosítására szolgál.|
|**Jogkivonat lejár** |DateTime |Az az idő, amelynél a jogkivonat lejár. MSAL szükség szerint a token megújításával terjeszti ki a lejárati dátum.|
|**Hozzáférési jogkivonat** |Karakterlánc |A lexikális elem karakterlánca küldött HTTP-kérelmek igénylő egy *Authorization fejlécet*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Megtudhatja, mi az a hozzáférési jogkivonat (nem kötelező)
Szükség esetén az "Access Token" értéket másolja és illessze be https://jwt.ms dekódolni a, és tekintse meg a jogcímek listája.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

A Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Minden egyes, amely regisztrálva van az alkalmazásregisztrációs portálra az alkalmazás alapértelmezés szerint automatikusan megjelenik az ebben a hatókörben. Más Microsoft Graph API-k, valamint a egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. A Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a *Calendars.Read* jogosultságot a az alkalmazás regisztrációs adatait. Adja hozzá a *Calendars.Read* a hatókör a `acquireTokenSilent` hívható meg. 

> [!NOTE]
> Felhasználó kérheti további hozzájárulásokat azoktól a hatókörök számának növelésével.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1. hiba:
Az alkalmazást egy összevont Azure Active Directory-tartomány az a következő hibák, amikor bejelentkezési egyike jelenhet meg:
 - "Nem érvényes ügyfél-tanúsítvány található a kérelemben.
 - Nem a felhasználó tanúsítványtárolójában található érvényes tanúsítvány.
 - Próbálja meg újra a másik hitelesítési módszer kiválasztása. "

**OK:** vállalati és a tanúsítványok képességek nem engedélyezettek.

**Megoldás:** kövesse a [összevont tartományt integrált hitelesítés](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>2. hiba:
Engedélyezi a ésőbb [összevont tartományt integrált hitelesítés](#enable-integrated-authentication-on-federated-domains-optional) próbálja használni a Windows Hello Windows 10-es számítógépre jelentkezik be egy több-factor-hitelesítés konfigurációja tartalmazó környezetben, a tanúsítványok listájának számára jelenik meg , azonban ha a PIN-kód használatát választja, a PIN-kód ablak számára nem jelenik meg.

**OK:** Ez az egy ismert korlátozás a Windows 10 asztali verzió (működik a Windows 10 Mobile finom) futó UWP-alkalmazás a Webeshitelesítés-szervező

**Megkerülő megoldás:** megoldás a felhasználók kell úgy, hogy jelentkezzen be az egyéb beállításokat, és válassza a *jelentkezzen be egy felhasználónevet és jelszót* Ehelyett válassza meg a jelszavát, és ezután nyissa meg a telefon hitelesítésen keresztül.

