---
title: Használja a Microsoft identity platform felhasználói beavatkozás nélkül biztonságos erőforrások eléréséhez |} Az Azure
description: Az OAuth 2.0 hitelesítési protokoll a Microsoft identity platform végrehajtása az webes alkalmazásokat hozhat létre.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6aed38c8c670c751ee51de95e6622685caea1ce
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500923"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>A Microsoft identity platform és az OAuth 2.0 ügyfél-hitelesítési folyamata

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Használhatja a [OAuth 2.0-ügyfél hitelesítő adatainak megadása](https://tools.ietf.org/html/rfc6749#section-4.4) RFC 6749, más néven megadott *két Egyszárú OAuth*, web-ban üzemeltetett erőforrások eléréséhez az alkalmazás identitását. Támogatás az ilyen kiszolgálók közötti kapcsolatok definiálására, amelyeket futtatnia kell a háttérben, egy felhasználóval azonnali beavatkozás nélkül gyakran használják. Ilyen típusú alkalmazások gyakran nevezik *démonok* vagy *szolgáltatásfiókok*.

Az OAuth 2.0 ügyfél-hitelesítő adatok megadása a folyamat lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) a saját hitelesítő adatait, használja a felhasználó megszemélyesítése helyett egy másik webszolgáltatás hívásakor. Ebben a forgatókönyvben az ügyfél nem általában egy középső rétegű webszolgáltatás, egy démon, illetve egy webhelyen. A magasabb szintű megbízhatóságra a Microsoft identity platform is lehetővé teszi, hogy a hívó szolgáltatás (és nem egy közös titkos kulcsot) egy tanúsítvány használandó hitelesítő adatokat.

> [!NOTE]
> A Microsoft identity platform végpont nem támogatja az összes Azure AD-forgatókönyvek és funkciók. Annak megállapításához, hogy a Microsoft identity platform végpontot kell használnia, olvassa el [a Microsoft identity platform korlátozásai](active-directory-v2-limitations.md).

A több jellemző *három Egyszárú OAuth*, egy ügyfélalkalmazás egy erőforrás eléréséhez egy adott felhasználó nevében engedélyt kap. Az engedély van átadva a felhasználó az alkalmazáshoz, során általában a [hozzájárulás](v2-permissions-and-consent.md) folyamat. Azonban az ügyfél-hitelesítő adatok (*két Egyszárú OAuth*) flow-jogosultságokkal közvetlenül magának az alkalmazásnak. Az alkalmazás megjeleníti az erőforrás egy tokent, ha az erőforrás tesz kötelezővé, hogy maga az alkalmazás végrehajtására egy műveletet, és nem a felhasználó rendelkezik-e.

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes ügyfél hitelesítési folyamata a következő ábra hasonlóan néz ki. A cikk későbbi részében lépések mindegyikét ismertetünk.

