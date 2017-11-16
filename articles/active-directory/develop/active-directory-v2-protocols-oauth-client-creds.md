---
title: "Az Azure AD v2.0 használják a felhasználói beavatkozás nélkül biztonságos erőforrások eléréséhez |} Microsoft Docs"
description: "A webalkalmazások Azure AD végrehajtása az OAuth 2.0 hitelesítési protokoll használatával."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 93b54c3fc4397573f77b2e157c6f1866786690da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Az Azure Active Directory v2.0 és az OAuth 2.0 ügyfél hitelesítő adatok folyamata
Használhatja a [OAuth 2.0 ügyfél hitelesítő adatai megadják](http://tools.ietf.org/html/rfc6749#section-4.4), más néven *két Egyszárú OAuth*, az alkalmazáshoz a webkiszolgáló által szolgáltatott erőforrások eléréséhez. Gyakran engedélyezze az ilyen típusú kiszolgálók – olyan műveleteket, amelyek kell a háttérben futnak, a felhasználó azonnali közreműködése nélkül szolgál. Ilyen típusú alkalmazások gyakran nevezik *démonok* vagy *szolgáltatásfiókok*.

> [!NOTE]
> A v2.0-végpontra nem támogatja, minden Azure Active Directory forgatókönyvek és funkciók. Annak megállapításához, hogy a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

A tipikus több *három Egyszárú OAuth*, ügyfélalkalmazás engedélyt elért egy erőforrást egy adott felhasználó nevében. Az engedély delegált a felhasználótól az alkalmazást, általában során a [hozzájárulás](active-directory-v2-scopes.md) folyamat. Azonban az ügyfél-hitelesítő adatok folyamatában jogosultságokkal közvetlenül magának az alkalmazásnak. Ha az alkalmazás megadja egy erőforrást, az erőforrás jogkivonat érvényesíti, amely az alkalmazás maga engedélyezési végrehajtani a műveletet, és hogy a nem a felhasználó rendelkezik-e engedélyezési.

## <a name="protocol-diagram"></a>Protokoll diagramja
A teljes ügyfél-hitelesítő adatok folyamata a következő ábra hasonlít. Azt írja le a cikk lépéseit.

![Ügyfél hitelesítő adatai](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Közvetlen engedélyezési beolvasása
Az alkalmazások általában kap közvetlen engedélyezési elért egy erőforrást az alábbi két módszer egyikével: az erőforráscsoportok hozzáférés-vezérlési listaként (ACL) vagy alkalmazás engedély-hozzárendelést az Azure Active Directory (Azure AD) révén. Két módszer közül a leggyakrabban használt Azure AD-ben, és azt javasoljuk azok az ügyfelek és az erőforrásokat, hajtsa végre az ügyfél hitelesítő adatok folyamata. Egy erőforrás azonban engedélyezi az ügyfeleknek más módon választhat. Minden erőforrás-kiszolgáló a módszert, amelyet az alkalmazás számára a legtöbb legjobb választhat.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Egy erőforrás-szolgáltató kényszerítése előfordulhat, hogy egy jogosultsági ellenőrzés, hogy tudja, és engedélyezi a hozzáférést a valamilyen konkrét szintje Alkalmazásazonosítók listája alapján. Amikor az erőforrás egy tokent kap a v2.0-végpontra, dekódolni a jogkivonatot, és bontsa ki az ügyfél Alkalmazásazonosítót a a `appid` és `iss` jogcímeket. Ezután összeveti a hozzáférés-vezérlési Listában, amely a karbantartott alkalmazást. A hozzáférés-vezérlési lista granularitási és metódus változhat, jelentősen erőforrások között.

Gyakori használati eset, hogy használja a hozzáférés-vezérlési Listában webalkalmazás vagy egy webes API tesztek futtatásához. A Web API előfordulhat, hogy a teljes körű engedélyeket csak egy részhalmazát jogot konkrét ügyfélhez. Az API-végpontok közötti meghagy, hozzon létre teszt ügyfél szerez be a v2.0-végpontra származó jogkivonatokat, majd elküldi azokat az API-t. Az API-t ellenőrzi az ACL teljes körű hozzáférés az API-t teljes funkciót az a teszt ügyfél Azonosítóját. Ha ilyen típusú hozzáférés-vezérlési lista használata esetén ügyeljen arra, hogy nem csak a hívó érvényesítése `appid` érték. Is ellenőrzi, hogy a `iss` a token értéke megbízható.

Ez a hitelesítési típus közös démonok és szolgáltatásfiókokat, el kell érniük a fogyasztói személyes Microsoft-fiókkal rendelkező felhasználók tulajdonában lévő adatokat. A szervezet tulajdonában lévő adatokat azt javasoljuk, hogy alkalmazás-engedélyek szükséges engedélyt kapott.

### <a name="application-permissions"></a>Alkalmazásengedélyek
Hozzáférés-vezérlési listák helyett API-k segítségével teszi közzé az alkalmazást engedélyekkel. Egy alkalmazás engedélyt kap egy alkalmazás egy szervezet rendszergazdája, és csak az adott szervezet és az alkalmazottak tulajdonában lévő adatokat eléréséhez használható. Például a Microsoft Graph több alkalmazás engedélyek a következő mutatja:

* E-mailek olvasása az összes postaládában
* E-mailek olvasása és írása az összes postaládában
* E-mailek küldése bármely felhasználó nevében
* Címtáradatok olvasása

Alkalmazásengedélyek kapcsolatos további információkért látogasson el [Microsoft Graph](https://graph.microsoft.io).

Alkalmazásengedélyek az alkalmazás használatához hajtsa végre a lépéseket, a következő szakaszokban arról lesz szó.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Az alkalmazás regisztrációs portálon engedélyek kéréséhez
1. Keresse fel az alkalmazást a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy [hozzon létre egy alkalmazást](active-directory-v2-app-registration.md), ha még nem tette meg. Szüksége lesz legalább egy alkalmazás titkos kulcs használatára, az alkalmazás létrehozásakor.
2. Keresse meg a **közvetlen Alkalmazásengedélyek** szakaszt, és adja meg az alkalmazáshoz szükséges engedélyeket.
3. **Mentés** az alkalmazás regisztrálása.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Ajánlott: Beléptetni a felhasználót az alkalmazáshoz
Általában Alkalmazásengedélyek használó alkalmazás építésekor szükséges az alkalmazáshoz egy lap vagy nézet, amelyen a rendszergazda az Alkalmazásengedélyek jóváhagyja. Ezen a lapon lehet az alkalmazás bejelentkezési folyamata, az alkalmazás beállításokban része, vagy egy dedikált "Csatlakozás" folyamat lehet. Sok esetben érdemes ezt az alkalmazást a "Csatlakozás" nézet csak, miután egy felhasználó a munkahelyi vagy iskolai Microsoft-fiókkal van bejelentkezve.

Ha bejelentkezik a felhasználó az alkalmazáshoz, a szervezet, amelyhez a felhasználó tartozik, kérje meg, hogy az Alkalmazásengedélyek jóváhagyása előtt azonosíthatja. Bár nem feltétlenül szükséges, ez segíthet a felhasználók intuitívabb környezetet. Írja alá a felhasználót, hajtsa végre a [v2.0 protokoll oktatóanyagok](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Az engedélyeket kérhet a directory-rendszergazda
Amikor készen áll a szervezet felügyeleti engedélyeket kérhet, akkor is átirányítja a felhasználót a v2.0 *rendszergazda jóváhagyását végpont*.

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

| Paraméter | Az állapot | Leírás |
| --- | --- | --- |
| Bérlői |Szükséges |A directory-bérlőt, amelyet az engedélyt. Ez lehet GUID vagy rövid név formátumban. Ha nem biztos lehet bérlői a felhasználó tagja, és azt szeretné, hogy azok jelentkezzen be minden bérlő, használjon, amely `common`. |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz hozzárendelni. |
| redirect_uri |Szükséges |Az átirányítási URI, ha azt szeretné, hogy a válasz küldését az alkalmazások kezeléséhez. Az pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált azzal a különbséggel, hogy az URL-kódolású kell lennie, és további szegmenst veheti fel. |
| state |Ajánlott |Egy érték, amely megtalálható a kérelem a biztonságijogkivonat-válaszban is visszaadott. Bármely, a kívánt tartalmat karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a használatos. |

Ezen a ponton az Azure AD kényszeríti annak engedélyezése, hogy csak a bérlői rendszergazda jelentkezhetnek be a kérés teljesítéséhez. A rendszergazda jóváhagyása az alkalmazást az app-regisztrálási portál a kért közvetlen alkalmazás engedélyeket kell adnia.

##### <a name="successful-response"></a>A sikeres válasz
Ha a rendszergazda engedélyeit, az alkalmazás jóváhagyása, a sikeres válasz jelenik meg:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paraméter | Leírás |
| --- | --- | --- |
| Bérlői |A directory-bérlőhöz, amely engedéllyel rendelkezik az alkalmazás a kért, GUID formátumban. |
| state |Egy érték, amely megtalálható a kérelem a biztonságijogkivonat-válaszban is visszaadott. Bármely, a kívánt tartalmat karakterlánc lehet. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a használatos. |
| admin_consent |Beállítása **igaz**. |

##### <a name="error-response"></a>Hibaválaszba
Ha a rendszergazda nem hagyja jóvá az engedélyeket az alkalmazáshoz, a sikertelen válasz jelenik meg:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paraméter | Leírás |
| --- | --- | --- |
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a hibákat, és amely hibák reagálni használhatja. |
| error_description |Egy adott hibaüzenet, amelyik segíthet a hiba alapvető oka azonosítása. |

Miután az alkalmazás üzembe helyezési végpont már a sikeres válasz érkezett, az alkalmazás köszönhetően a kért közvetlen Alkalmazásengedélyek. Most már a kívánt erőforráshoz tartozó jogkivonatot kérhet.

## <a name="get-a-token"></a>A jogkivonat beolvasása
Miután az alkalmazás jut hozzá a szükséges engedélyt, folytatásához a hozzáférési tokenek beszerzése API-k esetében. Ahhoz, hogy a jogkivonatot az ügyfél hitelesítő adatok megadása, küldött POST kérelmet a `/token` v2.0-végponttal:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkot a hozzáférési token kérelem

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paraméter | Az állapot | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz hozzárendelni. |
| Hatókör |Szükséges |Átadott értéknek a `scope` a kérésben paraméternek kell lennie az erőforrás-azonosító (alkalmazás azonosítója URI), a kívánt, elhelyezni az erőforrás a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. Ez az érték tájékoztatja arról, hogy minden közvetlen alkalmazás engedélyeit az alkalmazás már konfigurálta, akkor kell ki a használni kívánt erőforráshoz tartozó megfelelően a jogkivonat a v2.0-végponttól. |
| client_secret |Szükséges |Az alkalmazás az app-regisztrációs portálon létrehozott Alkalmazáskulcsot. |
| grant_type |Szükséges |Kell `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>A második esetben: hozzáférési jogkivonat kérelem tanúsítvánnyal

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

| Paraméter | Az állapot | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz hozzárendelni. |
| Hatókör |Szükséges |Átadott értéknek a `scope` a kérésben paraméternek kell lennie az erőforrás-azonosító (alkalmazás azonosítója URI), a kívánt, elhelyezni az erőforrás a `.default` utótag. A Microsoft Graph például értéke `https://graph.microsoft.com/.default`. Ez az érték tájékoztatja arról, hogy minden közvetlen alkalmazás engedélyeit az alkalmazás már konfigurálta, akkor kell ki a használni kívánt erőforráshoz tartozó megfelelően a jogkivonat a v2.0-végponttól. |
| client_assertion_type |Szükséges |Az értéknek kell lennie`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Szükséges | Egy helyességi feltétel (egy JSON Web Token) hozzon létre, és írja alá a tanúsítványt igénylő regisztrálta hitelesítő adatként az alkalmazáshoz. További információ a [tanúsítvány a hitelesítő adatok](active-directory-certificate-credentials.md) megtudhatja, hogyan kell regisztrálni a tanúsítványt, és a helyességi feltétel formátuma.|
| grant_type |Szükséges |Kell `client_credentials`. |

Figyelje meg, hogy a paraméterek megegyeznek-szinte közös titkos kulcs kérése gazdabuszadaptereken azzal a különbséggel, hogy a client_secret paraméter helyébe két paramétert: egy client_assertion_type és client_assertion.

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
| access_token |A kért hozzáférési jogkivonat. Az alkalmazás a token használatával hitelesíteni a védett erőforrások, például egy webes API. |
| token_type |A jogkivonat típusa értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott `bearer`. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |

### <a name="error-response"></a>Hibaválaszba
Egy hiba történt egy válasz így néz ki:

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
| error |Egy hiba kód karakterlánc, amely a besorolására a felmerülő hibákat, és reagálni hibákat is használhatja. |
| error_description |Egy adott hibaüzenet, amelyek segíthetnek a hitelesítési hiba okának azonosításához. |
| error_codes |Diagnosztika segíthet STS-specifikus hibakódok listáját. |
| időbélyeg |Az az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segíthetnek a diagnosztika egyedi azonosítója. |
| correlation_id |A kérelemhez, amely segíthet diagnosztika összetevői között egyedi azonosítója. |

## <a name="use-a-token"></a>Használja a tokent
Most, hogy jut hozzá a jogkivonatot, a jogkivonat segítségével küld kérelmeket az erőforráshoz. Amikor a jogkivonat lejár, ismételje meg a kérést a `/token` végpont egy friss hozzáférési jogkivonat beszerzése.

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
Egy alkalmazás például, hogy az ügyfél hitelesítő adatai megadják a rendszergazda használatával valósít meg hozzájárulás végpont, olvassa el a [v2.0 démon kódminta](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
