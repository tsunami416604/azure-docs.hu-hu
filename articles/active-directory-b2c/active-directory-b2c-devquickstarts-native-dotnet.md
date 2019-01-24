---
title: Hitelesítés, előfizetési, az Azure Active Directory B2C-profil szerkesztése |} A Microsoft Docs
description: Hogyan hozhat létre egy Windows asztali alkalmazás, amely tartalmazza a bejelentkezési, regisztrációs, és profilok kezelése Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 96cdbca1b0a5a75c24b9d0e75eaf1fc861bf764a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843123"
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Windowsos asztali alkalmazás készítése
Az Azure Active Directory (Azure AD) B2C segítségével néhány egyszerű lépéssel az asztali alkalmazás hatékony önkiszolgáló identitáskezelési funkciókat adhat hozzá. Ebből a cikkből megtudhatja, hogyan hozhat létre egy .NET Windows megjelenítési Alaprendszeri (WPF) "Feladatlista" alkalmazást, amely tartalmazza a felhasználói regisztrációt, bejelentkezést és profilkezelést. Az alkalmazás támogatni fogja a regisztrációs és bejelentkezési felhasználónév vagy e-mail használatával. Ez például a Facebook és a Google közösségi fiókok használatával is magában foglalja regisztráció és bejelentkezés támogatása.

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és sok minden mást. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne ebben az útmutatóban.

## <a name="create-an-application"></a>Alkalmazás létrehozása
A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

* Például egy **natív ügyfél** az alkalmazásban.
* Másolás a **átirányítási URI** `urn:ietf:wg:oauth:2.0:oob`. Ez a kódminta alapértelmezett URL-címe.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Erre később még szüksége lesz.

## <a name="create-your-policies"></a>Szabályzatok létrehozása
Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Ez a kódminta három identitásélményt tartalmaz: regisztráció, bejelentkezés és profilszerkesztés. A szabályzat létrehozásakor ügyeljen arra, hogy:

