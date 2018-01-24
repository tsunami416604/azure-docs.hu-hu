---
title: "Hitelesítés, a regisztráció, szerkesztheti a profilját .NET Azure Active Directory B2C |} Microsoft Docs"
description: "Hogyan hozhat létre, amely tartalmazza a bejelentkezéshez, regisztráció, a Windows asztali alkalmazások és profilok kezelése az Azure Active Directory B2C használatával."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: seohack1
ms.openlocfilehash: 5d4664e87ca0a45d59d976f6415fce858bc51dcd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Az Azure AD B2C: A Windows asztali alkalmazás elkészítésére.
Azure Active Directory (Azure AD) B2C használatával az asztali alkalmazást a néhány néhány lépés elvégzésével hatékony önkiszolgáló identitáskezelési funkciókat adhat hozzá. Ez a cikk bemutatja, hogyan hozzon létre egy .NET Windows megjelenítési alaprendszer (WPF) "Feladatlista" alkalmazást, amely tartalmazza a felhasználói regisztráció, bejelentkezés és profilok kezelése. Az alkalmazás támogatni fogja előfizetési, és jelentkezzen be egy felhasználónevet vagy e-mailek használatával. Azt is támogatni fogja előfizetési, és jelentkezzen be például a Facebookhoz és a Google közösségi fiókokkal.

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt.  A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és sok minden mást. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne ebben az útmutatóban.

## <a name="create-an-application"></a>Alkalmazás létrehozása
A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre.  Ügyeljen arra, hogy:

* Tartalmaznak egy **natív ügyfél** az alkalmazásban.
* Másolás a **átirányítási URI** `urn:ietf:wg:oauth:2.0:oob`. Ez a kódminta alapértelmezett URL-címe.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Erre később még szüksége lesz.

## <a name="create-your-policies"></a>Szabályzatok létrehozása
Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Ebben a kódmintában három identitásélményt tartalmaz: regisztráció, bejelentkezés és profilszerkesztés. Szeretné az egyes, ebben a szabályzatban leírtak szerint a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). A három szabályzat létrehozásakor ügyeljen arra, hogy:

* Az identitásszolgáltatók paneljén válassza a **User ID sign-up** (Regisztrálás felhasználói azonosítóval) vagy az **Email sign-up** (Regisztrálás e-mail címmel) lehetőséget.
* A regisztrációs házirendben válassza ki a **megjelenítendő nevet** és az egyéb regisztrációs attribútumokat.
* Alkalmazási jogcímnek minden házirend esetén válassza ki a **megjelenítendő nevet** és az **objektumazonosítót**. Kiválaszthat egyéb jogcímeket is.
* Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A házirendek nevére később még szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A három házirend sikeres létrehozása után készen áll az alkalmazás elkészítésére.

## <a name="download-the-code"></a>A kód letöltése
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). A minta menet közbeni létrehozásához [letöltheti a projektvázát tartalmazó .zip-fájlt](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). A vázprojektet klónozhatja is:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

A kész alkalmazás [.zip fájlként](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) vagy `complete` az adott adattár ágában is elérhető.

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A `TaskClient` projekt a WPF asztali alkalmazás, amely a felhasználó kommunikál. Ez az oktatóanyag céljából egy háttér-feladat webes API-t, minden felhasználói feladatlistát tároló, Azure-ban üzemeltetett meghívja.  Nem kell a webes API-k létrehozására, már meg futnak.

