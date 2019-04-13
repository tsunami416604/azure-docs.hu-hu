---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3f72f6a5097221c904faff633b5a4ee5a6e023c1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532635"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Használható az MSAL egy token beszerzése a Microsoft Graph API-hoz

Ebben a szakaszban Ön használható az MSAL egy token beszerzése a Microsoft Graph API-hoz.

1. Az a *MainWindow.xaml.cs* fájlt, adja hozzá a hivatkozást az MSAL az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Cserélje le a `MainWindow` osztályban az alábbi kódot:

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

Hívása a `AcquireTokenInteractive` módszer eredményezi egy ablak, amely felkéri a felhasználót, hogy jelentkezzen be. Alkalmazások általában felhasználóktól interaktívan jelentkezik be először egy védett erőforrás eléréséhez szükséges. Akkor is előfordulhat, hogy kell bejelentkezni, amikor egy token beszerzéséhez a beavatkozás nélküli művelet sikertelen (például, ha a felhasználó jelszava lejárt).

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus kezeli a token beszerzését és a megújítások felhasználói beavatkozás nélkül. Miután `AcquireTokenInteractive` hajtja végre az első alkalommal `AcquireTokenSilent` a szokásos módszer használatával szerezze be a jogkivonatokat, amelyeket a későbbi hívások, védett erőforrások eléréséhez, mert kérelem vagy token megújítása hívások beavatkozás nélkül.

Végül a `AcquireTokenSilent` metódus sikertelen lesz. A hiba oka lehet, hogy a felhasználó kijelentkeztetése vagy módosítani a jelszavát egy másik eszközön. Ha az MSAL észleli, hogy a probléma megoldhatók egy interaktív intézkedést kér, akkor aktiválódik egy `MsalUiRequiredException` kivétel. Az alkalmazás ehhez a kivételhez, két módon tudják kezelni:

* Azt is ellenőrizze, egy hívást kell végrehajtanunk `AcquireTokenInteractive` azonnal. Ez a hívás eredménye kéri a felhasználót, hogy jelentkezzen be. Az online alkalmazások általában használják ezt a mintát, ha a felhasználó nem érhető el kapcsolat nélküli tartalom. A minta az interaktív telepítés által létrehozott követi, ebben a mintában a művelet az első alkalommal végre, a minta az is látható. 

* Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` obsahuje hodnotu null, és a egy `MsalUiRequiredException` kivétel történt. 

* A kód a minta ezután kezeli a kivételt meghívásával `AcquireTokenInteractive`, aminek eredményeképpen kéri a felhasználót, hogy jelentkezzen be.

* Azt is inkább jelenthet egy vizuális jelzés a felhasználók számára, amely egy interaktív bejelentkezési szükség, úgy, hogy és kiválaszthatja a megfelelő időben való bejelentkezéshez. Vagy az alkalmazás megpróbálhatja `AcquireTokenSilent` később. Ezt a mintát gyakran használják, amikor a felhasználók használhatják más színvonalának – az alkalmazás funkciói például, ha offline tartalom érhető el az alkalmazást. Ebben az esetben felhasználók megadhatja, hogy szeretne hozzáférni a védett erőforrásokhoz, vagy frissítse az elavult adatokat. Másik lehetőségként az alkalmazás dönt, hogy újra `AcquireTokenSilent` Ha visszaállítja a hálózat elvégzése után átmenetileg nem érhető el.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API meghívása éppen megszerzett jogkivonattal használatával

Adja hozzá a következő új metódust a `MainWindow.xaml.cs`. Győződjön meg arról, hogy a módszert használják egy `GET` kérelmet Graph API-engedélyezés fejléc használatával:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>A védett API REST-hívást végrehajtásával kapcsolatos további információkat

Ez a mintaalkalmazás használja a `GetHttpContentWithToken` metódust, hogy egy olyan HTTP `GET` kérelmek ellen védett erőforrás, amely a jogkivonat szükséges, és majd a tartalom térjen vissza a hívónak. Ez a módszer a megszerzett jogkivonattal a HTTP-engedélyezési fejléc hozzáadása. Ebben a példában az erőforrás a Microsoft Graph API *me* végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Adjon meg egy metódust ki egy felhasználót

Jelentkezzen ki egy felhasználót, adja hozzá a következő metódust a `MainWindow.xaml.cs` fájlt:

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
### <a name="more-information-about-user-sign-out"></a>További információ a felhasználó a Kijelentkezés

A `SignOutButton_Click` metódus felhasználókat távolít el a MSAL felhasználói gyorsítótárát, amely gyakorlatilag megmondja a MSAL az aktuális felhasználó törlésére úgy, hogy a jogkivonat-beszerzési egy későbbi kérés sikeres lesz, csak akkor, ha interaktív történik.

Bár ebben a példában az alkalmazás támogatja az egyszeri felhasználók, az MSAL forgatókönyvek, ahol több fiókot is bejelentkezhet a egyszerre támogatja. Ilyen például az e-mail alkalmazást, ahol a felhasználó rendelkezik-e több fiókot.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

A jogkivonat alapvető információit jeleníti meg, adja hozzá a következő metódust a *MainWindow.xaml.cs* fájlt:

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

A hozzáférési jogkivonatot, amellyel a Microsoft Graph API meghívása után a felhasználó bejelentkezik, mellett MSAL is egy azonosító jogkivonat kéri le. Ez a token részhalmazát, amely a felhasználók számára a profiljával kapcsolatos információkat tartalmaz. A `DisplayBasicTokenInfo` metódus az alapvető információkat jeleníti meg a jogkivonatot. Ha például jeleníti meg a felhasználó megjelenített neve és azonosítója, valamint a jogkivonat lejárati dátuma és a karakterlánc, amely a hozzáférési jogkivonat magát. Kiválaszthatja a *Microsoft Graph API meghívása* többször gombra, és tekintse meg, hogy további kérésekhez fel lett-e újra ugyanezt a tokent. Megtekintheti a lejárati dátum kiterjesztendő, amikor az MSAL úgy dönt, hogy az idő, a-token megújításához is.
<!--end-collapse-->
