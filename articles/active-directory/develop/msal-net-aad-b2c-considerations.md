---
title: Az Azure AD B2C (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Figyelembe kell venni bizonyos ismerje meg az Azure AD B2C-vel használata a Microsoft-hitelesítési tár .NET (MSAL.NET) esetén.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c608518a9eb80d807297f010778ae452c0f61f5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075775"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>A közösségi identitású felhasználók az MSAL.NET használatával

MSAL.NET használatával jelentkezzen be a közösségi identitású felhasználók [Azure Active Directory B2C (Azure AD B2C-vel)](https://aka.ms/aadb2c). Az Azure AD B2C házirendek fogalma köré épül. Az MSAL.NET házirendet a rendszer lefordítja arra-szolgáltató kezeléséről.

- Ha a nyilvános ügyfélalkalmazás elindításához példányosítania, adja meg a házirend-szolgáltató kell.
- Ha meg szeretné alkalmazni a szabályzatot, meg kell hívnia a felülbírálás `AcquireTokenInteractive` tartalmazó egy `authority` paraméter.

Az MSAL van ezen a lapon 3.x. Ha érdekli az MSAL 2.x verzióját, lásd: [MSAL az Azure AD B2C-vel tulajdonságairól 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Egy Azure AD B2C-bérlő és a házirend hatóság

A használandó hatóság `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` ahol:

- `tenant` az Azure AD B2C bérlő neve 
- `policyName` a szabályzat vonatkozik (például "b2c_1_susi" sign-a vagy a regisztráláshoz a) neve.

Az aktuális útmutatás az Azure AD B2C-ből a használandó `b2clogin.com` szolgáltatóként. Például: `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. További információkért lásd: Ez [dokumentáció](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Az alkalmazás hárítható el

Az alkalmazás létrehozását, meg kell adnia a szolgáltatót.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>A szabályzat alkalmazásához jogkivonat beszerzése

Egy token beszerzése az Azure AD B2C-t a védett API-ja nyilvános ügyfélalkalmazás előírja, hogy a felülbírálás-szolgáltató:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

a következőre:

- `policy` folyamatban van egy korábbi karakterlánc (például `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` a fiók megkeresi egy adott házirend módszer. Példa:

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

(Például hogy a felhasználó szerkesztheti a profilját, vagy új jelszót kérnek) házirend alkalmazása jelenleg kész meghívásával `AcquireTokenInteractive`. Ezek a házirendek két esetén a visszaadott jogkivonat ne használjon / hitelesítés eredménye.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Különleges eset EditProfile és ResetPassword házirendek

Ha azt szeretné, ahol a végfelhasználók jelentkezzen be egy közösségi identitást biztosít, és ezután szerkesztheti a profilját az Azure AD B2C-vel EditProfile házirendet alkalmazni szeretné. Ehhez a úgy, hogy a hívó `AcquireTokenInteractive` az adott házirendnek és a egy kérdés beállítása az adott szolgáltató `Prompt.NoPrompt` elkerülése érdekében a fiók kiválasztása párbeszédpanelen megjelenő (a felhasználó már bejelentkezett)

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Erőforrás tulajdonosának jelszavas hitelesítő (ROPC) adatai az Azure AD B2C-vel
A ROPC folyamatot a további részletekért tekintse meg a [dokumentáció](v2-oauth-ropc.md).

Ez a folyamat **nem ajánlott** mivel az alkalmazás a felhasználó megkérése a jelszavát nem biztonságos. A problémáról további információk: [Ez a cikk](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Felhasználónév/jelszó használatával, vannak megadva felfelé több minden:
- Alapszintű bérlők modern identitás: jelszó fogott lekérdezi, játssza vissza. Mert a fogalom, amely megszerezhetik megosztás titkos kód. Ez az beállításának kompatibilis.
- Felhasználók, akik kell tennie a többtényezős hitelesítés nem lehet bejelentkezni (mivel nem lett a beavatkozás nélküli).
- Felhasználók nem tudják az egyszeri bejelentkezés.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>A ROPC folyamat konfigurálása az Azure AD B2C-vel
Az Azure AD B2C-bérlőben, hozzon létre egy új felhasználói folyamat, és válassza **jelentkezzen be a ROPC**. Ez lehetővé teszi a ROPC házirend-bérlője számára. Lásd: [konfigurálása az erőforrás tulajdonosának jelszavas hitelesítő adatai flow](/azure/active-directory-b2c/configure-ropc) további részletekért.

`IPublicClientApplication` egy metódust tartalmaz:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ez a módszer paraméterekként fogadja:
- A *hatókörök* hozzáférési jogkivonat kérése.
- A *felhasználónév*.
- A SecureString *jelszó* a felhasználó számára.

Fontos, hogy a szolgáltató, amely tartalmazza a ROPC házirendet használja.

### <a name="limitations-of-the-ropc-flow"></a>A ROPC flow korlátozásai
 - A ROPC folyamat **csak a helyi fiókok esetében működik** (kell regisztrálnia az Azure AD B2C egy e-mail vagy felhasználónév). Ez a folyamat nem működik, ha az Azure AD B2C által támogatott Identitásszolgáltatók valamelyik összevonása (Facebook, Google, stb.).
 - Jelenleg nincs **id_token nem adott vissza az Azure AD B2C-ből** az MSAL ROPC folyamat végrehajtása során. Ez azt jelenti, egy fiók objektum nem hozható létre, így a gyorsítótárban lesz nincs fiókja, és a felhasználói. A AcquireTokenSilent folyamat nem fog működni ebben a forgatókönyvben. Azonban ROPC nem jelenik meg a felhasználói Felületet, így nem nincs hatással a felhasználói élményt.

## <a name="google-auth-and-embedded-webview"></a>Google-hitelesítés és a beágyazott WebView-t

Ha Ön egy Azure AD B2C-t fejlesztő Google használata Identitásszolgáltatóként, azt recommand rendszer böngészőt használ, nem engedélyezi a Google [beágyazott webnézeteket hitelesítést](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Jelenleg `login.microsoftonline.com` megbízható hatóság a Google-lel. Ez a szolgáltató használatával a beágyazott WebView-t fog működni. Azonban használatával `b2clogin.com` nem megbízható hatóság a Google-lel, így a felhasználók nem fognak tudni hitelesíteni.

A wikin, és a egy frissítést biztosít [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) dolgot módosítjuk.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Az Azure AD B2C az MSAL.Net gyorsítótárazás 

### <a name="known-issue-with-azure-ad-b2c"></a>Ismert probléma az Azure AD B2C-vel

Támogatja az MSAL.Net egy [jogkivonat-gyorsítótár](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). A jogkivonat kulcs gyorsítótárazását a jogcímek, az identitásszolgáltató által visszaadott alapul. Jelenleg az MSAL.Net kell két jogcímek jogkivonat gyorsítótár kulcsának hozhat létre:  
- `tid` az Azure AD-bérlő azonosítója, azaz és 
- `preferred_username` 

Mindkét ezeket a jogcímeket hiányoznak számos, az Azure AD B2C-forgatókönyveket. 

Az ügyfél hatás az, hogy a felhasználónév mező megjelenítése során kapott "A jogkivonatot a válaszból hiányzik" értéket? Ha igen, ennek az az oka az Azure AD B2C-vel nesmí vracet hodnotu a számára a preferred_username a IdToken a közösségi fiókok és a külső identitásszolgáltató (IDP) vonatkozó korlátozások miatt. Az Azure AD egy preferred_username értékét adja vissza, mert a felhasználó, akik tudni fogja, de az Azure AD B2C-vel, mert a felhasználó bejelentkezhet egy helyi fiók, Facebook, Google, a GitHub-stb. hiba nem konzisztens az Azure AD B2C preferred_username használandó értéket. Az ADAL-gyorsítótár kompatibilitási bevezetéséről MSAL feloldásához döntöttünk során kezelése az Azure AD B2C fiókkal rendelkező, amikor a IdToken ad vissza semmit a preferred_username "A jogkivonatot a válaszból hiányzik" használandó található a rendszerünkben. Az MSAL való gyorsítótár kompatibilitás megőrzése érdekében a szalagtárak különböző preferred_username értéket kell visszaadnia.

### <a name="workarounds"></a>Megkerülő megoldások

#### <a name="mitigation-for-the-missing-tenant-id"></a>A bérlői azonosító mérséklése.

A javasolt áthidaló megoldás a [gyorsítótárazási házirend](#acquire-a-token-to-apply-a-policy)

Másik lehetőségként használhatja a `tid` jogcímet, ha használja a [B2C-vel egyéni szabályzatok](https://aka.ms/ief), mert azt lehetővé teszi az alkalmazás további jogcímeket adja vissza. Tudjon meg többet a [jogcímek átalakítása](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>"A jogkivonatot a válaszból hiányzik" megoldás
Az egyik lehetőség, hogy a "name" jogcímet a előnyben részesített felhasználóneveként. Ez a folyamat említett [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> "a visszatérési jogcím oszlop kiválasztása a visszaküldött hitelesítő jogkivonatokban a sikeres Profilszerkesztési művelet után az alkalmazásnak szeretne jogcímeket. Jelölje be például megjelenítendő név, postai irányítószám."

## <a name="next-steps"></a>További lépések 

Interaktív módon az Azure AD B2C-alkalmazást az MSAL.NET-jogkivonatok beszerzésének további információt a következő minta szerepelnek.

| Sample | Platform | Leírás|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Android, Xamarin az UWP | Egy egyszerű Xamarin Forms alkalmazás amely azt mutatja be az MSAL.NET használatával hitelesíteni a felhasználókat az Azure AD B2C és hozzáférni egy webes API-hoz az eredményül kapott jogkivonatokkal.|