Ha szeretné megtudni, hogyan webes API-k biztonságosan hitelesíti a kérelmeket az Azure AD B2C segítségével, tekintse meg a [webes API-k első lépések a cikk](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Házirendek végrehajtása
Az alkalmazás kommunikál az Azure AD B2C adja meg a HTTP-kérelem részeként végrehajtani kívánt szabályzatot hitelesítési-üzenetek küldésével. Az asztali alkalmazások .NET az előzetes Microsoft hitelesítési könyvtár (MSAL) segítségével OAuth 2.0 hitelesítési üzeneteket küldeni, a házirendek hajtható végre, és webes API-k hívása jogkivonatokhoz.

### <a name="install-msal"></a>MSAL telepítése
Adja hozzá a MSAL a `TaskClient` projektet a Visual Studio Csomagkezelő konzol használatával.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Adja meg a B2C-re vonatkozó információkat
Nyissa meg a fájlt `Globals.cs` , és cserélje le a tulajdonság értékek mindegyike saját. Ez az osztály a teljes használható `TaskClient` való hivatkozás általánosan használt értékeit.

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
Az elsődleges osztály az MSAL `PublicClientApplication`. Ez az osztály az alkalmazás az Azure AD B2C rendszerben jelenti. Ha az alkalmazás initalizes példányt létrehozni `PublicClientApplication` a `MainWindow.xaml.cs`. Ez az ablak teljes is használható.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>A regisztrációs folyamat kezdeményezéséhez
A felhasználó jelentkezik be alkalmazásának kiválasztásával, ha el szeretné indítani a regisztrációs folyamat, amely a regisztrációs szabályzatban létrehozott használja. MSAL használatával egyszerűen meghívja a `pca.AcquireTokenAsync(...)`. A paraméterek átadása akkor `AcquireTokenAsync(...)` határozza meg, melyik jogkivonatot kap, a hitelesítési kérelmet, és több használandó házirendet.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
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

### <a name="initiate-a-sign-in-flow"></a>A bejelentkezési folyamat kezdeményezéséhez
A bejelentkezési folyamat is kezdeményezhető azonos módon, hogy a regisztrációs folyamat elindítása. Amikor egy felhasználó bejelentkezik, a hívást azonos MSAL, hogy a bejelentkezési házirend használatával most:

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

### <a name="initiate-an-edit-profile-flow"></a>Egy profil szerkesztése folyamat kezdeményezéséhez
Ebben az esetben azonos módon egy profil szerkesztése házirend hajthat végre:

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

A fenti esetek mindegyikét olyan, MSAL vagy ad vissza egy jogkivonatot a `AuthenticationResult` vagy kivételt jelez. Minden alkalommal, amikor a jogkivonat beszerzése MSAL, használhatja a `AuthenticationResult.User` objektumot frissíti a felhasználói adatokat az alkalmazásban, például a felhasználói felületen. Adal-t is gyorsítótáraz lexikális eleme az alkalmazás egyéb elemeinek a használatát.

### <a name="check-for-tokens-on-app-start"></a>Ellenőrizze a jogkivonatok az alkalmazás indítása
MSAL segítségével is nyomon követheti, a felhasználói bejelentkezési állapotot.  Az alkalmazáshoz, az azt szeretnénk, a felhasználó a bejelentkezés után is zárja be az alkalmazást, és nyissa meg újra.  Vissza belül a `OnInitialized` bírálja felül, MSAL által használható `AcquireTokenSilent` az ellenőrzési módszert gyorsítótárazott jogkivonatokat:

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
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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

## <a name="call-the-task-api"></a>A feladat API hívása
MSAL most segítségével hajtható végre a házirendek és a jogkivonatok megkapásához.  A feladat API hívása ezeket a jogkivonatokat egy használni kívánt, újra használhatja MSAL tartozó `AcquireTokenSilent` az ellenőrzési módszert gyorsítótárazott jogkivonatokat:

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

Amikor hívása `AcquireTokenSilentAsync(...)` sikeres és egy jogkivonat található a gyorsítótárban, adhat hozzá a jogkivonatot a `Authorization` a HTTP-kérelem fejlécében. A feladat webes API-t fogja használni ezt a fejlécet hitelesíteni a kérelmet a felhasználók feladatlistáit olvasható:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>A felhasználó kijelentkezik
Végezetül segítségével MSAL és az alkalmazás a felhasználói munkamenet befejezése, ha a felhasználó **Kijelentkezés**.  MSAL használatakor mindez törlésének hatására az összes, a jogkivonatok token a gyorsítótárból:

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
Végezetül hozza létre, és a minta futtatásához.  Jelentkezzen az alkalmazás egy e-mail cím vagy a felhasználó nevét. Jelentkezzen ki, és jelentkezzen be ismét ugyanahhoz a felhasználóhoz. A felhasználói profil szerkesztése. Jelentkezzen ki, és jelentkezzen egy másik felhasználó.

## <a name="add-social-idps"></a>Közösségi IDPs hozzáadása
Jelenleg az alkalmazás támogatja-e a csak felhasználói regisztráció és bejelentkezés használó **helyi fiókok**. Ezek a fiókok a B2C-címtárban tárolja, amely a felhasználónév és jelszó használata. Az Azure AD B2C segítségével támogathatja az egyéb identitás-szolgáltatóktól (IDPs) bármely, a kód módosítása nélkül.

Közösségi IDPs hozzáadása az alkalmazáshoz, először ezek a cikkek részletes utasításait követve. Minden egyes IDP, amelyeket támogatni kíván meg kell, hogy a rendszer az alkalmazás regisztrálása és szerezzen be egy ügyfél-azonosítót.

* [Az IDP Facebook beállítása](active-directory-b2c-setup-fb-app.md)
* [Az IDP Google beállítása](active-directory-b2c-setup-goog-app.md)
* [Az IDP Amazon beállítása](active-directory-b2c-setup-amzn-app.md)
* [Az IDP LinkedIn beállítása](active-directory-b2c-setup-li-app.md)

Miután hozzáadta az identitás-szolgáltatóktól B2C-címtárban, módosítania kell a három szabályzat tartalmazza az új IDPs mindegyikének leírtak szerint a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md). A házirend mentése után futtassa újra az alkalmazást. A bejelentkezési hozzáadott új IDPs kell megjelennie, és minden a személyazonosság-előfizetési beállítások észlel.

A házirendek kísérletezhet, és figyelje meg a mintaalkalmazás hatással. Vegye fel vagy távolítsa el a IDPs, kezelheti az alkalmazás jogcímét, vagy módosítsa a regisztrációs attribútumokat. Kísérletet, amíg meg nem látja hogyan házirendeket, a hitelesítési kérelmek és MSAL alkalmazássá.

Az elkészült mintát referenciaként [egy .zip-fájlban is](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Ezenfelül a GitHubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
