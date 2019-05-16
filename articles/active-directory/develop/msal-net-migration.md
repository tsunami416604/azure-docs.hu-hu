---
title: Áttelepítés az MSAL.NET |} Az Azure
description: Tudnivalók a Microsoft-hitelesítési tár .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) és hogyan telepíthet át az MSAL.NET közötti különbségeket.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 028c7f5d42587a6b2129bba07831b0e799d607f4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544216"
---
# <a name="migrating-applications-to-msalnet"></a>MSAL.NET alkalmazások áttelepítése

Microsoft-hitelesítési tár .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) is szolgálnak jogkivonatokat kérhet az Azure AD és az Azure AD-entitások hitelesítéséhez. Eddig nagyon a legtöbb fejlesztő már használta az Azure AD fejlesztői platform (1.0-s verzió) (munkahelyi és iskolai fiókok esetében) az Azure AD-identitások hitelesítésére az Azure AD Authentication Library (ADAL) használó tokenek kérésével. Most MSAL.NET használatával, hitelesítheti a Microsoft identitás (Azure AD-identitások és a Microsoft-fiókok és közösségi és a helyi fiókok Azure AD B2C segítségével) a Microsoft identity platform végponton keresztül szélesebb készletét használja. 

Ez a cikk ismerteti, hogyan lehet a Microsoft-hitelesítési tár .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) közül választhat, és összehasonlítja a két kódtárakat.  

## <a name="differences-between-adal-and-msal-apps"></a>Adal-t és az MSAL alkalmazások közötti különbségek
A legtöbb esetben érdemes MSAL.NET és a Microsoft identity platform végpont, amely a Microsoft hitelesítési kódtárainak legújabb generációja. MSAL.NET használatával, a felhasználók Azure ad-ben (munkahelyi és iskolai fiókok esetében), a Microsoft (személyes) fiókok (MSA), az alkalmazásokba történő bejelentkezéssel tokenek beszerzése vagy az Azure AD B2C-t. 

Már ismeri az Azure AD fejlesztőknek (1.0-s verzió) végponti (és ADAL.NET), előfordulhat, hogy szeretné-e olvasni [Mi a Microsoft identity platform (2.0-s verzió) végpont?](active-directory-v2-compare.md).