![Az ügyfélhitelesítő adatok folyamata](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Közvetlen engedélyezési beolvasása

Egy alkalmazás általában kap közvetlen engedélyezési kétféleképpen lévő erőforrások eléréséhez:

* [Hozzáférés-vezérlési lista (ACL) az erőforráscsoportok segítségével](#access-control-lists)
* [Alkalmazás engedélyek hozzárendelése az Azure AD használatával](#application-permissions)

Két módszer közül a leggyakrabban használt Azure AD-ben és javasoljuk, hogy azok az ügyfelek és az erőforrást, hajtsa végre az ügyfél hitelesítő adatokat a folyamat. Erőforrás engedélyezéséhez az ügyfeleknek más módon is beállíthatja. Minden egyes erőforrás-kiszolgáló is válassza ki a módszert, amelyet a legésszerűbb az alkalmazásához.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák

Erőforrás-szolgáltató léptethet egy jogosultsági ellenőrzés alkalmazás (ügyfél), hogy tudja, és a egy bizonyos szintű hozzáférést biztosít azonosítók listája alapján. Amikor az erőforrás egy jogkivonatot kap a Microsoft identity platform végpontról, dekódolni a jogkivonatot, és bontsa ki az ügyfél Alkalmazásazonosítója a `appid` és `iss` jogcímeket. Ezután összeveti az alkalmazás-hozzáférés-vezérlési lista (ACL), amely a karbantartott ellen. Az ACL granularitási és módszer jelentősen eltérő lehet erőforrások között.

Egy gyakori alkalmazási helyzet, hogy az ACL-webalkalmazás vagy webes API-hoz, a tesztek futtatásához. A webes API-t is biztosít egy adott ügyfél előfordulhat, hogy teljes körű engedélyeket csak egy részhalmazát. Teljes körű teszteket futtatni az API-t, hozzon létre egy tesztügyfél szerez be a Microsoft identity platform végpont származó jogkivonatokat, majd elküldi azokat az API-t. Az API-t ezután ellenőrzi az ACL-t a tesztügyfél Alkalmazásazonosítót az API teljes funkcióinak teljes körű hozzáférést. Ha az ilyen típusú ACL-t használ, mindenképpen ellenőrizze, nem csak a hívó `appid` érték, de is ellenőrizze, hogy a `iss` a jogkivonat értéke megbízható.

Ez a hitelesítési típus démonok és szolgáltatásfiókok, felhasználói személyes Microsoft-fiókkal rendelkező felhasználók tulajdonában lévő adatok elérését igénylő gyakori. A szervezet tulajdonában lévő adatokat javasoljuk, hogy megjelenik-e a szükséges jogosultsági Alkalmazásengedélyek keresztül.

### <a name="application-permissions"></a>Alkalmazásengedélyek

ACL-ek helyett a API-k segítségével teszi közzé a alkalmazást engedélyek egy készletét. Egy alkalmazás engedélyt kap egy alkalmazás által a szervezet rendszergazdája, és csak az adott szervezet és az alkalmazottak tulajdonában lévő adatok eléréséhez használható. Például a Microsoft Graph tesz elérhetővé számos Alkalmazásengedélyek tegye a következőket:

* E-mailek olvasása az összes postaládában
* E-mailek olvasása és írása az összes postaládában
* E-mailek küldése bármely felhasználó nevében
* Címtáradatok olvasása

Alkalmazásengedélyek kapcsolatos további információkért látogasson el [Microsoft Graph](https://developer.microsoft.com/graph).

Alkalmazásengedélyek használata az alkalmazásban, a következő szakaszokban ismertetett lépésekkel.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon az engedélyek kéréséhez

1. Regisztrálja, és hozzon létre egy alkalmazást az új keresztül [alkalmazásregisztrációk (előzetes verzió) élmény](quickstart-register-app.md).
2. Nyissa meg az alkalmazás az App regisztrációk (előzetes verzió) felületen. Keresse meg a **tanúsítványok és titkos kulcsok** szakaszt, és adjon hozzá egy **új titkos ügyfélkulcsot**, mert szüksége lesz legalább egy ügyfélkulcsot a jogkivonat kéréséhez.
3. Keresse meg a **API-engedélyek** szakaszt, és adja hozzá a **Alkalmazásengedélyek** az alkalmazásának.
4. **Mentés** az alkalmazás regisztrációját.

#### <a name="recommended-sign-the-user-into-your-app"></a>Ajánlott: A felhasználó bejelentkeznie az alkalmazásba

Általában Alkalmazásengedélyek használó alkalmazás létrehozását, ha az alkalmazás vagy van szükség egy lap, amelyen a rendszergazda hagyja jóvá az Alkalmazásengedélyek megtekintése. Ezen az oldalon az alkalmazás bejelentkezési folyamata, az app-beállításokban, részei lehetnek, vagy egy dedikált "Csatlakozás" folyamat lehet. Sok esetben logikus jelenjen meg ez az alkalmazás "Csatlakozás" nézet csak akkor, ha egy felhasználó munkahelyi vagy iskolai Microsoft-fiókkal van bejelentkezve.

Ha az alkalmazás a felhasználó bejelentkezik, azonosíthatja a szervezet, amelyhez a felhasználó tartozik, kérje meg a felhasználót, hogy az Alkalmazásengedélyek jóváhagyása előtt. Bár ez nem feltétlenül szükséges, segíthet intuitívabb környezetet biztosít a felhasználók számára. A felhasználó jelentkezik, kövesse a [a Microsoft identity platform protokoll oktatóanyagok](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Az engedélyek kéréséhez a directory-rendszergazda

Ha készen áll a szervezet felügyeleti engedélyeket kérhet, átirányíthatja a felhasználó a Microsoft identity platform *rendszergazdai jóváhagyás végpontja*.

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása! (A saját alkalmazás-Azonosítóját használja a legjobb eredmények elérése érdekében – oktatóanyag alkalmazása nem fog hasznos engedélyek kéréséhez.) [![A Postmanben futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

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
| `tenant` | Szükséges | A directory-bérlőhöz, amelyet szeretne az engedélyt. Ez lehet GUID vagy rövid név formátumban. Ha nem tudja, hogy melyik bérlőhöz, a felhasználó tartozik, és azt szeretné, hogy azok jelentkezzen be minden bérlő, használja a `common`. |
| `client_id` | Szükséges | A **Alkalmazásazonosítót (ügyfél)** , amely a [az Azure-portál – alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt felhasználói élményt. |
| `redirect_uri` | Szükséges | Az átirányítási URI-t a válasz az alkalmazás kezelni kell elküldeni kívánt helyre. Ez pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie, és további szegmensek veheti fel. |
| `state` | Ajánlott | Egy érték, amely a kérelemhez, amely a token válaszban visszaadott is megtalálható. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |

Ezen a ponton az Azure AD érvényesíti, amely csak a bérlői rendszergazda teljes körű be tud jelentkezni a kérelmet. A rendszergazda jóváhagyása kért az alkalmazáshoz a regisztrációs portálon alkalmazás közvetlen alkalmazás engedélyeket kell adnia.

##### <a name="successful-response"></a>A sikeres válasz

Ha a rendszergazda az alkalmazás engedélyeit jóváhagyja, a sikeres válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- |
| `tenant` | A directory-bérlővel, amely az alkalmazás a engedélyekkel, amely azt kéri, GUID formátumú. |
| `state` | Egy érték, amely tartalmazza a kérés is, hogy a jogkivonat választ adja vissza. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolás előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet szolgál. |
| `admin_consent` | Állítsa be **igaz**. |

##### <a name="error-response"></a>Hiba történt a válasz

Ha a rendszergazda nem hagyja jóvá az alkalmazás engedélyeit, a sikertelen válasz a következőhöz hasonló:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Egy hibakód karakterláncát, amely segítségével besorolása a hibákat, és amelyek használatával reagálhat a hibákat. |
| `error_description` | Egy adott hibaüzenet, amelyek segítségével hiba kiváltó okának azonosításához. |

Sikeres válasz kapott az alkalmazás üzembe helyezési végpontról, miután az alkalmazás, amely a kért közvetlen Alkalmazásengedélyek szerzett. Most már a kívánt erőforrás jogkivonatot kérhet.

## <a name="get-a-token"></a>Egy token beszerzése

Az alkalmazás beszerzése szükséges engedélyt, után folytassa a hozzáférési tokenek beszerzése az API-k. Egy token beszerzése által az ügyfél hitelesítő adatok, küldjön egy POST kérelmet a `/token` a Microsoft identity platform végpont:

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása! (A saját alkalmazás-Azonosítóját használja a legjobb eredmények elérése érdekében – oktatóanyag alkalmazása nem fog hasznos engedélyek kéréséhez.) [![A Postmanben futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: A közös titkos kulcsot a hozzáférési jogkivonat kérése

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
| `tenant` | Szükséges | A directory-bérlő az alkalmazás tervek GUID Azonosítóját vagy a tartománynév formátumban való működésre. |
| `client_id` | Szükséges | Az Alkalmazásazonosító, amely az alkalmazás hozzá van rendelve. Ezt az információt találja a portálon, ahol regisztrálta az alkalmazást. |
| `scope` | Szükséges | Az átadott érték a `scope` paraméter a kéréshez a kívánt, elhelyezni, az erőforrást az erőforrás-azonosító (Alkalmazásazonosító URI-alkalmazás) kell lennie a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. <br/>Ez az érték jelzi, hogy az összes közvetlen alkalmazás engedélyt már konfigurálta az alkalmazást, a végpontot kell jogkivonatok kiállítása az erőforrással használni kívánt azokat a a Microsoft identity platform végpont. További információkat talál a `/.default` hatókörét, tekintse meg a [dokumentáció hozzájárulás](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Szükséges | Az ügyfél titkos kulcsát az alkalmazás az alkalmazás regisztrációs portálon létrehozott. A titkos ügyfélkulcsot kell URL-kódolású elküldése előtt. |
| `grant_type` | Szükséges | Meg kell `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat kérése tanúsítvánnyal

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
| `tenant` | Szükséges | A directory-bérlő az alkalmazás tervek GUID Azonosítóját vagy a tartománynév formátumban való működésre. |
| `client_id` | Szükséges |Az alkalmazás (ügyfél) azonosítója, amely az alkalmazás hozzá van rendelve. |
| `scope` | Szükséges | Az átadott érték a `scope` paraméter a kéréshez a kívánt, elhelyezni, az erőforrást az erőforrás-azonosító (Alkalmazásazonosító URI-alkalmazás) kell lennie a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. <br/>Ez az érték tájékoztatja, hogy az összes közvetlen alkalmazás engedélyt már konfigurálta az alkalmazást, azt kell jogkivonatok kiállítása az erőforrással használni kívánt azokat az a Microsoft identity platform végpont. További információkat talál a `/.default` hatókörét, tekintse meg a [dokumentáció hozzájárulás](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Szükséges | Az értéket kell beállítani `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Szükséges | Egy helyességi feltétel (egy JSON webes jogkivonat) létrehozására és aláírására a tanúsítványt igénylő regisztrált hitelesítő adatként az alkalmazáshoz. További információ [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) megtudhatja, hogyan regisztrálhat a tanúsítvány és a helyességi feltétel formátumát.|
| `grant_type` | Szükséges | Meg kell `client_credentials`. |

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
| `access_token` | A kért hozzáférési jogkivonatot. Az alkalmazás a jogkivonat használatával hitelesíteni a védett erőforráshoz, például egy webes API-nak. |
| `token_type` | Typ tokenu értékét jelöli. Csak írja be, hogy a Microsoft identity platform támogatja a `bearer`. |
| `expires_in` | Az, hogy mennyi ideig hozzáférési jogkivonat érvénytelen (másodpercben). |

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
| `error` | Egy hibakód karakterláncát besorolása a fellépő hibákat, és reagálhat a hibák használható. |
| `error_description` | Egy adott hibaüzenet, amelyek segíthetnek hitelesítési hiba kiváltó okának azonosításához. |
| `error_codes` | STS-specifikus hibakódok, melyek segíthetnek a diagnostics használatával listája. |
| `timestamp` | A hiba ideje. |
| `trace_id` | A kérelem a diagnostics használatával segítségével egyedi azonosítója. |
| `correlation_id` | A kérelem a diagnostics használatával különböző összetevők segítségével egyedi azonosítója. |

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

## <a name="code-samples-and-other-documentation"></a>Kódminták és az egyéb dokumentáció

Olvassa el a [ügyfél hitelesítő adatai – áttekintés dokumentáció](https://aka.ms/msal-net-client-credentials) , a Microsoft-hitelesítési tár

| Sample | Platform |Leírás |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1 Console | Egy egyszerű .NET Core-alkalmazás, amely jelennek meg a felhasználók egy bérlő lekérdezése a Microsoft Graph használatával az alkalmazás identitását helyett egy felhasználó nevében. A minta is mutatja be, a módosítás a hitelesítéshez tanúsítványokat használ. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET, MVC | Egy webalkalmazást, amely szinkronizálja az adatokat a Microsoft Graph használatával az alkalmazás identitását helyett egy felhasználó nevében. |