* Az identitásszolgáltatók paneljén válassza a **User ID sign-up** (Regisztrálás felhasználói azonosítóval) vagy az **Email sign-up** (Regisztrálás e-mail címmel) lehetőséget.
* A regisztrációs házirendben válassza ki a **megjelenítendő nevet** és az egyéb regisztrációs attribútumokat.
* Alkalmazási jogcímnek minden házirend esetén válassza ki a **megjelenítendő nevet** és az **objektumazonosítót**. Kiválaszthat egyéb jogcímeket is.
* Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`. A házirendek nevére később még szüksége lesz.

Miután sikeresen létrehozta a szabályzatokat, készen áll az alkalmazás elkészítésére.

## <a name="download-the-code"></a>A kód letöltése
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). A minta menet közbeni létrehozásához [letöltheti a projektvázát tartalmazó .zip-fájlt](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). A vázprojektet klónozhatja is:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

A kész alkalmazás [.zip fájlként](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) vagy `complete` az adott adattár ágában is elérhető.

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A `TaskClient` projekt a WPF asztali alkalmazás, amellyel a felhasználó kommunikál. Ez az oktatóanyag az alkalmazásában háttérbeli feladat webes API-k, az Azure-ban, amely tárolja a felhasználók feladatlistáit üzemeltetett hív meg. Nem kell a webes API készítése, már eleve is működjön az Ön számára.

Ha szeretné megtudni, hogyan webes API-k biztonságos hitelesíti a kérelmeket az Azure AD B2C-vel, tekintse meg a [webes API első lépéseket ismertető cikk](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Hajtsa végre a szabályzatok
Az alkalmazás az Azure AD B2C-vel kommunikál, adja meg a szabályzatot, hajtsa végre a HTTP-kérelem részeként szeretne hitelesítési üzenetek küldésével. .NET asztali alkalmazások esetén használhatja az OAuth 2.0 hitelesítési üzenetek küldéséhez, hajtsa végre a szabályzatok a előzetes verzióját a Microsoft-hitelesítési tár (MSAL), és webes API-k hívása tokenekhez.

### <a name="install-msal"></a>Az MSAL telepítése
Adja hozzá a MSAL az `TaskClient` projekt a Visual Studio Csomagkezelői konzol használatával.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Adja meg a B2C-re vonatkozó információkat
Nyissa meg a fájlt `Globals.cs` , és cserélje le a tulajdonságértékek mindegyike saját. Ez az osztály használja `TaskClient` gyakran használt hivatkozás értékre.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>A PublicClientApplication létrehozása
Az elsődleges osztály az MSAL `PublicClientApplication`. Ez az osztály az alkalmazás az Azure AD B2C-vel rendszerben jelenti. Ha az alkalmazás initalizes hozzon létre egy példányt `PublicClientApplication` a `MainWindow.xaml.cs`. Ez az időszak során is használható.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default. Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Egy regisztrációs folyamatának elindításához
Egy felhasználó jóváhagyja jelentkezik be, ha el szeretné indítani a regisztrációs szabályzatban létrehozott használó regisztrációs folyamatot. Az MSAL használatával, csak hívja `pca.AcquireTokenAsync(...)`. Az átadott paraméterek `AcquireTokenAsync(...)` határozza meg, melyik jogkivonatot kap, a házirend a hitelesítési kérelmet, és további használt.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API). Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>A bejelentkezési folyamatának elindításához
A bejelentkezési folyamatának elindításához ugyanolyan módon, hogy a regisztrációs folyamat elindításához. Amikor egy felhasználó bejelentkezik, a hívást ugyanazt az MSAL, ezúttal a bejelentkezési szabályzat használatával:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Egy profil szerkesztése folyamatának elindításához
Egy profil szerkesztése házirend újra, az azonos módon hajthat végre:

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

A fenti esetek mindegyikét MSAL vagy egy tokent ad vissza a `AuthenticationResult` vagy kivételt jelez. Minden alkalommal, amikor egy token lekérése az MSAL, használhatja a `AuthenticationResult.User` objektumot frissíti a felhasználói adatokat az alkalmazásban, például a felhasználói felületen. Adal-t is gyorsítótárazza a jogkivonat az alkalmazás egyéb részei a használatra.

### <a name="check-for-tokens-on-app-start"></a>Alkalmazás indítása a jogkivonatok keresése
Az MSAL segítségével nyomon követheti, a felhasználó bejelentkezési állapotát. Ebben az alkalmazásban szeretnénk, a felhasználó számára is, zárja be az alkalmazást, és nyissa meg újra után is bejelentkezve maradjon. Vissza belül a `OnInitialized` bírálja felül, használható az MSAL a `AcquireTokenSilent` az ellenőrzési módszert gyorsítótárazott token:

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache. Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>A feladat-API meghívása
Hajtsa végre a házirendek és a jogkivonatok megkapásához MSAL most már használt. Ha a feladat-API meghívásához ezek a jogkivonatok egy használni kívánt, újra használhatja MSAL a `AcquireTokenSilent` az ellenőrzési módszert gyorsítótárazott token:

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Ha a hívást `AcquireTokenSilentAsync(...)` sikeres és a egy token található a gyorsítótárban, adhat hozzá a jogkivonatot a `Authorization` a HTTP-kérés fejlécébe. A feladat webes API a fejléc használatával hitelesíteni a kérelmet, olvassa el a felhasználók feladatlistáit:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>A felhasználó kijelentkeztetése
Végül választva a felhasználó az alkalmazás a felhasználói munkamenet befejezéséhez használhatja az MSAL **Kijelentkezés**. Ha az MSAL használatával törlésével az összes a tokeneket a token a gyorsítótárból mindez:

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása
Végül hozhat létre, és futtassa a mintát. Jelentkezzen az alkalmazás egy e-mail címet vagy felhasználói nevet. Jelentkezzen ki, és jelentkezzen be ismét ugyanazt a felhasználót. Annak a felhasználónak a profil szerkesztése. Jelentkezzen ki, és jelentkezzen egy másik felhasználó.

## <a name="add-social-idps"></a>Közösségi identitásszolgáltató hozzáadása
Jelenleg az alkalmazás támogatja-e a csak a felhasználói regisztráció és bejelentkezés használó **helyi fiókok**. Ezek a fiókok a B2C-címtárban tárolt, amely a felhasználónév és jelszó használata. Azure AD B2C használatával bármely, a kód módosítása nélkül adhat hozzá más identitásszolgáltató (IDP) támogatása.

A közösségi identitásszolgáltató hozzáadása az alkalmazáshoz, először a részletes utasításait végrehajtva ezek a cikkek. Minden egyes Identitásszolgáltatót szeretne biztosítani meg kell regisztrálni egy alkalmazást, hogy a rendszer, és szerezzen be egy ügyfél-azonosítót.

* [Facebook-Identitásszolgáltató beállítása](active-directory-b2c-setup-fb-app.md)
* [Google-Identitásszolgáltató beállítása](active-directory-b2c-setup-goog-app.md)
* [Amazon-Identitásszolgáltató beállítása](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn-Identitásszolgáltató beállítása](active-directory-b2c-setup-li-app.md)

Miután hozzáadta az identitás-szolgáltatóktól B2C-címtárban, kell szerkesztenie, tartalmazza az új identitásszolgáltató használatát, a három szabályzat minden egyes leírtak szerint a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md). Miután mentette a szabályzatokat, futtassa újra az alkalmazást. Az új identitásszolgáltató bejelentkezési hozzáadott kell megjelennie, és minden, az identitás-előfizetési beállítások élményhez.

A szabályzatok kísérletezhet, és figyelje meg az eredményt a mintaalkalmazást a. Adja hozzá, vagy távolítsa el az identitásszolgáltató használatát, alkalmazás jogcímét, vagy módosítsa a regisztrációs attribútumokat. Kísérletet, amíg nem láthatja, hogy milyen szabályzatok, a hitelesítési kérések és a MSAL alkalmazássá.

Ha az elkészült mintát [.zip-fájlként biztosított](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Ezenfelül a GitHubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
