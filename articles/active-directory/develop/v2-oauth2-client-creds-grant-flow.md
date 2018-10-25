---
title: Használja az Azure AD v2.0 felhasználói beavatkozás nélkül biztonságos erőforrások eléréséhez |} A Microsoft Docs
description: Az Azure AD az OAuth 2.0 hitelesítési protokoll megvalósítása a webes alkalmazásokat hozhat létre.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 2dc1be6b861515cf34f8dd799fa732da530e82a1
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985399"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Az Azure Active Directory 2.0-s verzió és az OAuth 2.0 ügyfél-hitelesítési folyamata

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Használhatja a [OAuth 2.0-ügyfél hitelesítő adatainak megadása](http://tools.ietf.org/html/rfc6749#section-4.4) RFC 6749, más néven megadott *két Egyszárú OAuth*, web-ban üzemeltetett erőforrások eléréséhez az alkalmazás identitását. Az ilyen típusú támogatás általánosan futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül kiszolgálók közötti interakció szolgál. Ilyen típusú alkalmazások gyakran nevezik *démonok* vagy *szolgáltatásfiókok*.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

A több jellemző *három Egyszárú OAuth*, egy ügyfélalkalmazás egy erőforrás eléréséhez egy adott felhasználó nevében engedélyt kap. Az engedély van átadva a felhasználó az alkalmazáshoz, során általában a [hozzájárulás](v2-permissions-and-consent.md) folyamat. Azonban az ügyfél hitelesítési folyamata az engedélyek közvetlenül a magának az alkalmazásnak. Ha az alkalmazás megadja egy erőforrást, az erőforrás-jogkivonat kikényszeríti, hogy maga az alkalmazás végrehajtására egy műveletet, és hogy nem a felhasználók számára engedélyt.

## <a name="protocol-diagram"></a>Protokoll diagramja
A teljes ügyfél hitelesítési folyamata a következő diagram hasonlóan néz ki. A cikk későbbi részében lépések mindegyikét ismertetünk.

![Az ügyfélhitelesítő adatok folyamata](./media/v2-oauth2-client-creds-grant-flow/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Közvetlen engedélyezési beolvasása
Egy alkalmazás általában kap kétféleképpen lévő erőforrások eléréséhez közvetlen engedélyezése: hozzáférés-vezérlési lista (ACL) az erőforráscsoportok, vagy az alkalmazás engedélyek hozzárendelése az Azure Active Directoryban (Azure AD). Két módszer közül a leggyakrabban használt Azure AD-ben, és javasoljuk, hogy azok az ügyfelek és az erőforrást, hajtsa végre az ügyfél hitelesítő adatokat a folyamat. Egy erőforrás lehet váltani, egyéb módon az ügyfelek azonban engedélyezéséhez. Minden egyes erőforrás-kiszolgáló is válassza ki a módszert, amelyet a legésszerűbb az alkalmazásához.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Erőforrás-szolgáltató léptethet egy jogosultsági ellenőrzés, hogy tudja, és a egy bizonyos szintű hozzáférést biztosít alkalmazások azonosítóinak listája alapján. Amikor az erőforrás egy jogkivonatot kap a v2.0-végpont, dekódolni a jogkivonatot, és bontsa ki az ügyfél Alkalmazásazonosítója a `appid` és `iss` jogcímeket. Ezután összeveti az alkalmazás egy ACL, amely a karbantartott ellen. Az ACL granularitási és módszer jelentősen eltérő lehet erőforrások között.

Egy gyakori alkalmazási helyzet, hogy az ACL-webalkalmazás vagy webes API-hoz, a tesztek futtatásához. A webes API-t is biztosít egy adott ügyfél előfordulhat, hogy teljes körű engedélyeket csak egy részhalmazát. Teljes körű teszteket futtatni az API-t, hozzon létre egy tesztügyfél beszerzi a v2.0-végpont származó jogkivonatokat, majd elküldi azokat az API-t. Az API-t ezután ellenőrzi az ACL-t a tesztügyfél Alkalmazásazonosítót az API teljes funkcióinak teljes körű hozzáférést. Ha az ilyen típusú ACL-t használ, mindenképpen ellenőrizze, nem csak a hívó `appid` értéket. Emellett ellenőrizze, hogy a `iss` a jogkivonat értéke megbízható.

Ez a hitelesítési típus démonok és szolgáltatásfiókok, felhasználói személyes Microsoft-fiókkal rendelkező felhasználók tulajdonában lévő adatok elérését igénylő gyakori. A szervezet tulajdonában lévő adatokat javasoljuk, hogy megjelenik-e a szükséges jogosultsági Alkalmazásengedélyek keresztül.

### <a name="application-permissions"></a>Alkalmazásengedélyek
ACL-ek helyett a API-k segítségével teszi közzé a alkalmazást engedélyek egy készletét. Egy alkalmazás engedélyt kap egy alkalmazás által a szervezet rendszergazdája, és csak az adott szervezet és az alkalmazottak tulajdonában lévő adatok eléréséhez használható. Például a Microsoft Graph tesz elérhetővé számos Alkalmazásengedélyek tegye a következőket:

* E-mailek olvasása az összes postaládában
* E-mailek olvasása és írása az összes postaládában
* E-mailek küldése bármely felhasználó nevében
* Címtáradatok olvasása

Alkalmazásengedélyek kapcsolatos további információkért látogasson el [Microsoft Graph](https://graph.microsoft.io).

Alkalmazásengedélyek használata az alkalmazásban, hajtsa végre a lépéseket, a következő szakaszokban bemutatjuk.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon az engedélyek kéréséhez
1. Nyissa meg az alkalmazását a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy [hozzon létre egy alkalmazást](quickstart-v2-register-an-app.md), ha még nem tette. Az alkalmazás létrehozásakor legalább egy alkalmazás titkos használni kell.
2. Keresse meg a **Microsoft Graph-engedélyek** szakaszt, és adja hozzá a **Alkalmazásengedélyek** az alkalmazásának.
3. **Mentés** az alkalmazás regisztrációját.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: Jelentkezzen be a felhasználót az alkalmazáshoz
Általában Alkalmazásengedélyek használó alkalmazás létrehozását, ha az alkalmazás vagy van szükség egy lap, amelyen a rendszergazda hagyja jóvá az Alkalmazásengedélyek megtekintése. Ezen az oldalon az alkalmazás bejelentkezési folyamata, az app-beállításokban, részei lehetnek, vagy egy dedikált "Csatlakozás" folyamat lehet. Sok esetben logikus jelenjen meg ez az alkalmazás "Csatlakozás" nézet csak akkor, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiókkal van bejelentkezve.

Ha bejelentkezik a felhasználót az alkalmazáshoz, azonosíthatja a szervezet, amelyhez a felhasználó tartozik, kérje meg a felhasználót, hogy az Alkalmazásengedélyek jóváhagyása előtt. Bár ez nem feltétlenül szükséges, segíthet intuitívabb környezetet biztosít a felhasználók számára. A felhasználó jelentkezik, kövesse a [v2.0 protokoll oktatóanyagok](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Az engedélyek kéréséhez a directory-rendszergazda
Ha készen áll a szervezet felügyeleti engedélyeket kérhet, átirányíthatja a felhasználót a v2.0 *rendszergazdai jóváhagyás végpontja*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |A directory-bérlőhöz, amelyet szeretne az engedélyt. Ez lehet GUID vagy rövid név formátumban. Ha nem tudja, hogy melyik bérlőhöz, a felhasználó tartozik, és azt szeretné, hogy azok jelentkezzen be minden bérlő, használja a `common`. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| redirect_uri |Szükséges |Az átirányítási URI-t a válasz az alkalmazás kezelni kell elküldeni kívánt helyre. Ez pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie, és további szegmensek veheti fel. |
| state |Ajánlott |Egy érték, amely tartalmazza a kérés is, hogy a jogkivonat választ adja vissza. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |

Ezen a ponton az Azure ad-ben kikényszeríti, hogy csak a bérlői rendszergazda bejelentkezhet a kérés teljesítéséhez. A rendszergazda jóváhagyása kért az alkalmazáshoz a regisztrációs portálon alkalmazás közvetlen alkalmazás engedélyeket kell adnia.

##### <a name="successful-response"></a>A sikeres válasz
Ha a rendszergazda az alkalmazás engedélyeit jóváhagyja, a sikeres válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- | --- |
| bérlő |A directory-bérlővel, amely az alkalmazás a engedélyekkel, amely azt kéri, GUID formátumú. |
| state |Egy érték, amely tartalmazza a kérés is, hogy a jogkivonat választ adja vissza. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |
| admin_consent |Állítsa be **igaz**. |

##### <a name="error-response"></a>Hiba történt a válasz
Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- | --- |
| error |Egy hibakód karakterláncát, amely segítségével besorolása a hibákat, és amelyek használatával reagálhat a hibákat. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hiba kiváltó okának azonosításához. |

Sikeres válasz kapott az alkalmazás üzembe helyezési végpontról, miután az alkalmazás, amely a kért közvetlen Alkalmazásengedélyek szerzett. Most már a kívánt erőforrás jogkivonatot kérhet.

## <a name="get-a-token"></a>Egy token beszerzése
Az alkalmazás beszerzése szükséges engedélyt, után folytassa a hozzáférési tokenek beszerzése az API-k. Egy token beszerzése által az ügyfél hitelesítő adatok, küldjön egy POST kérelmet a `/token` v2.0-végpont:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkos kulcsot a hozzáférési jogkivonat kérése

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
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| bérlő |Szükséges | A directory-bérlő az alkalmazás tervek GUID Azonosítóját vagy a tartománynév formátumban való működésre. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| scope |Szükséges |Az átadott érték a `scope` paraméter a kéréshez a kívánt, elhelyezni, az erőforrást az erőforrás-azonosító (Alkalmazásazonosító URI-ja) kell lennie a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. Ez az érték tájékoztatja, hogy az összes közvetlen alkalmazás engedélyt már konfigurálta az alkalmazást, azt kell jogkivonatok kiállítása az erőforrással használni kívánt azokat a a v2.0-végpontra. |
| client_secret |Szükséges |Az alkalmazás titkos az alkalmazás az alkalmazás regisztrációs portálon létrehozott. A titkos ügyfélkulcsot kell URL-kódolású elküldése előtt.|
| grant_type |Szükséges |Meg kell `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>A második eset: a tanúsítványhoz a hozzáférési jogkivonat kérése

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
| bérlő |Szükséges | A directory-bérlő az alkalmazás tervek GUID Azonosítóját vagy a tartománynév formátumban való működésre. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| scope |Szükséges |Az átadott érték a `scope` paraméter a kéréshez a kívánt, elhelyezni, az erőforrást az erőforrás-azonosító (Alkalmazásazonosító URI-ja) kell lennie a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. Ez az érték tájékoztatja, hogy az összes közvetlen alkalmazás engedélyt már konfigurálta az alkalmazást, azt kell jogkivonatok kiállítása az erőforrással használni kívánt azokat a a v2.0-végpontra. |
| client_assertion_type |Szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Szükséges | Egy helyességi feltétel (egy JSON Web Token) létrehozására és aláírására a tanúsítványt igénylő regisztrált hitelesítő adatként az alkalmazáshoz. További információ [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) megtudhatja, hogyan regisztrálhat a tanúsítvány és a helyességi feltétel formátumát.|
| grant_type |Szükséges |Meg kell `client_credentials`. |

Figyelje meg, hogy paraméterei szinte teljesen megegyezik a kérés által közös titkos kulcsot is azzal a különbséggel, hogy a titkos ügyfélkódot paraméter váltotta fel két paramétert: egy client_assertion_type és client_assertion.

### <a name="successful-response"></a>A sikeres válasz
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
| access_token |A kért hozzáférési jogkivonatot. Az alkalmazás a jogkivonat használatával hitelesíteni a védett erőforráshoz, például egy webes API-nak. |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott `bearer`. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |

### <a name="error-response"></a>Hiba történt a válasz
Egy hibaválasz a következőhöz hasonló:

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
| error |Egy hibakód karakterláncát besorolása a fellépő hibákat, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segíthetnek hitelesítési hiba kiváltó okának azonosításához. |
| error_codes |STS-specifikus hibakódok, melyek segíthetnek a diagnostics használatával listája. |
| időbélyeg |Az idő, a hiba történt. |
| trace_id |A kérelem, melyek segíthetnek a diagnostics használatával egyedi azonosítója. |
| correlation_id |A kérelemhez, amely a különböző összetevők segíthetnek a diagnosztikai egyedi azonosítója. |

## <a name="use-a-token"></a>Egy token
Most, hogy egy jogkivonatot, melyeket beszerezett, használja a jogkivonatot, hogy az erőforrás kéréseket. Ha a jogkivonat lejár, ismételje meg a kérést a `/token` végpont egy új hozzáférési jogkivonat beszerzése.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Kódminta
Egy alkalmazás például, hogy az ügyfél hitelesítő adatai megadják a rendszergazda segítségével valósítja meg hozzájárulás végpont, olvassa el a [v2.0 démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
