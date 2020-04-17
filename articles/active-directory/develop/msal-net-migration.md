---
title: Áttérés MSAL.NET
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) közötti különbségeket, valamint azt, hogy miként telepíthető át MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f389943d284c573312473f426048f8aadb79088e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533972"
---
# <a name="migrating-applications-to-msalnet"></a>Alkalmazások áttelepítése MSAL.NET

A .NET (MSAL.NET) Microsoft Authentication Library és az Azure AD Authentication Library for .NET (ADAL.NET) az Azure AD-entitások hitelesítésére és az Azure AD-től származó tokenek igénylésére szolgál. Eddig a legtöbb fejlesztő az Azure AD for developers platformmal (1.0-s) együttműködve hitelesítette az Azure AD-identitásokat (munkahelyi és iskolai fiókokat) az Azure AD hitelesítési könyvtár (ADAL) használatával történő jogkivonatok kérésével. Az MSAL használata:

- a Microsoft-identitások szélesebb körét (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi fiókok az Azure AD B2C-n keresztül) hitelesítheti, mivel a Microsoft identity platform végpontját használja,
- a felhasználók a legjobb egyszeri bejelentkezési élményt kapják.
- az alkalmazás engedélyezheti a növekményes hozzájárulást, és a feltételes hozzáférés támogatása egyszerűbb
- profitálaz innovációból.

**MSAL.NET most már az ajánlott hitelesítési könyvtár a Microsoft identity platformmal való használatra.** A ADAL.NET nem valósítanak meg új funkciókat. Az erőfeszítések az MSAL javítására összpontosítanak.

Ez a cikk a Microsoft .NET (MSAL.NET) és az Azure AD authentication library for .NET (ADAL.NET) közötti különbségeket ismerteti, és segít az MSAL-ra való áttelepítésben.

## <a name="differences-between-adal-and-msal-apps"></a>Az ADAL és az MSAL alkalmazások közötti különbségek

A legtöbb esetben MSAL.NET és a Microsoft identity platform végpontját szeretné használni, amely a Microsoft hitelesítési kódtárai legújabb generációja. A MSAL.NET használatával beszerezheti a jogkivonatokat az Alkalmazásba Azure AD -vel (munkahelyi és iskolai fiókokkal), microsoftos (személyes) fiókokkal (MSA) vagy Azure AD B2C-vel bejelentkező felhasználók számára.

Ha már ismeri az Azure AD fejlesztőknek (v1.0) végpont (és ADAL.NET), érdemes lehet olvasni [Mi a különbség a Microsoft identity platform (v2.0) végpont?](active-directory-v2-compare.md)