Azonban továbbra is szeretné használni a ADAL.NET, ha az alkalmazásnak a felhasználók a korábbi [Active Directory összevonási szolgáltatások (ADFS)](/windows-server/identity/active-directory-federation-services). További részletekért lásd: [támogatja az AD FS](https://aka.ms/msal-net-adfs-support).

A következő képen látható összefoglaltuk ADAL.NET és MSAL.NET közötti különbségekről ![párhuzamos kód](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-csomagok és a névterek

A felhasznált ADAL.NET a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot. a névtér használatára `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Hozzá kell adnia MSAL.NET használatával a [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet csomagot, majd használja a `Microsoft.Identity.Client` névtér

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrások

ADAL.NET beszerzi a jogkivonatok *erőforrások*, de MSAL.NET beszerzi a jogkivonatok *hatókörök*. Felülbírálások MSAL.NET AcquireToken számos szükséges hatókörök nevű paraméter (`IEnumerable<string> scopes`). Ez a paraméter az egyszerű listája, amelyek a kívánt engedélyekkel és a kért erőforrások deklarálja. Jól ismert hatóköröknek a [Microsoft Graph-hatókörök](/graph/permissions-reference).

Akkor is az MSAL.NET 1.0-s verziójú erőforrások eléréséhez. A részleteket a [egy 1.0-s verziójú alkalmazás hatóköreinek](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Core-osztályok

- ADAL.NET használ [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) , a biztonsági jogkivonat szolgáltatás (STS) vagy az engedélyezési kiszolgáló, egy szolgáltató keresztül létesített kapcsolat reprezentációja. Ezzel szemben, MSAL.NET köré [ügyfélalkalmazások](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Két különálló osztályt biztosít: `PublicClientApplication` és `ConfidentialClientApplication`

- -Jogkivonatok beszerzésének: ADAL.NET és MSAL.NET rendelkezik az ugyanazon hitelesítést hívások (`AcquireTokenAsync` és `AcquireTokenSilentAsync` ADAL.NET, a és `AqquireTokenInteractive` és `AcquireTokenSilent` az MSAL.NET), de különböző paraméterek megadása kötelező. Egy különbség az, hogy az MSAL.NET, nem kell megadni azt a tényt a `ClientID` az alkalmazás minden AcquireTokenXX hívásban. Valójában a `ClientID` készítése során csak egyszer állítsa a (`IPublicClientApplication` vagy `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>Nem IUser IAccount

ADAL.NET felhasználók kezelhetők. Azonban egy felhasználó egy emberi vagy a szoftver-ügynököt, de azt is rendelkeznek/saját/felelős egy vagy több fiókot a Microsoft identity rendszer (több Azure AD-fiókok esetében az Azure AD B2C-vel, személyes Microsoft-fiókokba). 

MSAL.NET 2.x most határozza meg a fiók (a felületen IAccount) fogalmát. Ez használhatatlanná tévő változást biztosítja a megfelelő szemantikát: az a tény, hogy ugyanaz a felhasználó rendelkezhet több fiókot, különböző Azure-ban AD-címtár. MSAL.NET is biztosít a Vendég-forgatókönyveket, és ez jobb információt, otthoni fiókadatok biztosított.

IUser és IAccount közti különbségekkel kapcsolatos további információkért lásd: [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Kivételek

#### <a name="interaction-required-exceptions"></a>Beavatkozás szükséges kivételek

MSAL.NET több explicit kivételek rendelkezik. Például, amikor beavatkozás nélküli hitelesítés nem sikerül, az adal-t az eljárást, hogy a rendszer a findlogin, és keresse meg a `user_interaction_required` kód chyby:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

A részleteket a [az ajánlott mintája a jogkivonat-beszerzési](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) ADAL.NET az

MSAL.NET használatával, akkor tényleges `MsalUiRequiredException` leírtak szerint [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Kezelési jogcím challenge-kivételek

ADAL.NET, a jogcím-challenge, kivételek kezelése a következő módon:

- `AdalClaimChallengeException` Kivétel (a származtatásának `AdalServiceException`) a szolgáltatás által kiváltott, abban az esetben, ha egy erőforrást igényel további jogcímeket a felhasználó (például két-tényezők hitelesítés). A `Claims` tag néhány JSON-töredék a jogcímeket, amely várhatóan tartalmazza.
- Továbbra is a ADAL.NET, a nyilvános ehhez a kivételhez fogadása kell hívja a `AcquireTokenInteractive` bírálja felül, a jogcímek paraméter kellene. Ez a felülbírálás, `AcquireTokenInteractive` meg sem próbál eléri a gyorsítótár, mivel nem szükséges. A hiba oka, hogy a jogkivonat a gyorsítótárban nem rendelkezik a megfelelő jogcím (ellenkező esetben egy `AdalClaimChallengeException` lenne nem rendelkezik fel). Ezért hiba esetén nem kell, és tekintse meg a gyorsítótár. Vegye figyelembe, hogy a `ClaimChallengeException` észlelések érkezhetnek a WebAPI OBO, ezzel a, mivel a `AcquireTokenInteractive` akkor kell meghívni a webes API meghívása nyilvános ügyfél-alkalmazásokban.
- További részletek többek között a mintákat: kezelési [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

MSAL.NET, a jogcím-challenge, kivételek kezelése a következő módon:

- A `Claims` az illesztett a `MsalServiceException`.
- Van egy `.WithClaim(claims)` módszer, amelyek használhatók a `AcquireTokenInteractive` builder. 

### <a name="supported-grants"></a>Támogatott biztosít

Nem minden biztosít még támogatottak az MSAL.NET és a v2.0-végpontra. Az alábbiakban a ADAL.NET és MSAL összehasonlítása. NET biztosít a támogatott.

#### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

Az alábbiakban a támogatott asztali és mobil alkalmazások ADAL.NET és MSAL.NET biztosít

Hozzáférés | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktív | [Interaktív hitelesítés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktív módon az MSAL.NET-jogkivonatok beszerzésének](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrált Windows-hitelesítés | [Integrált (Kerberos) Windows-hitelesítés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication)
Felhasználónév / jelszó | [A felhasználónév és jelszó-jogkivonatok beszerzésének](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Felhasználónév-jelszó-hitelesítés](msal-authentication-flows.md#usernamepassword)
Eszközkód folyamata | [Eszközprofil böngészők nem eszközökön](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Eszköz kódfolyamat](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

Az alábbiakban a biztosít a webalkalmazások, a webes API-k és a démon alkalmazások ADAL.NET és MSAL.NET támogatja:

Az alkalmazás típusa | Hozzáférés | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web App, Web API, daemon | Ügyfél-hitelesítő adatok | [Ügyfél-hitelesítő adatok elkezdenek beérkezni a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Ügyfél-hitelesítő adatok elkezdenek beérkezni az MSAL.NET](msal-authentication-flows.md#client-credentials))
Webes API | Meghatalmazó: | [Szolgáltatások közötti hívások ADAL.NET az a felhasználó nevében](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Az MSAL.NET nevében](msal-authentication-flows.md#on-behalf-of)
Web App | Auth Code | [Az engedélyezési kódokat a webalkalmazások ADAL.NET-jogkivonatok beszerzésének](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Az engedélyezési kódokat az A MSAL.NET web Apps-jogkivonatok beszerzésének](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cache megőrzése

ADAL.NET lehetővé teszi, hogy terjessze ki a `TokenCache` osztályba a kívánt megőrzéssel funkciók platformokon nélkül egy biztonságos tárolási (a .NET-keretrendszer és a .NET core) használatával a `BeforeAccess`, és `BeforeWrite` módszereket. További információkért lásd: [ADAL.NET a Token gyorsítótár szerializálási](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET lehetővé teszi a jogkivonatok gyorsítótárát egy lezárt osztály, akkor kiterjesztheti eltávolítása. Ezért a token cache megőrzése megvalósítását egy segítőosztály, amellyel kommunikál a lezárt tokengyorsítótárral formájában kell lennie. Ennek az interakciónak leírt [Token gyorsítótár szerializálása az MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>A közös hatóság jelzés

Az 1.0-s verziójú, ha a https://login.microsoftonline.com/common szolgáltatót, hogy engedélyezi-e felhasználók számára történő bejelentkezést bármely AAD-fiók (a bármely szervezet). Lásd: [ADAL.NET szolgáltató-ellenőrzés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Ha használja a https://login.microsoftonline.com/common a 2.0-s szolgáltatót, hogy segítségével a felhasználók jelentkezzen be minden AAD szervezet vagy egy személyes Microsoft-fiókkal (MSA). Az MSAL.NET, ha szeretné korlátozni a bejelentkezési bármelyik AAD-fiókjába (viselkedést, a ADAL.NET), szüksége https://login.microsoftonline.com/organizations. További információkért lásd: a `authority` paraméter [ügyfélalkalmazás nyilvános](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>1.0-s és 2.0-s verziójú jogkivonatok

Nincsenek jogkivonatok két verziója:
- 1.0-s verziójú jogkivonatok
- 2.0-s verziójú jogkivonatok 

Az 1.0-s verziójú végpont (adal-t használják) csak 1.0-s verziójú jogkivonatokat bocsát ki.

Azonban a v2.0-végpont (MSAL által használt) bocsát ki a jogkivonatot, amely a webes API verziója. Az alkalmazásjegyzékben, a webes API-tulajdonságra lehetővé teszi a fejlesztők számára, hogy válassza ki, melyik verzióját jogkivonat elfogadható. Lásd: `accessTokenAcceptedVersion` a a [alkalmazásjegyzék](reference-app-manifest.md) referenciák dokumentációiba.

1.0-s és 2.0-s verziójú jogkivonatok kapcsolatos további információkért lásd: [Azure Active Directory hozzáférési jogkivonatok](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Hatókörök webes API 1.0-s verziójú jogkivonatok elfogadása

Az OAuth2-engedélyek engedélyhatókörök, amely 1.0-s verziója a webes API (erőforrás) alkalmazás közzéteszi az ügyfélalkalmazások számára. Ezek engedélyhatókörök hozzájárulási során adható az ügyfélalkalmazások számára. Foglalkozó részében olvashat az oauth2Permissions [Azure Active Directory alkalmazásjegyzékének](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Hatókörök kérelmezzenek hozzáférést a megadott OAuth2 engedélyek 1.0-s verziójú alkalmazások

Hogy szeretné-e szerzi be a jogkivonatokat meghatározott hatókörök 1.0-s verziójú alkalmazások (például az AAD graph, amely https://graph.windows.net), létre kell `scopes` elkülönített változó összefűzésével előállítjuk a kívánt erőforrás-azonosítója egy kívánt OAuth2 engedéllyel az adott erőforráshoz.

Például a hozzáférés be nevét a felhasználó egy webes API-alkalmazás Alkalmazásazonosító URI-ja v1.0 `ResourceId`, érdemes használni:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Ha szeretné olvasása és írása az MSAL.NET Azure Active Directoryval az AAD graph API használatával (https://graph.windows.net/), például a hatókörök listájának az alábbi kódrészletben lenne létrehozásához:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Figyelmeztetés: Kell egy vagy két perjeleket a hatókör egy 1.0-s verziójú webes API-hoz tartozó

Ha szeretne írni az Azure Resource Manager API-nak megfelelő hatóköre (https://management.core.windows.net/), meg kell kérnie a következő hatókörrel (Megjegyzés: a két perjeleket) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Ennek oka, hogy a Resource Manager API-t vár a célközönség jogcím perjellel (`aud`), és azután ott van az API-név a hatókörből külön perjellel.

Az Azure AD által használt logikai a következő:
- Adal-t (1.0-s verzió) végpont egy 1.0-s verziójú hozzáférési jogkivonattal (az egyetlen lehetséges), aud erőforrás =
- A hozzáférési jogkivonat kérése egy erőforrást, 2.0-s verziójú jogkivonatok, aud elfogadásával MSAL (v2.0-végpont) erőforrást =. Alkalmazásazonosító
- Az MSAL (v2.0-végpontra) egy hozzáférési jogkivonatot kér egy erőforrást egy 1.0-s verziójú hozzáférési jogkivonat (amely a fenti eset) elfogadása az Azure AD a kért hatókörhöz a kívánt célközönség elemzi a minden, a legutóbbi / előtti és használhatná az erőforrás-azonosítója alapján. Ezért ha https://database.windows.net közönség vár "https://database.windows.net/", szüksége lesz egy hatókörének kérése https://database.windows.net//.default. Lásd: # is kiadhatnak[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Erőforrás URL-cím, záró perjellel hiányzik, amely az sql-hitelesítési hiba #747 okozza


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókörök igényelhet hozzáférést egy 1.0-s verziójú alkalmazás minden engedély

Például ha azt szeretné, a statikus hatókörök 1.0-s verziójú alkalmazások jogkivonat-beszerzési, egy kell használni

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Hatókörök kérése esetén ügyfél hitelesítőadat-folyamat / démon alkalmazás

Ügyfél-hitelesítő adat folyamatát, esetén a hatókör át is lenne `/.default`. Azonban igen, az Azure AD: "minden az alkalmazásszintű engedélyek, amelyek a rendszergazda az alkalmazás regisztrálása a hozzájárult.

## <a name="adal-to-msal-migration"></a>Az ADAL-ra MSAL áttelepítése

A ADAL.NET v2-ben. X-, a frissítési jogkivonatok adatai voltak titkosítatlanok, így ezek a jogkivonatok használata körül megoldások fejlesztése azokat a gyorsítótárazást, és használja a `AcquireTokenByRefreshToken` 2.x-es ADAL által biztosított metódusokkal. Ezek a megoldások némelyike is esetekben használatos, például:
* Hosszú ideig futó szolgáltatások, beleértve az irányítópultok frissítése a felhasználók nevében, mivel már csatlakoztatva vannak a felhasználók műveleteket hajthatja végre. 
* A WebFarm forgatókönyvek engedélyezése az ügyfél számára a RT használata a web Service (gyorsítótárazása történik, ügyféloldali, titkosított cookie-k és nem kiszolgáló oldali)

Ez nem a helyzet az MSAL.NET, azonban nem okból rendszerbetöltést végrehajtani a frissítés hosszabb ajánlott jogkivonatok, biztonsági okokból ily módon. Ez megnehezítik migrálása a MSAL az API-ként 3.x nem teszik lehetővé a korábban beszerzett frissítési biztonsági jogkivonat adja át. 

Szerencsére a MSAL.NET most már rendelkezik egy API-t, amely lehetővé teszi, hogy a korábbi frissítési biztonsági jogkivonat, telepítse a `IConfidentialClientApplication` 

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Ezzel a módszerrel megadhatja a korábban használt frissítési jogkivonatot, és bármely hatókörök (erőforrások), amennyit csak szeretne. A frissítési jogkivonatot fog kicserélt egy új, és az alkalmazásba a gyorsítótárba.  

Ez a módszer rendeltetésszerű célokra, amelyek nem jellemzőek nem érhető el azonnal a a `IConfidentialClientApplication` anélkül, hogy leadó `IByRefreshToken`.

Ez a kódrészlet egy bizalmas ügyfélalkalmazás áttelepítése kód látható. `GetCachedRefreshTokenForSignedInUser` az alkalmazás, amely kihasználja a 2.x-es ADAL korábbi verzióját az egyes storage-ban tárolt a frissítési jogkivonatot beolvasni. `GetTokenCacheForSignedInUser` a bejelentkezett felhasználó a gyorsítótárban deserializes, (a bizalmas ügyfélalkalmazások kell rendelkeznie egy gyorsítótár felhasználónként).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Egy hozzáférési jogkivonatot, és a AuthenticationResult adja vissza, miközben az új frissítési jogkivonat a gyorsítótárban tárolt azonosító jogkivonat jelenik meg.

Is használhatja ezt a módszert a különböző integrációs forgatókönyvek, ahol egy érhető el frissítési jogkivonat rendelkezik.

## <a name="next-steps"></a>További lépések

További információ a hatókörök annak [hatókörök, engedélyek és jóváhagyás az a Microsoft identity platform végpont](v2-permissions-and-consent.md)