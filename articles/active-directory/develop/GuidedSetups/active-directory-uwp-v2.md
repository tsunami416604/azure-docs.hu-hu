---
title: Ismerkedés az Azure AD v2 UWP |} Microsoft Docs
description: Hogyan univerzális Windows Platform alkalmazások (UWP) meghívhatja az API-k, amelyhez hozzáférési jogkivonatok az Azure Active Directory-v2 végpontja
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
ms.openlocfilehash: c2d5681e30651aac7a09a8ead923015e9a892d42
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796614"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph API hívása (XAML) univerzális Windows Platform-alkalmazásból

Ez az útmutató ismerteti, hogyan egy natív univerzális Windows Platform (UWP-) alkalmazás olyan hozzáférési jogkivonatot kérelmezhet és hívhatja meg Microsoft Graph API-val. Az útmutató többi API-k, az Azure Active Directory v2 végpont a hozzáférési jogkivonatok igénylő is vonatkozik.

Ez az útmutató végén az alkalmazás személyes fiókok használatával meghívja a védett API. Többek között Outlook.com-os, live.com és mások számára. Az alkalmazás is meghívja a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely Azure Active Directoryban.

>[!NOTE]
> Ez az útmutató az univerzális Windows Platform fejlesztési telepítve van szükség a Visual Studio 2017. Lásd: [beolvasása beállítása](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) , hogy töltse le és univerzális Windows Platform-alkalmazások fejlesztéséhez a Visual Studio konfigurálása.

### <a name="how-this-guide-works"></a>Ez az útmutató működése

