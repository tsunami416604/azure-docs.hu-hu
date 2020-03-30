---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az Azure AD B2C használatával kapcsolatos konkrét szempontokat a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
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
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262814"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>A MSAL.NET használatával közösségi identitással rendelkező felhasználók jelentkeznek be

A MSAL.NET segítségével bejelentkezhet a közösségi identitással rendelkező felhasználókba az [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)használatával. Az Azure AD B2C a szabályzatok fogalmára épül. A MSAL.NET a házirend megadása egy hatóság biztosítását jelenti.

- A nyilvános ügyfélalkalmazás példányosításakor meg kell adnia a házirendet a hatóságban.
- Ha házirendet szeretne alkalmazni, meg kell hívnia `AcquireTokenInteractive` egy `authority` paraméter t.

Ez az oldal az MSAL 3.x. Ha érdekli az MSAL 2.x, olvassa el [az Azure AD B2C sajátosságait az MSAL 2.x.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Az Azure AD B2C-bérlő és -szabályzat jogosultsága

A használatjoga `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` az, ha:

- `azureADB2CHostname`az Azure AD B2C-bérlő és az állomás `{your-tenant-name}.b2clogin.com`neve (például ),
- `tenant`az Azure AD B2C-bérlő teljes neve `{your-tenant-name}.onmicrosoft.com`(például ) vagy a bérlő GUID azonosítója, 
- `policyName`az alkalmazandó házirend vagy felhasználói folyamat neve (például "b2c_1_susi" a regisztrációhoz/bejelentkezéshez).

Az Azure AD B2C-hatóságokkal kapcsolatos további információkért tekintse meg ezt a [dokumentációt.](/azure/active-directory-b2c/b2clogin)

## <a name="instantiating-the-application"></a>Az alkalmazás példányosítása

Az alkalmazás létrehozásakor meg kell adnia a felhatalmazást.

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

## <a name="acquire-a-token-to-apply-a-policy"></a>Jogkivonat beszerzése a házirend alkalmazásához

Egy nyilvános ügyfélalkalmazásban egy Azure AD B2C-védelemmel ellátott API-hoz egy jogkivonat beszerzése megköveteli a felülbírálások használatát egy hatósággal:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

a következőre:

- `policy`az előző karakterláncok egyike (például). `PolicySignUpSignIn`
- `ParentActivityOrWindow`androidos (a tevékenység) és nem kötelező más platformokon, amelyek támogatják a szülő felhasználói felületet, például a Windows ablakait és az uIViewController t iOS rendszerben. További információ [itt a felhasználói felület párbeszédablakán.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`egy olyan módszer, amely egy adott házirend hez talál fiókot. Példa:

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

A házirend vagy a felhasználói folyamat alkalmazása (például annak engedélyezése, hogy a végfelhasználó `AcquireTokenInteractive`szerkesztse a profilját, vagy alaphelyzetbe állítsa jelszavát) jelenleg a hívással történik. A két szabályzat esetén nem használja a visszaadott jogkivonat / hitelesítés eredménye.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Az EditProfile és a ResetPassword házirendek különleges esete

Ha olyan felhasználói élményt szeretne biztosítani, amelyben a végfelhasználók egy közösségi identitással jelentkeznek be, majd szerkesztik a profiljukat, az Azure AD B2C profilszerkesztési szabályzatot szeretné alkalmazni. Ennek módja az, hogy `AcquireTokenInteractive` felhívja az adott házirend adott hatóságát, és egy kérdés készletet a `Prompt.NoPrompt` fiókkiválasztási párbeszédpanel megjelenítésének megakadályozására (mivel a felhasználó már be van jelentkezve, és aktív cookie-munkamenettel rendelkezik).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Erőforrás-tulajdonosi jelszó hitelesítő adatai (ROPC) az Azure AD B2C-vel
A ROPC-folyamattal kapcsolatos további részletekért tekintse meg ezt a [dokumentációt.](v2-oauth-ropc.md)

Ez a folyamat **nem ajánlott,** mert az alkalmazás kéri a felhasználó jelszavát nem biztonságos. A problémáról további információt [ebben a cikkben](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)talál. 

Segítségével felhasználónév / jelszó, akkor feladja számos dolgot:
- A modern identitás alaptételei: a jelszót kihalják, visszajátsszák. Mert van egy elképzelésünk a megosztási titokról, amit el lehet fogni. Ez nem kompatibilis a jelszó nélküli.
- Azok a felhasználók, akiknek többdíjú előfizetést kell tenniük, nem tudnak bejelentkezni (mivel nincs interakció).
- A felhasználók nem tudnak egyszeri bejelentkezést végezni.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>A ROPC-folyamat konfigurálása az Azure AD B2C-ben
Az Azure AD B2C-bérlőben hozzon létre egy új felhasználói folyamatot, és válassza **a Bejelentkezés a ROPC használatával lehetőséget.** Ez lehetővé teszi a ROPC-házirendet a bérlő számára. További részletekért [olvassa el az erőforrás-tulajdonos jelszó-hitelesítő adatok folyamatának konfigurálása](/azure/active-directory-b2c/configure-ropc) című témakört.

`IPublicClientApplication`tartalmaz egy módszert:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ez a módszer a következő paramétereket veszi figyelembe:
- A hozzáférési jogkivonatot kérő *hatókörök.*
- *Felhasználónév*.
- A felhasználó *SecureString-jelszava.*

Ne felejtse el használni a ROPC-házirendet tartalmazó jogosultságot.

### <a name="limitations-of-the-ropc-flow"></a>A ROPC-áramlás korlátai
 - A ROPC-folyamat **csak helyi fiókok esetén működik** (ahol e-mailben vagy felhasználónévvel regisztrál az Azure AD B2C-vel). Ez a folyamat nem működik, ha az Azure AD B2C (Facebook, Google stb.) által támogatott identitásszolgáltatók bármelyikének összevannak kapva.

## <a name="google-auth-and-embedded-webview"></a>Google Auth és beágyazott webview

Ha Ön Azure AD B2C fejlesztő, aki a Google-t identitásszolgáltatóként használja, akkor a rendszerböngésző használatát javasoljuk, mivel a Google nem engedélyezi a [beágyazott webnézetekből történő hitelesítést.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) Jelenleg `login.microsoftonline.com` egy megbízható hatóság a Google.Currently, is a trusted authority with Google. Ennek a jogosultságnak a használata a beágyazott webmegtekintéssel fog működni. A `b2clogin.com` használat azonban nem megbízható jogosultság a Google-nál, így a felhasználók nem tudják hitelesíteni magukat.

A probléma változása esetén frissítjük a [problémát.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Gyorsítótárazás az Azure AD B2C-vel MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Ismert probléma az Azure AD B2C-vel

MSAL.Net támogatja a [token gyorsítótárat](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). A token gyorsítótárazási kulcs az identitásszolgáltató által visszaadott jogcímek alapján. Jelenleg MSAL.Net két jogcímre van szüksége egy token gyorsítótárkulcs létrehozásához:  
- `tid`amely az Azure AD-bérlőazonosító, és 
- `preferred_username` 

Mindkét jogcím hiányzik az Azure AD B2C-forgatókönyvek számos. 

Az ügyfél hatása az, hogy amikor megpróbálja megjeleníteni a felhasználónév mező, ön szerzés " hiányzó -ból jogkivonat válasz" mint a érték? Ha igen, ennek az az oka, hogy az Azure AD B2C nem ad vissza értéket az IdToken a preferred_username a közösségi fiókok és a külső identitásszolgáltatók (IdPs) korlátozásai miatt. Az Azure AD értéket ad vissza preferred_username mert tudja, ki a felhasználó, de az Azure AD B2C, mert a felhasználó bejelentkezhet egy helyi fiókkal, Facebook, Google, GitHub, stb nincs egységes érték az Azure AD B2C használni preferred_username. Az MSAL a gyorsítótár-kompatibilitás aDal-lal való kompatibilitásának feloldásához úgy döntöttünk, hogy a "Hiányzik a jogkivonat-válaszból" kifejezést használjuk a végén az Azure AD B2C-fiókok kezelése során, amikor az IdToken semmit sem ad vissza preferred_username. Az MSAL-nak értéket kell visszaadnia preferred_username a gyorsítótár-kompatibilitás fenntartása érdekében a könyvtárak között.

### <a name="workarounds"></a>Kerülő megoldások

#### <a name="mitigation-for-the-missing-tenant-id"></a>A hiányzó bérlőazonosító enyhítése

A javasolt megoldás az, hogy a [házirend általi gyorsítótárazást](#acquire-a-token-to-apply-a-policy)

Másik lehetőségként használhatja `tid` a jogcímet, ha a [B2C egyéni házirendeket](https://aka.ms/ief)használja, mivel lehetővé teszi további jogcímek visszaadását az alkalmazásnak. További információ a [jogcímek átalakításáról](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>A "Hiányzik a jogkivonat-válaszból" kockázatcsökkentés
Az egyik lehetőség a "name" jogcím használata az előnyben részesített felhasználónévként. A folyamat szerepel ebben a [B2C doc](../../active-directory-b2c/user-flow-overview.md) -> "A Visszakövetelési jogcím oszlopban válassza ki a kívánt jogcímeket az alkalmazásnak visszaküldött engedélyezési jogkivonatokban egy sikeres profilszerkesztési élmény után. Válassza például a Megjelenítendő név, Irányítószám lehetőséget."

## <a name="next-steps"></a>További lépések 

További részletek a jogkivonatok interaktív beszerzéséről MSAL.NET Az Azure AD B2C-alkalmazások az alábbi mintában található.

| Sample | Platform | Leírás|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-natív](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Egy egyszerű Xamarin Forms alkalmazás bemutatja, hogyan használhatja a MSAL.NET a felhasználók hitelesítésére az Azure AD B2C-n keresztül, és az eredményül kapott jogkivonatokkal való hozzáférés egy webes API-t.|
