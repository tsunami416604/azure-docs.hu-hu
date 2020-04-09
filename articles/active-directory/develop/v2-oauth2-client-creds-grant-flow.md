---
title: OAuth 2.0 ügyfél hitelesítő adatok áramlását a Microsoft identitás platform | Azure
description: Webalkalmazásokat hozhat létre az OAuth 2.0 hitelesítési protokoll Microsoft identitásplatform-implementációjával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a03f8cb412b6d6ae95165331ae836bdfde5d670d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886296"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>A Microsoft identity platform és az OAuth 2.0 ügyfélhitelesítő adatok folyamata

Az [OAuth 2.0 ügyfél hitelesítő adatok megadása](https://tools.ietf.org/html/rfc6749#section-4.4) a 6749-es számú RFC-ben , más néven *kétlábú OAuth-ban*található ügyfélhitelesítő adatok használatával egy alkalmazás identitásának használatával érheti el a weben tárolt erőforrásokat. Az ilyen típusú támogatást gyakran használják a kiszolgálók közötti interakciókhoz, amelyeknek a háttérben kell futniuk, a felhasználóval való azonnali interakció nélkül. Az ilyen típusú alkalmazásokat gyakran démonnak vagy *szolgáltatásfióknak* *nevezik* .

Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen. Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

Az OAuth 2.0 ügyfél hitelesítő adatok at flow lehetővé teszi a webszolgáltatás (bizalmas ügyfél) a saját hitelesítő adatait, ahelyett, hogy megszemélyesíteni a felhasználó, hitelesítése, ha egy másik webszolgáltatás hívása. Ebben a forgatókönyvben az ügyfél általában egy középső rétegű webszolgáltatás, egy démonszolgáltatás vagy egy webhely. A magasabb szintű biztonság érdekében a Microsoft identity platform azt is lehetővé teszi, hogy a hívó szolgáltatás egy tanúsítványt használjon (a közös titok helyett) hitelesítő adatként.

> [!NOTE]
> A Microsoft identity platform végpont jatttal nem támogatja az összes Azure AD-forgatókönyvek és -szolgáltatások. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

A tipikus *háromlábú OAuth-ban*egy ügyfélalkalmazás engedélyt kap egy erőforrás elérésére egy adott felhasználó nevében. Az engedélyt a felhasználó delegálja az alkalmazásnak, általában a [jóváhagyási](v2-permissions-and-consent.md) folyamat során. Az ügyfél hitelesítő adatai (*kétlábú OAuth)* folyamatában azonban az engedélyek közvetlenül magának az alkalmazásnak adnak meg engedélyeket. Amikor az alkalmazás egy jogkivonatot jelenít meg egy erőforrásnak, az erőforrás kényszeríti, hogy maga az alkalmazás rendelkezik engedéllyel egy művelet végrehajtására, és nem a felhasználó.

## <a name="protocol-diagram"></a>Protokolldiagram

A teljes ügyfélhitelesítő adatfolyam az alábbi ábrához hasonlóan néz ki. A cikk későbbi lépéseit ismertetjük.

![Az ügyfél hitelesítő adatainak folyamatát bemutató diagram](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Közvetlen engedély beszerezni

Az alkalmazások általában közvetlen engedélyt kapnak egy erőforrás elérésére a következő két módszer egyikével:

* [Hozzáférés-vezérlési listán (ACL) keresztül az erőforráson](#access-control-lists)
* [Alkalmazásengedély-hozzárendelés az Azure AD-ben](#application-permissions)

Ez a két módszer a leggyakoribb az Azure AD-ben, és azt javasoljuk, hogy az ügyfelek és az erőforrások, amelyek az ügyfél hitelesítő adatok áramlását. Egy erőforrás más módon is engedélyezheti ügyfeleit. Minden erőforrás-kiszolgáló kiválaszthatja azt a módszert, amely a legmegfelelőbb az alkalmazásszámára.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák

Az erőforrás-szolgáltató kényszerítheti az engedélyezési ellenőrzést az általa ismerik az alkalmazásazonosítók listáját, és adott szintű hozzáférést biztosítanak. Amikor az erőforrás jogkivonatot kap a Microsoft identity platform végpontról, dekódolhatja a jogkivonatot, és kinyerheti az ügyfél alkalmazásazonosítóját a és `appid` `iss` a jogcímek. Ezután összehasonlítja az alkalmazást egy általa fenntartott hozzáférés-vezérlési listával (ACL). Az ACL részletessége és módszere jelentősen eltérhet az erőforrások között.

Gyakori használati eset egy ACL használatával futtathat teszteket egy webalkalmazáshoz vagy egy webes API-hoz. Előfordulhat, hogy a webes API csak a teljes engedélyek egy részhalmazát adja meg egy adott ügyfélnek. Az API-n futó végpontok teszteléséhez hozzon létre egy tesztügyfelet, amely beszerzi a jogkivonatokat a Microsoft identity platform végpontjáról, majd elküldi azokat az API-nak. Az API ezután ellenőrzi az ACL a tesztügyfél alkalmazásazonosítóját az API teljes funkcionalitásához való teljes hozzáféréshez. Ha ilyen típusú ACL-t használ, győződjön meg `appid` róla, hogy nem `iss` csak a hívó értékét ellenőrzi, hanem azt is, hogy a token értéke megbízható-e.

Az ilyen típusú engedélyezés gyakori a démonok és a szolgáltatásfiókok, amelyek személyes Microsoft-fiókkal rendelkező fogyasztói felhasználók tulajdonában lévő adatokhoz kell hozzáférniük. A szervezetek tulajdonában lévő adatok esetében azt javasoljuk, hogy a szükséges engedélyeket alkalmazásengedélyeken keresztül kapja meg.

### <a name="application-permissions"></a>Alkalmazásengedélyek

Az ACL-ek használata helyett az API-k segítségével elérhetővé teheti az **alkalmazásengedélyek készletét.** Az alkalmazásengedélyt a szervezet rendszergazdája ad meg egy alkalmazásnak, és csak az adott szervezet és alkalmazottai tulajdonában lévő adatok elérésére használható. A Microsoft Graph például több alkalmazásengedélyt tesz elérhetővé a következők höz:

* Levelek olvasása az összes postaládában
* Levelek olvasása és írása az összes postaládában
* E-mail küldése bármely felhasználóként
* Címtáradatok olvasása

Az alkalmazásengedélyekről a [Microsoft Graph című lapban](https://developer.microsoft.com/graph)talál további információt.

Ha alkalmazásengedélyeket szeretne használni az alkalmazásban, kövesse a következő szakaszokban ismertetett lépéseket.


> [!NOTE]
> Ha a felhasználóval ellentétben alkalmazásként hitelesíti, nem használhatja a "delegált engedélyeket" (a felhasználó által megadott hatóköröket).  Az alkalmazáshoz (vagy a webes API előzetes hitelesítése) megadott "alkalmazásengedélyeket", más néven "szerepköröket" kell használnia.    


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Engedélyek kérése az alkalmazásregisztrációs portálon

1. Regisztráljon és hozzon létre egy alkalmazást az új [alkalmazásregisztrációk (előzetes verzió) felületén.](quickstart-register-app.md)
2. Nyissa meg az alkalmazást az alkalmazásregisztrációk (előzetes verzió) élményben. Keresse meg a **Tanúsítványok & titkos kulcsok szakaszt,** és adjon hozzá egy új **ügyféltitkot,** mert a jogkivonat kéréséhez legalább egy ügyféltitokra lesz szüksége.
3. Keresse meg az **API-engedélyek szakaszt,** majd adja hozzá az alkalmazás **szükséges alkalmazásengedélyeket.**
4. **Mentse** az alkalmazás regisztrációját.

#### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: Írja alá a felhasználót az alkalmazásba

Amikor alkalmazásengedélyeket használó alkalmazást hoz létre, az alkalmazásnak általában szüksége van egy olyan lapra vagy nézetre, amelyen a rendszergazda jóváhagyja az alkalmazás engedélyeit. Ez az oldal része lehet az alkalmazás bejelentkezési folyamatának, az alkalmazás beállításainak része, vagy lehet egy dedikált "connect" folyamat. Sok esetben célszerű, hogy az alkalmazás csak akkor jelenítse meg ezt a "connect" nézetet, ha a felhasználó bejelentkezett egy munkahelyi vagy iskolai Microsoft-fiókkal.

Ha bejelentkezik a felhasználóaz alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a felhasználó tartozik, mielőtt megkérné a felhasználót az alkalmazásengedélyek jóváhagyására. Bár nem feltétlenül szükséges, segíthet egy intuitívabb felhasználói élményt létrehozni. A felhasználó bejelentkezéséhez kövesse a [Microsoft identity platform protokoll oktatóanyagait.](active-directory-v2-protocols.md)

#### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése címtáradminisztrátortól

Ha készen áll arra, hogy engedélyeket kérjen a szervezet rendszergazdájától, átirányíthatja a felhasználót a Microsoft identity platform *rendszergazdájának hozzájárulási végpontjára.*

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman! (A legjobb eredmény érdekében használja a saját alkalmazásazonosítóját – az oktatóanyag-alkalmazás nem kér hasznos engedélyeket.) [Próbálja meg futtatni ezt a kérést a Postman ben ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amelytől engedélyt szeretne kérni. Ez lehet GUID vagy rövid név formátumban. Ha nem tudja, hogy a felhasználó melyik bérlőhöz tartozik, és szeretné, `common`hogy bármelyik bérlővel jelentkezzen be, használja a használatát. |
| `client_id` | Kötelező | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `redirect_uri` | Kötelező | Az átirányítási URI, ahol azt szeretné, hogy a választ kell küldeni az alkalmazás kezelni. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy URL-kódolva kell lennie, és további elérési útszegmensekkel is rendelkezhet. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszaadja. Bármilyen tartalom karakterlánca lehet. Az állapot a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például az oldal vagy a nézet, amelyen voltak. |

Ezen a ponton az Azure AD kényszeríti, hogy csak egy bérlői rendszergazda jelentkezhet be a kérelem teljesítéséhez. A rendszergazdának jóvá kell hagynia az alkalmazáshoz kért összes közvetlen alkalmazásengedélyt az alkalmazás regisztrációs portálján.

##### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | Az a címtár-bérlő, amely megadta az alkalmazásnak az általa kért engedélyeket GUID formátumban. |
| `state` | Egy érték, amely szerepel a kérelemben, amely szintén visszaadja a jogkivonat-válasz. Bármilyen tartalom karakterlánca lehet. Az állapot a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt, például az oldal vagy a nézet, amelyen voltak. |
| `admin_consent` | Értéke **Igaz**. |

##### <a name="error-response"></a>Hibaválasz

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amellyel hibatípusokat osztályozhat, és amelya hibákra való reagáláshoz használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hiba kiváltó okának azonosításában. |

Miután sikeres választ kapott az alkalmazáskiépítési végponttól, az alkalmazás megszerezte a kért közvetlen alkalmazásengedélyeket. Most már kérhet egy jogkivonatot a kívánt erőforráshoz.

## <a name="get-a-token"></a>Token beszereznie

Miután megszerezte az alkalmazáshoz szükséges engedélyt, folytassa az API-k hozzáférési jogkivonatai beszerzésével. Ha jogkivonatot szeretne beszerezni az ügyfél hitelesítő adatok `/token` megadásával, küldjön postai kérelmet a Microsoft identity platform végpontjára:

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman! (A legjobb eredmény érdekében használja a saját alkalmazásazonosítóját – az oktatóanyag-alkalmazás nem kér hasznos engedélyeket.) [Próbálja meg futtatni ezt a kérést a Postman ben ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem megosztott titkos titokkal

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amely ellen az alkalmazás működni kíván, GUID vagy tartománynév formátumban. |
| `client_id` | Kötelező | Az alkalmazáshoz rendelt alkalmazásazonosító. Ezeket az információkat abban a portálon találja, ahol regisztrálta az alkalmazást. |
| `scope` | Kötelező | A kérelemben `scope` a paraméternek átadott értéknek a kívánt erőforrás erőforrás-azonosítójának (alkalmazásazonosító `.default` URI) kell lennie, amelyet az utótaggal kell elhelyezni. A Microsoft Graph példában `https://graph.microsoft.com/.default`az érték . <br/>Ez az érték közli a Microsoft identity platform végpontjával, hogy az alkalmazáshoz konfigurált összes közvetlen alkalmazásengedély közül a végpontnak jogkivonatot kell kiadnia a használni kívánt erőforráshoz társított aknak. A hatókörről `/.default` a [hozzájárulási dokumentációban](v2-permissions-and-consent.md#the-default-scope)olvashat bővebben. |
| `client_secret` | Kötelező | Az alkalmazás regisztrációs portálján létrehozott ügyféltitok. Az ügyféltitok nak URL-kódolásúnak kell lennie az elküldés előtt. |
| `grant_type` | Kötelező | A beállításnak `client_credentials`a beállítására kell beállítható. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amely ellen az alkalmazás működni kíván, GUID vagy tartománynév formátumban. |
| `client_id` | Kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító (ügyfélazonosító). |
| `scope` | Kötelező | A kérelemben `scope` a paraméternek átadott értéknek a kívánt erőforrás erőforrás-azonosítójának (alkalmazásazonosító `.default` URI) kell lennie, amelyet az utótaggal kell elhelyezni. A Microsoft Graph példában `https://graph.microsoft.com/.default`az érték . <br/>Ez az érték tájékoztatja a Microsoft identity platform végpontját, hogy az alkalmazáshoz konfigurált összes közvetlen alkalmazásengedély közül ki kell adnia egy jogkivonatot a használni kívánt erőforráshoz társítottak számára. A hatókörről `/.default` a [hozzájárulási dokumentációban](v2-permissions-and-consent.md#the-default-scope)olvashat bővebben. |
| `client_assertion_type` | Kötelező | Az értéket a `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`értékre kell állítani. |
| `client_assertion` | Kötelező | Egy állítás (egy JSON-webtoken), amelyet létre kell hoznia, és alá kell írnia az alkalmazás hitelesítő adataiként regisztrált tanúsítvánnyal. A [tanúsítvány hitelesítő adatairól megtudhatja,](active-directory-certificate-credentials.md) hogyan regisztrálhatja a tanúsítványt és a feltétel formátumát.|
| `grant_type` | Kötelező | A beállításnak `client_credentials`a beállítására kell beállítható. |

Figyelje meg, hogy a paraméterek majdnem megegyeznek a megosztott titok kérésének esetében, azzal a különbséggel, hogy a client_secret paramétert két paraméter váltja fel: a client_assertion_type és a client_assertion.

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz így néz ki:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot a biztonságos erőforrás, például egy webes API-hoz történő hitelesítéshez. |
| `token_type` | A token típusának értékét jelzi. A Microsoft identity platform csak `bearer`a programot támogatja. |
| `expires_in` | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |

### <a name="error-response"></a>Hibaválasz

A hibaválasz így néz ki:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amelya felmerülő hibatípusok osztályozására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikában. |
| `timestamp` | A hiba bekövetkezésének időpontja. |
| `trace_id` | A diagnosztikával kapcsolatban segítséget kérő kérelem egyedi azonosítója. |
| `correlation_id` | Az összetevők közötti diagnosztika segítésére irányuló kérelem egyedi azonosítója. |

## <a name="use-a-token"></a>Token használata

Most, hogy beszerzett egy jogkivonatot, használja a jogkivonatot az erőforrásra vonatkozó kérelmek teljesítéséhez. Amikor a jogkivonat lejár, ismételje `/token` meg a kérést a végponthoz egy friss hozzáférési jogkivonat beszerzéséhez.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Kódminták és egyéb dokumentációk

Az [ügyfélhitelesítő adatok áttekintő dokumentációjának elolvasása](https://aka.ms/msal-net-client-credentials) a Microsoft hitelesítési tárából

| Sample | Platform |Leírás |
|--------|----------|------------|
|[active-directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 konzol | Egy egyszerű .NET Core alkalmazás, amely megjeleníti a felhasználók a bérlő lekérdezése a Microsoft Graph az alkalmazás identitását használva, nem pedig egy felhasználó nevében. A minta a hitelesítési tanúsítványok at használó változatot is szemlélteti. |
|[active-directory-dotnet-démon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET, MVC | Olyan webalkalmazás, amely a Microsoft Graph adatait szinkronizálja az alkalmazás identitásával, nem pedig egy felhasználó nevében. |