![Ez az útmutató graph működése](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Ez az útmutató UWP mintaalkalmazás, amely lekérdezi a Microsoft Graph API vagy egy webes API, amely támogatja az Azure Active Directory v2 végpont származó jogkivonatokat hoz létre. Ebben az esetben jogkivonat adni a hitelesítési fejlécéhez via HTTP-kérelmekre. Microsoft hitelesítési könyvtár (MSAL) kezeli a token kérése és megújítását.

### <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagok használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft hitelesítési kódtár|


## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen bemutatja a Windows asztali .NET-alkalmazás (XAML) történő integrálását a *jelentkezzen be Microsoft*. Majd lekérheti a webes API-k jogkivonatot, például a Microsoft Graph API szükséges.

Ez az útmutató alkalmazást hoz létre, amely egy gomb megjeleníti, hogy lekérdezések Graph API-val, a Kijelentkezés gombra és a szövegmezőbe, amely a hívások eredményének megjelenítése.

>[!NOTE]
> Szeretné ezt a mintát Visual Studio-projekt letöltése helyett? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) és ugorjon a [regisztrációja](#register-your-application "alkalmazás regisztrációs lépésében") lépéssel konfigurálhatja a kódminta futtatása előtt.


### <a name="create-your-application"></a>Az alkalmazás létrehozása
1. A Visual Studio válassza **fájl** > **új** > **projekt**.
2. A **sablonok**, jelölje be **Visual C#**.
3. Válassza a **Blank App (Universal Windows)** (Üres alkalmazás (Univerzális Windows-platform)) elemet.
4. Az alkalmazás neve, és válassza ki **OK**.
5. Ha a rendszer kéri, válassza ki a bármely verziójáról a **cél** és **minimális** verzióit, és válassza ki **OK**.

    >![Minimális és a cél-verziók](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft-hitelesítési tár hozzáadása a projekthez
1. A Visual Studio válassza **eszközök** > **NuGet-Csomagkezelő** > **Csomagkezelő konzol**.
2. Másolja és illessze be a következő parancsot a **Csomagkezelő konzol** ablakban:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Ez a parancs elvégzi [Microsoft hitelesítési tár](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL szerez be, gyorsítótárazza, és frissíti a felhasználói jogkivonatokhoz, amelyek védi az Azure Active Directory v2 API-k eléréséhez.

## <a name="initialize-msal"></a>MSAL inicializálása
Ebben a lépésben hozzon létre egy osztályt, például jogkivonatokat kezelése MSAL, interakcióba kezeléséhez nyújt segítséget.

1. Nyissa meg a **App.xaml.cs** fájlt, és a hivatkozás a MSAL osztályra:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Adja hozzá a következő két sort az alkalmazás osztály (belső <code>sealed partial class App : Application</code> blokk):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói Felületüket létrehozni

A **MainPage.xaml** fájl automatikusan létrejön a projekt sablon részeként. Nyissa meg ezt a fájlt, és kövesse az utasításokat:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>MSAL segítségével szolgáltatáshitelesítést egy token Microsoft Graph API-hoz.

Ez a szakasz bemutatja, hogyan MSAL használandó Microsoft Graph API a szolgáltatáshitelesítést egy token.

1.  A **MainPage.xaml.cs**, a hivatkozás hozzáadása a osztály MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Cserélje le a kódját a <code>MainPage</code> osztály az alábbi kódra:

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
Hívása a `AcquireTokenAsync` módszer eredményezi egy ablak, amely felszólítja a felhasználók jelentkezhetnek be. Alkalmazások általában a felhasználóknak az első alkalommal védett erőforrás eléréséhez szükséges interaktív bejelentkezéshez. Előfordulhat, hogy is szükségük jelentkezzen be, amikor egy csendes jogkivonat megszerzése sikertelen. Például akkor, ha a jelszó lejárt.

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése
A `AcquireTokenSilentAsync` metódus kezeli a token kérése és megújításokat felhasználói beavatkozás nélkül. Után `AcquireTokenAsync` először végrehajtása és a felhasználótól a hitelesítő adatokat, a `AcquireTokenSilentAsync` metódus jogkivonatokat ezt a kérelmet, mert csendes szerezni az jogkivonatok használatával. MSAL jogkivonat gyorsítótára és -megújítás fogja kezelni.

Végül a `AcquireTokenSilentAsync` metódus sikertelen. A hiba okait, hogy felhasználók rendelkeznek-e vagy kijelentkezteti a, vagy módosítani a jelszavát egy másik eszközön lehet. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MsalUiRequiredException` kivétel. Az alkalmazás kezeli ezt a kivételt, két módon:

* Az elleni hívás kezdeményezése `AcquireTokenAsync` azonnal. A hívás eredménye jelentkezzen be a felhasználótól. Ebben a mintában az online alkalmazások használják általában, ahol a felhasználó nincs elérhető kapcsolat nélküli tartalom van. A minta az interaktív telepítő által létrehozott mintát követi. A művelet az első futtatásakor a minta témakörben talál. 
    * Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` null értéket tartalmaz, és egy `MsalUiRequiredException` kivétel történt.
    * A kód a minta majd kezeli a kivételt meghívásával `AcquireTokenAsync`. A hívás eredménye jelentkezzen be a felhasználótól.

* Vagy ehelyett megadja egy visual arra utal, hogy a felhasználók számára, hogy az interaktív bejelentkezési szükség. Majd jelentkezzen be a megfelelő időben is választhatnak. Vagy az alkalmazás újra `AcquireTokenSilentAsync` később. Ebben a mintában használt gyakran, amikor a felhasználók használhatnak más alkalmazás működésének szüneteltetése nélkül. Például akkor, ha a kapcsolat nélküli tartalom érhető el az alkalmazást. Ebben az esetben felhasználók dönthet arról szeretne bejelentkezni a védett erőforrások eléréséhez, vagy az elavult adatok frissítése. Ellenkező esetben az alkalmazás dönt, hogy próbálja meg újra, vagy `AcquireTokenSilentAsync` amikor állítják vissza a hálózaton, után, átmenetileg nem érhető el.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Microsoft Graph API hívása csak megszerzett jogkivonattal használatával

* Adja hozzá a következő új metódust **MainPage.xaml.cs**. Ez a módszer biztosítja a `GET` Graph API kérelmet egy [engedélyezés] fejléc használatával:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a ellen védett API REST hívás

Ebben a minta az alkalmazásban a `GetHttpContentWithToken` módszer használható HTTP `GET` jogkivonat szükséges, amely védett erőforrás kérelmet. Majd a módszer a hívónak a tartalmat adja vissza. Ez a módszer hozzáadja a megszerzett lexikális elem szerepel a **HTTP engedélyezési** fejléc. Ez a minta az erőforrás a Microsoft Graph API **me** végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>A felhasználó kijelentkezik egy olyan metódus hozzáadása

* A felhasználó kijelentkezik, adja hozzá a következő metódust **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>További információ a kijelentkezési

A `SignOutButton_Click` metódus eltávolítja a felhasználót a MSAL felhasználói gyorsítótárból. Ez a módszer gyakorlatilag megmondja MSAL az aktuális felhasználó törlésére. Ezután egy jövőbeli jogkivonatot szerezni sikeres csak akkor, ha kell interaktív történik.
Ez a példa az alkalmazás támogatja az egy-egy felhasználóhoz. Azonban a MSAL forgatókönyvek, ahol több fiókjával is bejelentkezhet a egyszerre. Példa: Ha a felhasználó rendelkezik-e a különböző fiókok e-mail alkalmazást.

## <a name="display-basic-token-information"></a>Alapvető lexikális elem adatainak megjelenítése

* Adja hozzá a következő metódust **MainPage.xaml.cs** a token kapcsolatos alapvető információk megjelenítése:

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

Azonosító-jogkivonatokat keresztül szerzett **OpenID Connect** egy szűk köre a felhasználók számára vonatkozó információkat is tartalmazhat. `DisplayBasicTokenInfo` a token alapvető információkat jeleníti meg. Többek között a felhasználó megjelenített neve és azonosítója, a jogkivonatot, és a hozzáférési jogkivonat maga jelölő karakterlánc lejárati dátuma. Ha bejelöli a **Microsoft Graph API hívása** többször gombra kattint, láthatja, hogy ugyanezt a tokent további kérelmeknél lett-e használni. A lejárati dátum, amikor MSAL úgy dönt, hogy a token megújításához ideje kiterjesztett is megtekinthető.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Most regisztrálnia kell az alkalmazást a Microsoft alkalmazás regisztrációs portálon:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása.
2. Adjon meg egy nevet az alkalmazásnak.
3. Győződjön meg arról, hogy a kívánt beállítást **interaktív telepítés** van *nincs kiválasztva*.
4. Válassza ki **hozzáadása platformok**, jelölje be **natív alkalmazás**, majd válassza ki **mentése**.
5. A GUID-azonosítóját **Alkalmazásazonosító**, lépjen vissza a Visual Studio, nyissa meg a **App.xaml.cs**, és cserélje le `your_client_id_here` az imént regisztrált alkalmazás azonosítójával:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Engedélyezze az integrált hitelesítést összevont tartományt (nem kötelező)

Integrált Windows-hitelesítés egy összevont Azure Active Directory-tartomány használata esetén az alkalmazás jegyzékében kell engedélyezéséhez további funkciókat:

1. Kattintson duplán a **Package.appxmanifest**.
2. Válassza ki a **képességek** lapra, és győződjön meg arról, hogy engedélyezve vannak-e a következő beállításokat:

    - Vállalati hitelesítés
    - Magánhálózatok (ügyfél és kiszolgáló)
    - Megosztott felhasználói tanúsítványok

3. Nyissa meg **App.xaml.cs** , és adja hozzá a következő sort az alkalmazás konstruktorban:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Integrált Windows-hitelesítés alapértelmezés szerint ez a minta nincs konfigurálva. Igénylő alkalmazások *vállalati hitelesítési* vagy *megosztott felhasználói tanúsítványok* képességek a Windows áruház általi ellenőrzés magasabb szintű igényelnek. Nem minden fejlesztők továbbá kíván végrehajtani a magasabb szintű ellenőrzést. Engedélyezi ezt a beállítást csak akkor, ha egy összevont Azure Active Directory-tartomány a Windows-hitelesítés szükséges.


## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás teszteléséhez, válassza ki az F5 billentyűt a projekt a Visual Studióban. A fő ablak jelenik meg:

![Alkalmazás felhasználói felületén](media/active-directory-uwp-v2.md/testapp-ui.png)

Ha elkészült, teszteléséhez, válassza ki a **Microsoft Graph API hívása**. Ezután használja a Microsoft Azure Active Directory szervezeti fiókot vagy a Microsoft-fiókkal, például live.com vagy Outlook.com-os, jelentkezzen be. Ha most először, a felhasználó bejelentkezhet kérő ablak jelenik meg:

![Bejelentkezési oldal](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Hozzájárulás
Jelentkezzen be az alkalmazást, először találkozik az alábbihoz hasonló hozzájárulási képernyő. Válassza ki **Igen** explicit módon beleegyezését eléréséhez:

![Hozzáférés hozzájárulási képernyője](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Felhasználói profil adatait a Microsoft Graph API-hívás által visszaadott látja a **API-hívási eredmények** képernyőjén:

![API-hívási eredmények képernyő](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

A token keresztül szerzett alapvető információkat is látni `AcquireTokenAsync` vagy `AcquireTokenSilentAsync` a a **Token adatait** mezőbe:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|**Name (Név)** |Felhasználó teljes neve|A felhasználó nagyapja vezeték- és keresztneve.|
|**Felhasználónév** |<span>user@domain.com</span> |A felhasználónév, amely azonosítja a felhasználót.|
|**Jogkivonat lejár** |DateTime |A jogkivonat lejárati idejének. MSAL szükség szerint a token megújításával terjeszti ki a lejárati dátum.|
|**Hozzáférési jogkivonat** |Karakterlánc |A lexikális elem karakterlánca küldött HTTP-kérelmek igénylő egy *Authorization fejlécet*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Megtudhatja, mi az a hozzáférési jogkivonat (nem kötelező)
Szükség esetén másolja az értéket **Access Token** és illessze be https://jwt.ms dekódolni a, és tekintse meg a jogcímek listája.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörök és delegált jogosultságokkal sikeresen telepítették

Microsoft Graph API megköveteli a *user.read* hatókörrel, hogy a felhasználói profil olvasása. Ebben a hatókörben minden egyes, amely regisztrálva van az alkalmazásregisztrációs portálra az alkalmazás alapértelmezés szerint automatikusan kerül. Más Microsoft Graph API-kat, és egyéni API-k, a háttér-kiszolgáló további hatókörökkel lehet szükség. Microsoft Graph API megköveteli a *Calendars.Read* hatókörrel, hogy a felhasználók naptáraiban lévő listából.

A felhasználók naptáraiban a környezetben, az alkalmazások eléréséhez vegye fel a *Calendars.Read* jogosultságot a az alkalmazás regisztrációs adatait. Majd adja hozzá a *Calendars.Read* a hatókör a `acquireTokenSilent` hívható meg. 

> [!NOTE]
> Felhasználók további hozzájárulásokat azoktól a hatókörök számának növelésével kérheti.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1 probléma
Megjelenhet az alábbi hibaüzenetek valamelyike jelentkezik be az alkalmazást egy összevont Azure Active Directory-tartomány:
 - Nem található a kérelemben érvényes ügyféltanúsítványt.
 - Nem a felhasználó tanúsítványtárolójában található érvényes tanúsítvány.
 - Válasszon egy másik hitelesítési módszert.

**OK:** nincsenek engedélyezve a vállalati és a tanúsítvány képességeket.

**Megoldás:** kövesse a [összevont tartományt integrált hitelesítés](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>A probléma 2
Engedélyezi a [összevont tartományt integrált hitelesítés](#enable-integrated-authentication-on-federated-domains-optional) próbálja használni a Windows Hello a Windows 10 rendszerű számítógépeket a bejelentkezni egy környezetében többtényezős hitelesítés konfigurálva. Akkor jelenik meg a tanúsítványok listáját. Azonban ha a PIN-kód használatát választja, a PIN-kód ablak számára nem jelenik meg.

**OK:** probléma a webeshitelesítés-szervező, amely a Windows 10 asztali környezetben futnak UWP-alkalmazások ismert korlátozása. A Windows 10 Mobile jól működik.

**Megkerülő megoldás:** válasszon **jelentkezzen be más beállítások**. Válassza ki **jelentkezzen be egy felhasználónevet és jelszót**. Válassza ki **adja meg a jelszó**. A telefonos hitelesítési folyamatot, majd folytassa.