Azonban továbbra is használnia kell ADAL.NET, ha az alkalmazásnak be kell jelentkeznie az [Active Directory összevonási szolgáltatások (ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival rendelkező felhasználókba. További információt az [ADFS-támogatás című témakörben talál.](https://aka.ms/msal-net-adfs-support)

Az alábbi kép összefoglalja a ADAL.NET és ![MSAL.NET egymás melletti kód közötti különbségeket](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet csomagok és névterek

ADAL.NET a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet csomagból származik. a használandó névtér `Microsoft.IdentityModel.Clients.ActiveDirectory`.

A MSAL.NET használatához hozzá kell adnia a [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) `Microsoft.Identity.Client` NuGet csomagot, és használnia kell a névteret

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrások

ADAL.NET szerez tokeneket *az erőforrásokhoz*, de MSAL.NET a *hatóköröktokeneket.* A MSAL.NET AcquireToken felülbírálások száma hatókör(`IEnumerable<string> scopes`nevű paramétert( igényel. Ez a paraméter a kért engedélyeket és erőforrásokat deklaráló karakterláncok egyszerű listája. A jól ismert hatókörök a [Microsoft Graph hatókörei](/graph/permissions-reference).

Az is lehetséges, MSAL.NET a hozzáférést a v1.0-s erőforrások eléréséhez. Lásd a [részleteket a Hatókör egy v1.0-s alkalmazáshoz.](#scopes-for-a-web-api-accepting-v10-tokens)

### <a name="core-classes"></a>Alaposztályok

- ADAL.NET [a Hitelesítéskörnyezetet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) használja a biztonsági jogkivonat-szolgáltatással (STS) vagy az engedélyezési kiszolgálóval létesített kapcsolat egy hatóságon keresztül. Éppen ellenkezőleg, MSAL.NET köré [ügyfélalkalmazások](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Két külön osztályt `PublicClientApplication` biztosít: és`ConfidentialClientApplication`

- Tokenek beolvasása: ADAL.NET és MSAL.NET`AcquireTokenAsync` ugyanazokkal a hitelesítési hívások (és `AcquireTokenSilentAsync` a ADAL.NET, és `AcquireTokenInteractive` `AcquireTokenSilent` MSAL.NET), de különböző paraméterek szükségesek. Az egyik különbség az a tény, hogy MSAL.NET, `ClientID` akkor már nem kell átadni az alkalmazás minden AcquireTokenXX hívás. Sőt, `ClientID` az van beállítva,`IPublicClientApplication` csak `IConfidentialClientApplication`egyszer, amikor az épület a ( vagy ).

### <a name="iaccount-not-iuser"></a>IAccount nem IUser

ADAL.NET manipulált felhasználók. Azonban a felhasználó egy emberi vagy szoftverügynök, de rendelkezhet/saját/felelős egy vagy több fiók a Microsoft identitásrendszer (több Azure AD-fiókok, Azure AD B2C, Microsoft személyes fiókok).

MSAL.NET 2.x mostantól meghatározza a Fiók fogalmát (az IAccount felületen keresztül). Ez a törésváltozás biztosítja a megfelelő szemantikát: az a tény, hogy ugyanaz a felhasználó több fiókkal is rendelkezhet, különböző Azure AD-könyvtárakban. A MSAL.NET is jobb információkat nyújt a vendégesetekben, mivel az otthoni fiók adatai is rendelkezésre állnak.

Az IUser és az IAccount közötti különbségekről [a MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Kivételek

#### <a name="interaction-required-exceptions"></a>Beavatkozásszükséges kivételek

MSAL.NET kifejezettebb kivételekkel rendelkezik. Ha például a csendes hitelesítés sikertelen az ADAL-ban, `user_interaction_required` az eljárás a kivétel felkelője és a hibakód megkeresése:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Lásd a részleteket [az ajánlott minta beszerezni egy token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) ADAL.NET

A MSAL.NET használatával `MsalUiRequiredException` az [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)című részben leírtak szerint kapja el a fogást.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Jogcímkihívás-kivételek kezelése

A ADAL.NET a jogcímkihívás-kivételeket a következőképpen kezeljük:

- `AdalClaimChallengeException`a szolgáltatás által kiváltott `AdalServiceException`kivétel (abból származik), ha egy erőforrás nak több igényre van szüksége a felhasználótól (például kéttényezős hitelesítés). A `Claims` tag tartalmaz néhány JSON töredék a követelések, amelyek várhatóan.
- Még mindig ADAL.NET a kivételt fogadó nyilvános `AcquireTokenInteractive` ügyfélalkalmazásnak meg kell hívnia a jogcímparaméterrel rendelkező felülbírálási paramétert. Ez a `AcquireTokenInteractive` felülbírálása nem is próbálja meg, hogy elérje a cache, mert nem szükséges. Ennek az az oka, hogy a gyorsítótárban lévő `AdalClaimChallengeException` jogkivonat nem rendelkezik a megfelelő jogcímeket (ellenkező esetben egy nem lett volna dobott). Ezért nincs szükség a gyorsítótár ra. Vegye figyelembe, hogy a `ClaimChallengeException` lehet fogadni a WebAPI `AcquireTokenInteractive` csinál OBO, mivel a kell hívni egy nyilvános ügyfél alkalmazás hívja ezt a webes API-t.
- a részletekért, beleértve a mintákat lásd: [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) kezelése

A MSAL.NET a jogcímkihívás-kivételeket a következőképpen kezeljük:

- A `Claims` felszínre a `MsalServiceException`.
- Van egy `.WithClaim(claims)` módszer, amely `AcquireTokenInteractive` alkalmazható a szerkesztő.

### <a name="supported-grants"></a>Támogatott támogatások

Nem minden támogatást még nem támogatott MSAL.NET és a v2.0 végpont. Az alábbiakban összefoglaljuk a ADAL.NET és az MSAL-t. a NET által támogatott támogatások.

#### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

Itt vannak a támogatott támogatások ADAL.NET és MSAL.NET asztali és mobil alkalmazások

Engedély | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktív | [Interaktív hitelesítés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Tokenek interaktív beszerzése MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Beépített Windows-hitelesítés | [Integrált hitelesítés Windows rendszeren (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication)
Felhasználónév / Jelszó | [Tokenek beszerzése felhasználónévvel és jelszóval](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Felhasználónév jelszó-hitelesítése](msal-authentication-flows.md#usernamepassword)
Eszközkód-áramlás | [Eszközprofil webböngészőnélküli eszközökhöz](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Eszközkód-folyamat](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

A webes alkalmazások, webes API-k és démonalkalmazások ADAL.NET és MSAL.NET támogatott támogatások:

Alkalmazás típusa | Engedély | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webalkalmazás, webes API, démon | Ügyfél hitelesítő adatai | [Ügyfélhitelesítő adatok ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Ügyfél hitelesítő adatok áramlása a MSAL.NET)](msal-authentication-flows.md#client-credentials)
Webes API | A | [Szolgáltatás szolgáltatás hívások nevében a felhasználó ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [a MSAL.NET nevében](msal-authentication-flows.md#on-behalf-of)
Webalkalmazás | Hitelesítési kód | [Jogkivonatok beszerzése engedélyezési kódokkal a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Jogkivonatok beszerzése engedélyezési kódokkal az A MSAL.NET rendelkező webalkalmazásokban](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Gyorsítótár-megőrzés

ADAL.NET lehetővé teszi `TokenCache` az osztály kiterjesztését a kívánt adatmegőrzési funkció megvalósítására a biztonságos tároló `BeforeAccess`nélküli `BeforeWrite` platformokon (.NET Framework és .NET core) a használatával. További információt a [tokengyorsítótár-szerializálás ADAL.NET.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)

MSAL.NET a token gyorsítótárát lezárt osztálysá teszi, megszüntetve a kiterjesztés lehetőségét. Ezért a jogkivonat-gyorsítótár-megőrzési megvalósításának egy segítő osztály, amely kölcsönhatásba lép a lezárt jogkivonat-gyorsítótárakkal. Ezt az interakciót a [tokengyorsítótár szerializálása MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)ismerteti.

## <a name="signification-of-the-common-authority"></a>A közös hatóság megjelölése

Az 1.0-s számban, ha a jogosultságot használja, lehetővé teszi a `https://login.microsoftonline.com/common` felhasználók számára, hogy bármely AAD-fiókkal jelentkezzenek be (bármely szervezet esetében). Lásd: [Jogosultságérvényesítés ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Ha a `https://login.microsoftonline.com/common` 2.0-s verzióban használja a jogosultságot, lehetővé teszi a felhasználók számára, hogy bármely AAD-szervezettel vagy Microsoft-személyes fiókkal (MSA) jelentkezzenek be. Ha MSAL.NET szeretné korlátozni a bejelentkezést bármely AAD-fiókra (ugyanaz a `https://login.microsoftonline.com/organizations`viselkedés, mint ADAL.NET), akkor a használatát kell használnia. A részleteket `authority` lásd a paraméter [nyilvános ügyfélalkalmazás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>1.0-s és 2.0-s jogkivonatok

A tokeneknek két verziója van:
- 1.0-s kulcs
- 2.0-s kulcs

A v1.0 végpont (aDal által használt) csak az 1.0-s jogkivonatokat bocsátja ki.

Azonban a v2.0 végpont (msal által használt) a jogkivonat, amely a webes API elfogadja. A webes API alkalmazásjegyzék-tulajdonsága lehetővé teszi a fejlesztők számára, hogy kiválasszák a token elfogadott verzióját. Lásd `accessTokenAcceptedVersion` az [alkalmazásjegyzék](reference-app-manifest.md) referenciadokumentációjában.

A v1.0-s és 2.0-s jogkivonatokkal kapcsolatos további információkért lásd: [Azure Active Directory-hozzáférési jogkivonatok](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Hatókör egy web API-hoz, amely elfogadja a v1.0-s jogkivonatokat

Az OAuth2 engedélyek olyan engedélyhatókörök, amelyeket egy v1.0-s webes API-alkalmazás (erőforrás) az ügyfélalkalmazások számára elérhetővé. Ezek az engedélyható körök a hozzájárulás során adhatók meg az ügyfélalkalmazásoknak. Tekintse meg az oauth2Permissions az [Azure Active Directory alkalmazásjegyzékben című szakaszt.](active-directory-application-manifest.md)

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Egy 1.0-s alkalmazás adott OAuth2 engedélyéhez való hozzáférés kérésének hatóhatókörei

Ha egy 1.0-s jogkivonatokat (például a Microsoft Graph API-t, https://graph.microsoft.com)azaz a tetszésszerint a kívánt erőforrás-azonosító összefűzésével kell létrehoznia egy kívánt OAuth2-engedéllyel rendelkező alkalmazáshoz) jogkivonatokat szeretne beszerezni. `scopes`

Ha például a felhasználó nevében szeretne hozzáférni egy v1.0-s `ResourceId`webes API-hoz, amelyet az Alkalmazásazonosító URI-nak használni szeretne, a következőket szeretné használni:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Ha MSAL.NET Azure Active Directoryval szeretne olvasni és olvasnihttps://graph.microsoft.com/)a Microsoft Graph API használatával ( , hozzon létre egy listát a hatókörökről, például a következő kódrészletben:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Figyelmeztetés: Ha egy vagy két perjel van a v1.0 webes API-nak megfelelő hatókörben

Ha az Azure Resource Manager API-nak megfelelőhttps://management.core.windows.net/)hatókört szeretné írni ( , a következő hatókört kell kérnie (vegye figyelembe a két perjelet)

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Ennek az az oka, hogy a Resource`aud`Manager API perjelet vár a közönségjogcímében ( ), és van egy perjel, amely elválasztja az API-nevet a hatókörtől.

Az Azure AD által használt logika a következő:
- ADAL (1.0-s) végpont egy v1.0-s hozzáférési jogkivonattal (az egyetlen lehetséges), aud=resource
- MSAL (2.0-s végpont) egy hozzáférési jogkivonatot kér egy erőforrás tanusítatot a v2.0-s jogkivonatokat fogadó, aud=resource. Appid
- MSAL (2.0-s végpont) egy hozzáférési jogkivonatot kér egy erőforrás tanusítja a v1.0-s hozzáférési jogkivonatot (ami a fenti esetben), az Azure AD elemzi a kívánt közönséget a kért hatókörben azáltal, hogy mindent az utolsó perjel előtt, és erőforrás-azonosítóként használja. Ezért ha\/https: /database.windows.net ahttps://database.windows.net/" " közönséget várja, https:/database.windows.net//.default\/tartományt kell kérnie. Lásd még a[# 747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)számú probléma: Az erőforrás url-jének záró pere nincs megadva, ami sql auth hibát okozott #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókör a v1.0-s alkalmazás összes engedélyéhez való hozzáférés kéréséhez

Ha például egy v1.0-s alkalmazás összes statikus hatóköréhez jogkivonatot szeretne beszerezni, akkor

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Az ügyfél hitelesítő adatok áramlása / démonalkalmazás esetén kért hatókörök

Az ügyfél hitelesítő adatok áramlása esetén a `/.default`továbbítandó hatókör is . Ez a hatókör az Azure AD-nek a következőket adja meg: "az összes olyan alkalmazásszintű engedély, amelyhez a rendszergazda hozzájárult az alkalmazás regisztrációjában.

## <a name="adal-to-msal-migration"></a>ADAL a MSAL migráció

A ADAL.NET v2. X, a frissítési jogkivonatok voltak kitéve, amely lehetővé teszi, hogy megoldásokat `AcquireTokenByRefreshToken` kifejlesztése ezen tokenek használatával, a gyorsítótárazás, és az ADAL 2.x által biztosított módszerek használatával.
Ezen megoldások némelyikét olyan forgatókönyvekben használták, mint például:
* Hosszú ideig futó szolgáltatások, amelyek műveleteket hajtanak végre, beleértve az irányítópultok frissítését a felhasználók nevében, míg a felhasználók már nem kapcsolódnak.
* WebFarm-forgatókönyvek, amelyek lehetővé teszik az ügyfél számára, hogy az RT-t a webszolgáltatásba vigye (a gyorsítótárazás ügyféloldali, titkosított cookie, nem pedig kiszolgálói oldal)

MSAL.NET biztonsági okokból nem teszi elérhetővé a frissítési jogkivonatokat: az MSAL lekéri a frissítő jogkivonatokat.

Szerencsére MSAL.NET most már rendelkezik egy API-val, amely lehetővé teszi `IConfidentialClientApplication`a korábbi (ADAL-lal beszerzett) frissítési tokenek áttelepítését a következőkbe:

```csharp
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

Ezzel a módszerrel biztosíthatja a korábban használt frissítési jogkivonatot a kívánt hatókörökkel (erőforrásokkal) együtt. A frissítési jogkivonat ot kicseréli egy újra, és gyorsítótárba helyezi az alkalmazásba.

Mivel ez a módszer nem tipikus forgatókönyvekhez készült, nem könnyen `IConfidentialClientApplication` hozzáférhető a `IByRefreshToken`anélkül, hogy először a .

Ez a kódrészlet néhány áttelepítési kódot jelenít meg egy bizalmas ügyfélalkalmazásban. `GetCachedRefreshTokenForSignedInUser`az ADAL 2.x-et használó alkalmazás egy korábbi verziója által tárolt frissítési jogkivonat lekérése. `GetTokenCacheForSignedInUser`deszerializálja a bejelentkezett felhasználó gyorsítótárát (mivel a bizalmas ügyfélalkalmazásoknak felhasználónként egy gyorsítótárral kell rendelkezniük).

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

Az új frissítési jogkivonat a gyorsítótárban való tárolása közben egy hozzáférési jogkivonatot és azonosítótokent fog visszaadni.

Ezt a módszert különböző integrációs forgatókönyvekhez is használhatja, ahol frissítési jogkivonat érhető el.

## <a name="next-steps"></a>További lépések

A [hatókörökről a Hatókörök, engedélyek és hozzájárulás a Microsoft identity platform végpontjában](v2-permissions-and-consent.md) talál további információt.
