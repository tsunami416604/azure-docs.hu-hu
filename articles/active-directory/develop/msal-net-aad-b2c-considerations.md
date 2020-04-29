---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg az Azure AD B2C és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokkal.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533955"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>A MSAL.NET használata a felhasználók közösségi identitásokkal való bejelentkezéséhez

A MSAL.NET használatával közösségi identitásokkal jelentkezhet be a felhasználókba [Azure Active Directory B2C (Azure ad B2C)](https://aka.ms/aadb2c)használatával. Azure AD B2C a szabályzatok fogalma köré épül fel. A MSAL.NET-ben egy szabályzatot kell megadnia, amely egy szolgáltatót biztosít.

- A nyilvános ügyfélalkalmazás létrehozásakor meg kell adnia a szabályzatot a szolgáltatónál.
- Ha alkalmazni szeretné a szabályzatot, meg kell hívnia egy `AcquireTokenInteractive` `authority` paramétert tartalmazó felülbírálást.

Ez az oldal a 3. x MSAL. Ha érdekli a 2. x MSAL, tekintse meg [a Azure ad B2C a MSAL 2. x verziójában](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C bérlő és házirend szolgáltatója

A használandó `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` szolgáltató a következő:

- `azureADB2CHostname`a Azure AD B2C bérlő és a gazdagép neve (például `{your-tenant-name}.b2clogin.com`).
- `tenant`a Azure AD B2C bérlő teljes neve (például `{your-tenant-name}.onmicrosoft.com`) vagy a bérlő GUID azonosítója,
- `policyName`az alkalmazni kívánt házirend vagy felhasználói folyamat neve (például: "b2c_1_susi" a regisztrációhoz/bejelentkezéshez).

A Azure AD B2C-hatóságokkal kapcsolatos további információkért tekintse meg ezt a [dokumentációt](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Az alkalmazás példányának példánya

Az alkalmazás létrehozásakor meg kell adnia a szolgáltatót.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Jogkivonat beszerzése házirend alkalmazásához

A nyilvános ügyfélalkalmazás Azure AD B2C védett API-hoz való jogkivonatának beszerzéséhez a felülbírálásokat egy szolgáltatóval kell használni:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

a következőre:

- `policy`a korábbi karakterláncok egyike (például `PolicySignUpSignIn`).
- `ParentActivityOrWindow`szükséges az Androidhoz (a tevékenységhez), és nem kötelező más platformokhoz, amelyek támogatják a szülő felhasználói felületet, például a Windowst a Windowsban és a UIViewController az iOS-ben. További információt [itt talál a felhasználói felület párbeszédpanelen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`egy olyan metódus, amely megkeresi az adott szabályzathoz tartozó fiókot. Például:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Egy házirend vagy felhasználói folyamat alkalmazása (például a végfelhasználói profil szerkesztése vagy a jelszó alaphelyzetbe állítása) jelenleg a meghívásával `AcquireTokenInteractive`történik. A két házirend esetében nem használja a visszaadott jogkivonat/hitelesítési eredményt.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile-és ResetPassword metódusát-szabályzatok speciális esete

Ha olyan felhasználói élményt szeretne biztosítani, amelyben a végfelhasználók bejelentkeznek a közösségi identitással, majd szerkeszthetik a profiljaikat, alkalmazni kívánja a profil szerkesztése Azure AD B2C. Ennek a módszernek az a módja, `AcquireTokenInteractive` ha meghívja az adott szabályzathoz tartozó adott szolgáltatót, és `Prompt.NoPrompt` egy olyan kérést kap, amely megakadályozza a fiók kiválasztási párbeszédpanelének megjelenítését (mivel a felhasználó már be van jelentkezve, és aktív cookie-munkamenettel rendelkezik).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Erőforrás-tulajdonos jelszavának hitelesítő adatai (ROPC) Azure AD B2C
A ROPC folyamattal kapcsolatos további részletekért tekintse meg ezt a [dokumentációt](v2-oauth-ropc.md).

Ez a folyamat **nem ajánlott** , mert az alkalmazás a jelszót kérő felhasználó nem biztonságos. A problémával kapcsolatos további információkért tekintse meg [ezt a cikket](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

A Felhasználónév/jelszó használatával több dolgot is megadhat:
- A modern identitás alapvető alapelvei: a jelszó bekerül, majd újra lejátszva. Mivel egy titkos megosztási titok ezt a fogalmát felhasználhatja. Ez nem kompatibilis a jelszóval.
- Az MFA-t igénylő felhasználóknak nem lehet bejelentkezniük (mivel nincs interakció).
- A felhasználók nem tudnak egyszeri bejelentkezést végezni.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>A ROPC folyamat konfigurálása Azure AD B2C
A Azure AD B2C-bérlőben hozzon létre egy új felhasználói folyamatot, és válassza a bejelentkezés lehetőséget a **ROPC használatával**. Ez lehetővé teszi a bérlő ROPC-szabályzatának engedélyezését. További részletekért lásd: [az erőforrás-tulajdonos jelszava hitelesítő adatainak konfigurálása](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication`metódust tartalmaz:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

A metódus a következő paramétereket veszi figyelembe:
- Azok a *hatókörök* , amelyekhez hozzáférési tokent kell kérni.
- Egy *Felhasználónév*.
- A felhasználó SecureString *jelszava* .

Ne felejtse el használni a ROPC házirendet tartalmazó szolgáltatót.

### <a name="limitations-of-the-ropc-flow"></a>A ROPC folyamat korlátai
 - A ROPC folyamat **csak helyi fiókoknál működik** (a Azure ad B2C e-mailben vagy felhasználónévvel való regisztrálásakor). Ez a folyamat nem működik, ha a Azure AD B2C (Facebook, Google stb.) által támogatott egyesítő.

## <a name="google-auth-and-embedded-webview"></a>Google-hitelesítés és beágyazott webnézet

Ha Ön egy Azure AD B2C fejlesztő, aki a Google-t használja identitás-szolgáltatóként, a rendszerböngészőt használja, mivel a Google nem engedélyezi a [hitelesítést a beágyazott webnézetekben](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Jelenleg egy `login.microsoftonline.com` megbízható szolgáltató a Google-ban. A szolgáltató használata a beágyazott webnézettel fog működni. A használata `b2clogin.com` azonban nem megbízható a Google-ban, így a felhasználók nem fognak tudni hitelesítést végezni.

Ha változnak a dolgok, a [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) frissítését biztosítjuk.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Azure AD B2C gyorsítótárazása a MSAL.Net-ben

### <a name="known-issue-with-azure-ad-b2c"></a>Ismert probléma a Azure AD B2C

A MSAL.Net támogatja a [jogkivonat-gyorsítótárat](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). A jogkivonat-gyorsítótárazási kulcs az identitás-szolgáltató által visszaadott jogcímek alapján történik. Jelenleg a MSAL.Net két jogcímet igényel a jogkivonat-gyorsítótár kulcsának létrehozásához:
- `tid`Ez az Azure AD-bérlő azonosítója, és
- `preferred_username`

Ezek a jogcímek számos Azure AD B2C esetben hiányoznak.

Az ügyfél hatással van arra, hogy amikor a Felhasználónév mezőt szeretné megjeleníteni, "hiányzik a jogkivonat-válaszból" értékként? Ha igen, ennek az az oka, hogy Azure AD B2C nem ad vissza értéket a preferred_username IdToken a közösségi fiókok és külső identitás-szolgáltatók (IDP-EK) korlátai miatt. Az Azure AD egy értéket ad vissza a preferred_usernamehoz, mert tudja, hogy kik a felhasználók, de a Azure AD B2C esetében, mert a felhasználó helyi fiókkal, Facebook-, Google-, GitHub-és egyéb szolgáltatásokkal is bejelentkezhet preferred_username Azure AD B2C. A ADAL-mel való MSAL-kompatibilitás blokkolásának feloldásához úgy döntöttünk, hogy a "hiányzó a jogkivonat-válaszból" kifejezést használjuk az Azure AD B2C-fiókok kezelésekor, amikor a IdToken semmit nem ad vissza a preferred_username. A MSAL-nek egy értéket kell visszaadnia preferred_username számára a gyorsítótár-kompatibilitás fenntartásához a kódtárak között.

### <a name="workarounds"></a>Kerülő megoldások

#### <a name="mitigation-for-the-missing-tenant-id"></a>A hiányzó bérlői azonosító enyhítése

A javasolt Áthidaló megoldás a gyorsítótárazás használata [házirend szerint](#acquire-a-token-to-apply-a-policy)

Alternatív megoldásként használhatja a `tid` jogcímet is, ha a [B2C egyéni házirendeket](https://aka.ms/ief)használja, mert lehetővé teszi, hogy további jogcímeket ad vissza az alkalmazásnak. További információ a [jogcímek átalakításáról](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>A "hiányzó jogkivonat-válasz" megoldásának enyhítése
Az egyik lehetőség a "név" jogcím használata előnyben részesített felhasználónévként. Ennek a [B2C doc](../../active-directory-b2c/user-flow-overview.md) ->nak a folyamata szerepel a Return jogcím oszlopban, majd válassza ki azokat a jogcímeket, amelyeket az alkalmazásnak a sikeres profil-szerkesztési élmény után visszaküldött engedélyezési jogkivonatokban szeretne visszaadni. Válassza például a megjelenítendő név, az irányítószám lehetőséget.

## <a name="next-steps"></a>További lépések

A jogkivonatok interaktív módon való beszerzésével kapcsolatban további részleteket a következő példában talál: Azure AD B2C alkalmazások MSAL.NET.

| Sample | Platform | Leírás|
|------ | -------- | -----------|
|[Active-Directory-B2C-xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan használható a MSAL.NET a felhasználók hitelesítéséhez Azure AD B2C segítségével, és hogyan férhet hozzá egy webes API-hoz az eredményül kapott jogkivonatokkal.|
