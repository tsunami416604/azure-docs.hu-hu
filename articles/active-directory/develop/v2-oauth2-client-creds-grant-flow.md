---
title: A Microsoft Identity platform használatával felhasználói beavatkozás nélkül férhet hozzá a biztonságos erőforrásokhoz | Azure
description: Hozzon létre webalkalmazásokat a OAuth 2,0 hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1499e931a81e31494d7ff442c8295ba03f1cf33
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207636"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity platform és a OAuth 2,0 ügyfél-hitelesítő adatok folyamata

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Használhatja az RFC 6749-ben megadott [OAuth 2,0 ügyfél-hitelesítő adatokat](https://tools.ietf.org/html/rfc6749#section-4.4) , más néven *kétlábú OAuth*, hogy a webkiszolgálók hozzáférhessenek az alkalmazások identitásának használatával. Ezt a típust általában olyan kiszolgálók közötti interakciók esetén használják, amelyeknek a háttérben kell futniuk, anélkül, hogy a felhasználóval való azonnali interakcióra lenne szükség. Ezeket az alkalmazásokat gyakran *démonoknak* vagy *szolgáltatásfiókoknek*nevezzük.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

A OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamata lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett a hitelesítéshez egy másik webszolgáltatás hívásakor. Ebben az esetben az ügyfél általában egy közepes szintű webszolgáltatás, egy démon-szolgáltatás vagy egy webhely. A Microsoft Identity platform lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes Azure AD-forgatókönyvet és-funkciót. Annak megállapításához, hogy a Microsoft Identity platform-végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

A tipikusan *három lábon járó OAuth*egy ügyfélalkalmazás jogosult egy adott felhasználó nevében hozzáférni egy erőforráshoz. Az engedélyt a felhasználó delegálja az alkalmazásnak, általában a [beleegyezési](v2-permissions-and-consent.md) folyamat során. Az ügyfél hitelesítő adataiban (*kétlábú OAuth*) azonban az engedélyek közvetlenül az alkalmazás számára is megadhatók. Amikor az alkalmazás jogkivonatot jelenít meg egy erőforráshoz, az erőforrás azt kényszeríti, hogy maga az alkalmazás engedélyezte a művelet végrehajtását, és nem a felhasználót.

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes ügyfél-hitelesítő adatok folyamata az alábbi ábrához hasonlóan néz ki. A cikk későbbi részében leírt lépéseket ismertetjük.

![Az ügyfél-hitelesítő adatok folyamatát ábrázoló diagram](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Közvetlen engedély kérése

Az alkalmazások általában közvetlen engedélyt kapnak az erőforrásokhoz való hozzáférésre a következő két módszer egyikével:

* [Hozzáférés-vezérlési lista (ACL) használatával az erőforráson](#access-control-lists)
* [Alkalmazás-engedélyezési hozzárendelés az Azure AD-ben](#application-permissions)

Ez a két módszer a leggyakoribb az Azure AD-ben, és azt javasoljuk, hogy az ügyfél és a hitelesítő adatok folyamatát végző erőforrások számára. Egy erőforrás más módokon is dönthet az ügyfelek hitelesítéséhez. Minden erőforrás-kiszolgáló kiválaszthatja azt a metódust, amely a leginkább értelmezi az alkalmazását.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák

Egy erőforrás-szolgáltató érvényesítheti az engedélyezési ellenőrzési listát az általa ismert alkalmazás-(ügyfél-) azonosítók alapján, és egy adott hozzáférési szintet biztosít a számára. Ha az erőforrás jogkivonatot kap a Microsoft Identity platform végponttól, akkor dekódolhatja a jogkivonatot, és kinyerheti az ügyfél alkalmazás-AZONOSÍTÓját a `appid` és `iss` jogcímek közül. Ezután összehasonlítja az alkalmazást egy általa fenntartott hozzáférés-vezérlési listával (ACL). Az ACL részletessége és metódusa jelentősen változhat az erőforrások között.

Gyakori használati eset az, ha egy ACL-t használ a webalkalmazásokhoz vagy webes API-hoz való tesztek futtatásához. A webes API a teljes engedélyek egy részhalmazát is megadhatja egy adott ügyfél számára. A végpontok közötti tesztek az API-on való futtatásához hozzon létre egy teszt-ügyfelet, amely a Microsoft Identity platform-végponttól szerzi be a jogkivonatokat, majd elküldi azokat az API-nak. Az API ezt követően ellenőrzi az ügyfél alkalmazás-AZONOSÍTÓJÁHOZ tartozó ACL-t az API teljes funkcionalitásának teljes körű eléréséhez. Ha ezt a típusú ACL-t használja, ügyeljen arra, hogy ne csak a hívó `appid` értékét ellenőrizze, hanem azt is, hogy a token `iss` értéke megbízható-e.

Az ilyen típusú hitelesítés olyan démonok és szolgáltatásfiókok esetében fordul elő, amelyek a személyes Microsoft-fiókkal rendelkező fogyasztói felhasználók tulajdonában lévő adatforgalomhoz szükségesek. A szervezetek által birtokolt adattárolók esetében javasoljuk, hogy az alkalmazás engedélyein keresztül szerezze be a szükséges engedélyeket.

### <a name="application-permissions"></a>Alkalmazás engedélyei

Az ACL-ek használata helyett API-k használatával teheti elérhetővé az alkalmazások engedélyeit. Egy alkalmazás engedélyt kap egy alkalmazás számára egy szervezet rendszergazdája, és csak az adott szervezet és alkalmazottai által birtokolt adathozzáféréshez használható. Microsoft Graph például több alkalmazás-engedélyt tesz elérhetővé a következők elvégzéséhez:

* Levelek olvasása az összes postaládában
* Levelek olvasása és írása az összes postaládában
* E-mail küldése bármely felhasználóként
* Címtáradatok olvasása

Az alkalmazás engedélyeivel kapcsolatos további információkért nyissa meg a [Microsoft Graph](https://developer.microsoft.com/graph).

Az alkalmazás engedélyeinek használatához kövesse a következő szakaszokban ismertetett lépéseket.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az engedélyek igénylése az alkalmazás regisztrációs portálján

1. Regisztráljon, és hozzon létre egy alkalmazást az új [Alkalmazásregisztrációk (előzetes verzió) felületén](quickstart-register-app.md).
2. Nyissa meg az alkalmazást az Alkalmazásregisztrációk (előzetes verzió) felületén. Navigáljon a **tanúsítványok & Secrets** szakaszhoz, és vegyen fel egy **új ügyfél-titkot**, mert legalább egy ügyfél-titok szükséges a jogkivonat igényléséhez.
3. Keresse meg az **API-engedélyek** szakaszt, majd adja hozzá az alkalmazás által igényelt alkalmazás- **engedélyeket** .
4. **Mentse** az alkalmazás regisztrációját.

#### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: a felhasználó aláírása az alkalmazásba

Az alkalmazás-engedélyeket használó alkalmazások létrehozásakor általában az alkalmazásnak olyan oldalra vagy nézetre van szüksége, amelyen a rendszergazda jóváhagyja az alkalmazás engedélyeit. Ez az oldal lehet az alkalmazás bejelentkezési folyamatának része, az alkalmazás beállításainak egy része, vagy egy dedikált "kapcsolat" folyamat is. Sok esetben érdemes megmutatni, hogy az alkalmazás csak akkor jelenjen meg ez a "kapcsolódás" nézet, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiók bejelentkezett.

Ha aláírja a felhasználót az alkalmazásba, azonosíthatja azt a szervezetet, amelyhez a felhasználó tartozik, mielőtt megkéri a felhasználót, hogy hagyja jóvá az alkalmazás engedélyeit. Bár ez nem feltétlenül szükséges, a segítségével könnyebben hozhat létre intuitív felhasználói élményt. A felhasználó aláírásához kövesse a [Microsoft Identity platform protokoll oktatóanyagokat](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Engedélyek kérése egy címtár-rendszergazdától

Ha készen áll arra, hogy engedélyt kérjen a szervezet rendszergazdájától, átirányíthatja a felhasználót a Microsoft Identity platform *rendszergazdai engedélyezési végpontján*.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Saját alkalmazás-azonosító használata a legjobb eredményekhez – az oktatóanyag alkalmazás nem kér hasznos engedélyeket.) [![próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

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
| `tenant` | Szükséges | Az a címtár-bérlő, amelyre engedélyt szeretne kérni. Ez lehet a GUID vagy a felhasználóbarát név formátuma. Ha nem tudja, hogy a felhasználó melyik bérlőhöz tartozik, és szeretne bejelentkezni bármelyik Bérlővel, használja a `common`. |
| `client_id` | Szükséges | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `redirect_uri` | Szükséges | Az az átirányítási URI, ahová az alkalmazásnak el kell juttatnia a választ a kezelésére. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-címet kell kódolni, és további elérésiút-szegmensekkel is rendelkezhet. |
| `state` | Ajánlott | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. Bármely kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |

Ezen a ponton az Azure AD azt kényszeríti, hogy csak a bérlői rendszergazda jelentkezhet be a kérelembe. A rendszer felkéri a rendszergazdát, hogy fogadja el az alkalmazás regisztrálásához szükséges összes közvetlen alkalmazást az alkalmazás regisztrációs portálján.

##### <a name="successful-response"></a>Sikeres válasz

Ha a rendszergazda jóváhagyja az alkalmazás engedélyeit, a sikeres válasz a következőképpen néz ki:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A címtár-bérlő, amely az alkalmazást a kért engedélyekkel rendelkezik, GUID formátumban megadva. |
| `state` | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. Bármely kívánt tartalom sztringje lehet. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására szolgál a hitelesítési kérelem végrehajtása előtt, például az oldal vagy a nézet megtekintését. |
| `admin_consent` | Értéke **true (igaz**). |

##### <a name="error-response"></a>Hiba válasza

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőre hasonlít:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a hibák típusának osztályozására használható, és amelyek segítségével reagálhat a hibákra. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hiba kiváltó okának azonosításában. |

Miután sikeres választ kapott az App kiépítési végponttól, az alkalmazás megszerezte a kért közvetlen alkalmazás-engedélyeket. Most már igényelhet tokent a kívánt erőforráshoz.

## <a name="get-a-token"></a>Jogkivonat beszerzése

Miután megszerezte az alkalmazáshoz szükséges engedélyeket, folytassa az API-k hozzáférési jogkivonatának beszerzésével. Ha a tokent az ügyfél hitelesítő adatainak megadása segítségével szeretné lekérni, küldjön egy POST-kérelmet a `/token` Microsoft Identity platform-végpontnak:

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán! (Saját alkalmazás-azonosító használata a legjobb eredményekhez – az oktatóanyag alkalmazás nem kér hasznos engedélyeket.) [![próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: hozzáférési jogkivonat-kérelem közös titokkal

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
| `tenant` | Szükséges | Az az alkalmazás, amely a (z) GUID-vagy tartománynév-formátumban való működésre tervezi a címtárat. |
| `client_id` | Szükséges | Az alkalmazáshoz hozzárendelt alkalmazás-azonosító. Ezt az információt a portálon találja, ahol regisztrálta az alkalmazást. |
| `scope` | Szükséges | A kérelemben szereplő `scope` paraméternek átadott értéknek a kívánt erőforrás erőforrás-azonosítója (Application ID URI) kell lennie, amelyet a `.default` utótaggal kell feltüntetni. A Microsoft Graph példában az érték `https://graph.microsoft.com/.default`. <br/>Ez az érték közli a Microsoft Identity platform végpontját, amely az alkalmazáshoz konfigurált összes közvetlen alkalmazási engedélyre vonatkozik, a végpontnak a használni kívánt erőforráshoz társított tokent kell kiállítania. Ha többet szeretne megtudni a `/.default` hatóköréről, tekintse meg a [beleegyezett dokumentációt](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Szükséges | Az alkalmazás regisztrációs portálján az alkalmazáshoz generált ügyfél-titkos kulcs. Az ügyfél titkos kódjának URL-kódolással kell rendelkeznie a küldés előtt. |
| `grant_type` | Szükséges | `client_credentials`értékre kell állítani. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: hozzáférési jogkivonat kérése tanúsítvánnyal

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
| `tenant` | Szükséges | Az az alkalmazás, amely a (z) GUID-vagy tartománynév-formátumban való működésre tervezi a címtárat. |
| `client_id` | Szükséges |Az alkalmazáshoz hozzárendelt alkalmazás (ügyfél) azonosítója. |
| `scope` | Szükséges | A kérelemben szereplő `scope` paraméternek átadott értéknek a kívánt erőforrás erőforrás-azonosítója (Application ID URI) kell lennie, amelyet a `.default` utótaggal kell feltüntetni. A Microsoft Graph példában az érték `https://graph.microsoft.com/.default`. <br/>Ez az érték tájékoztatja a Microsoft Identity platform végpontját, amely az alkalmazáshoz konfigurált összes közvetlen alkalmazási engedélyhez tartozik, és a használni kívánt erőforráshoz társított jogkivonatot kell kiállítania. Ha többet szeretne megtudni a `/.default` hatóköréről, tekintse meg a [beleegyezett dokumentációt](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Szükséges | Az értéket `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`értékre kell beállítani. |
| `client_assertion` | Szükséges | Egy, az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvánnyal rendelkező (JSON webes jogkivonat). Tudnivalók a [tanúsítvány hitelesítő adatairól](active-directory-certificate-credentials.md) : a tanúsítvány regisztrálásának és az állítás formátumának megismerése.|
| `grant_type` | Szükséges | `client_credentials`értékre kell állítani. |

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a közös titok által benyújtott kérelem esetében, kivéve, ha a client_secret paramétert két paraméter helyettesíti: egy client_assertion_type és client_assertion.

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
| `access_token` | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a tokent a biztonságos erőforráshoz, például egy webes API-hoz való hitelesítéshez. |
| `token_type` | Megadja a jogkivonat típusának értékét. Az egyetlen típus, amelyet a Microsoft Identity platform támogat, `bearer`. |
| `expires_in` | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |

### <a name="error-response"></a>Hiba válasza

A hiba a következőképpen néz ki:

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
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben. |
| `timestamp` | A hiba bekövetkezésének időpontja. |
| `trace_id` | A diagnosztika támogatásához szükséges kérelem egyedi azonosítója. |
| `correlation_id` | Egy egyedi azonosító, amely az egyes összetevőkben a diagnosztika segítésére szolgál. |

## <a name="use-a-token"></a>Token használata

Most, hogy megszerezte a jogkivonatot, a token használatával teheti meg a kéréseket az erőforrásnak. A jogkivonat lejárata után ismételje meg a kérést az `/token` végpontra egy új hozzáférési jogkivonat beszerzéséhez.

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

## <a name="code-samples-and-other-documentation"></a>Példák a kódokra és egyéb dokumentációra

Olvassa el az [ügyfél hitelesítő adatai – áttekintés dokumentációját](https://aka.ms/msal-net-client-credentials) a Microsoft hitelesítési könyvtárából

| Minta | Platform |Leírás |
|--------|----------|------------|
|[Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2,1 konzol | Egy egyszerű .NET Core-alkalmazás, amely egy adott bérlő felhasználóit jeleníti meg a Microsoft Graph az alkalmazás identitásával, a felhasználó nevében. A minta azt is szemlélteti, hogy a hitelesítés tanúsítványokat használ a hitelesítéshez. |
|[Active-Directory-DotNet-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Egy webalkalmazás, amely a Microsoft Graph adatait az alkalmazás identitásával szinkronizálja, ahelyett, hogy a felhasználó nevében használja. |
