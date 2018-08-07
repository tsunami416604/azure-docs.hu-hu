---
title: Az Azure AD v2.0 OAuth-engedélyezési kód Flow |} A Microsoft Docs
description: Webes alkalmazások létrehozása az OAuth 2.0 hitelesítési protokoll megvalósítását az Azure AD használatával.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1e8807030cc1e08a41bfb6c1e8d2e1fe2b2432c7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581665"
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>2.0-s protokollok – az OAuth 2.0 hitelesítési Kódfolyamat
Az OAuth 2.0 hitelesítési kódmegadás alkalmazást, amely egy eszközön a védett erőforrások, például a webes API-k eléréséhez használható. Az app model v2.0 megvalósítását az OAuth 2.0 használatával, hozzáadhat jelentkezzen be, és az API eléréséhez a mobil- és asztali alkalmazásokhoz. Ez az Útmutató nyelvtől független, és ismerteti, hogyan küldhetők és fogadhatók HTTP-üzenetek bármelyikének használata nélkül a [Azure nyílt forráskódú hitelesítési tárak](active-directory-authentication-libraries.md).

> [!NOTE]
> Nem minden Azure Active Directory-forgatókönyvet és funkciót támogatja a v2.0-végpontra. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

Az OAuth 2.0 hitelesítési kódfolyamat leírt [, az OAuth 2.0 ismertetőjének 4.1 szakaszában](http://tools.ietf.org/html/rfc6749). A legtöbb alkalmazástípust, beleértve a hitelesítési és engedélyezési végrehajtásához használatos [webes alkalmazások](active-directory-v2-flows.md#web-apps) és [natív módon telepített alkalmazások](active-directory-v2-flows.md#mobile-and-native-apps). A folyamat lehetővé teszi, hogy az alkalmazások biztonságos beszerezni a v2.0-végpont által védett erőforrások eléréséhez használható access_tokens. 

## <a name="protocol-diagram"></a>Protokoll diagramja
Magas szinten a teljes hitelesítési folyamat egy natív/mobile alkalmazás egy kicsit nézhet ki:

![OAuth hitelesítési Kódfolyamat](./media/v2-oauth2-auth-code-flow/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Hozzáférési kód kérése
A hitelesítési kódfolyamat irányítja a felhasználót, hogy az ügyfél kezdődik a `/authorize` végpont. A kéréshez az ügyfél azt jelzi, hogy az engedélyeket kell beszerezni a felhasználó elől:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> A kérelem végrehajtása a hivatkozásra kattintva! Miután bejelentkezett, a böngésző át kell irányítani `https://localhost/myapp/` együtt egy `code` címet a címsorba.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Paraméter             |             | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő                | szükséges    | A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                    |
| client_id             | szükséges    | Az alkalmazás AZONOSÍTÓJÁT, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazás hozzárendelve.  |
| response_type         | szükséges    | Tartalmaznia kell `code` az engedélyezési kód folyamata.       |
| redirect_uri          | Ajánlott | Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat redirect_uri tulajdonsága. Pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve azt az URL-kódolású kell lennie. A natív és mobil alkalmazások esetén az alapértelmezett értéket használjon `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| scope                 | szükséges    | Szóközzel elválasztott listáját [hatókörök](v2-permissions-and-consent.md) , hogy szeretné-e a felhasználót, hogy engedélyt adjanak az.           |
| response_mode         | Ajánlott | Meghatározza a létrejövő jogkivonat vissza küldhet az alkalmazáshoz használandó módszert. Lehet `query`, `fragment`, vagy `form_post`. `query` a kódot biztosít az átirányítási URI-t a lekérdezési sztring paramétereként. Ha Ön a kért egy azonosító jogkivonat, használja az implicit folyamatot, nem használhatja `query` meghatározott a [OpenID specifikációja](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Ha csak a kódot kért, `query`, `fragment`, vagy `form_post`. `form_post` az átirányítási URI-t a kódot egy HOZZÁSZÓLÁSRA hajtja végre. További információ: [OpenID Connect protokollal](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| state                 | Ajánlott | A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat, akinél karakterlánc lehet. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások](http://tools.ietf.org/html/rfc6749#section-10.12). Az érték is az alkalmazás a felhasználói állapot kapcsolatos információkat is kódolása, előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet. |
| parancssor                | választható    | Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg csak érvényes értékei a "bejelentkezés", "none", és a "jóváhagyás". `prompt=login` a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem negating egyszeri bejelentkezéses kényszeríti. `prompt=none` Ellenkező – biztosítja, hogy a felhasználó el minden olyan interaktív kérdés nem egyike. Ha a kérés nem teljesíthető csendes egyszeri bejelentkezéses keresztül, a v2.0-végpont adja vissza egy `interaction_required` hiba. `prompt=consent` Elindítja az OAuth-hozzájárulási párbeszédpanel kéri a felhasználót, hogy az alkalmazás engedélyeket, a felhasználó bejelentkezése után. |
| login_hint            | választható    | Segítségével előre töltse ki a felhasználónév, e-mail-cím mező a bejelentkezési oldal a felhasználó számára, ha ismeri a kívánt időben felhasználóneve. Alkalmazások gyakran ismételt hitelesítés kellene már kinyert a felhasználónevet egy korábbi bejelentkezési használata során fogja használni ezt a paramétert a `preferred_username` jogcím.                                                                                                                                                                                                                                                                                                    |
| domain_hint           | választható    | Lehetnek `consumers` vagy `organizations`. Ha tartalmazza, azt kihagyja az e-mail-alapú felderítési folyamat, hogy a felhasználó végighalad a bejelentkezési lapon v2.0 némileg több zökkenőmentes felhasználói élményt vezet. Gyakran alkalmazásokat fogja használni ezt a paramétert ismételt hitelesítés során oly módon, a `tid` a korábbi bejelentkezési. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`. Ellenkező esetben használjon `domain_hint=organizations`.                                                                                                              |
| code_challenge_method | választható    | Kódolás használt módszer a `code_verifier` számára a `code_challenge` paraméter. Lehetnek `plain` vagy `S256`. Ha ki van zárva, `code_challenge` adatforrásmérete egyszerű szöveges Ha `code_challenge` részét képezi. Egyaránt támogatja a 2.0-s verziójú Azure AAD `plain` és `S256`. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | választható    | Használt kód engedélyezések koncepció kulcs használatával a kód Exchange (PKCE) egy natív ügyfél. Kötelező, ha `code_challenge_method` részét képezi. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                    |

Ezen a ponton a felhasználó kell adnia megadják hitelesítő adataikat, és a hitelesítés végrehajtásához. A v2.0-végpontra is biztosítja, hogy a felhasználó hozzájárult a megadott engedélyeket a `scope` lekérdezési paraméter. Ha a felhasználó nem egyezett bele bármelyik ezeket az engedélyeket, azt fogja kéri a felhasználót az járul hozzá a szükséges engedélyekkel. A részletek [engedélyek, beleegyezése és több-bérlős alkalmazások az itt elérhető](v2-permissions-and-consent.md).

A felhasználó végzi a hitelesítést, és engedélyezi a jóváhagyás után a v2.0-végpont választ küld az alkalmazáshoz, a kijelzett `redirect_uri`, a megadott metódussal a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=query` következőhöz hasonló:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paraméter | Leírás                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kód      | A authorization_code, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód használatával a célként megadott erőforrás-hozzáférési jogkivonat kérése. Authorization_codes nagyon rövid élettartamú, általában körülbelül 10 perc után járnak. |
| state     | Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak.                                            |

#### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is elküldheti az `redirect_uri` , az alkalmazás képes kezelni őket megfelelően:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter         | Leírás                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához.          |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hitelesítési végpont hibák hibakódok
A következő táblázat ismerteti a különböző visszaadható hibakódok a `error` válaszként küldött hibaüzenetben paraméterében.

| Hibakód                | Leírás                                                                                                           | Ügyfélművelet                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Protokollhiba történt, például egy hiányzó kötelező paraméter.                                                               | Javítsa ki, és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában fogott fejlesztési hiba.                                                                                                                                     |
| unauthorized_client       | Az ügyfélalkalmazás számára nem engedélyezett az engedélyezési kódot kér.                                           | Ez a hiba általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| ACCESS_DENIED             | Erőforrás tulajdonosának jóváhagyás elutasítva                                                                                         | Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem lehet folytatni, kivéve, ha a felhasználó.                                                                                                                                               |
| unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja a kód választípusához a kérésben.                                         | Javítsa ki, és küldje el újra a kérelmet. Ez az egy fejlesztési célú kezdeti tesztelés során általában történt hiba.                                                                                                                                     |
| server_error              | A kiszolgáló váratlan hibát észlelt.                                                                         | Ismételje meg a kérelmet. Ezeket a hibákat okozhat az ideiglenes feltételek. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz késik egy ideiglenes hiba.                                                                |
| temporarily_unavailable   | A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése.                                                           | Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik.                                                                                                               |
| invalid_resource          | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem található, vagy azt nem megfelelően van konfigurálva. | Ez a hiba azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával.                                          |
|login_required             | Túl sok vagy nem található felhasználó | Az ügyfél csendes hitelesítés kért (`prompt=none`), de egyetlen felhasználó nem található. Ez azt jelenti, hogy több felhasználó van aktív a munkamenetben, vagy egyik felhasználó sem. Ez figyelembe veszi a kiválasztott bérlő (például, ha az AAD 2-aktív fiókok és a egy MSA és `consumers` ki van választva, beavatkozás nélküli hitelesítés is használható). |
|interaction_required       | A kérelem felhasználói beavatkozást igényel. | Egy további hitelesítési lépés vagy hozzájárulás megadása kötelező. Ismételje meg a kérelmet anélkül `prompt=none`. |

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése
Most, hogy egy authorization_code beszerezett, és engedéllyel rendelkezik a felhasználó által, beválthatja a `code` számára egy `access_token` a kívánt erőforrást. Ehhez küldésével egy `POST` kérelmet a `/token` végpont:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása! (Ne felejtse el lecserélni a `code`) [ ![Postman futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paraméter     |                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő        | szükséges              | A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | szükséges              | Az alkalmazás AZONOSÍTÓJÁT, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazás hozzárendelve.                                                                                                                                                                                                                             |
| grant_type    | szükséges              | Meg kell `authorization_code` az engedélyezési kód folyamata.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | szükséges              | Hatókörök szóközzel elválasztott listáját. A hatókörök, ez a szakasz a kért megfelelő vagy a hatókörök, az első alapját képező kért egy részét kell lennie. Ha az ebben a kérelemben megadott hatókörök span több erőforrás-kiszolgáló, akkor a v2.0-végpont vissza az első hatókörében megadott erőforrás-jogkivonat. A hatókörök részletes magyarázatát, tekintse meg [engedélyek, beleegyezése és hatókörök](v2-permissions-and-consent.md). |
| Kód          | szükséges              | A folyamat első szakasza beszerzett authorization_code.                                                                                                                                                                                                                                                                                                                                                                   |
| redirect_uri  | szükséges              | Az azonos redirect_uri használt értékkel beszerezni a authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | a web apps szükséges | Az alkalmazás titkos, amelyet az alkalmazás az alkalmazás regisztrációs portálon létrehozott. Azt kell nem használható egy natív alkalmazást, mert client_secrets megbízhatóan nem tárolható az eszközökön. Web apps és a webes API-kat, amelynek a titkos ügyfélkódot tárolja biztonságos helyen a kiszolgálói oldalon lehetősége.  A titkos ügyfélkulcsot kell URL-kódolású elküldése előtt.                                                                                                                    |
| code_verifier | választható              | Az azonos code_verifier a authorization_code beszerzéséhez használt. Szükséges, ha az engedélyezési kód engedélyezési kérésben PKCE használt. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>A sikeres válasz
Token sikeres válasz fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paraméter     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | A kért hozzáférési jogkivonatot. Az alkalmazás a jogkivonat használatával hitelesítik magukat a védett erőforráshoz, például a webes API-t.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Typ tokenu értékét jelöli. Az egyetlen típus, amely támogatja az Azure ad-ben tulajdonosi                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | A hatókörök, amely a access_token érvényes.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot a jelenlegi hozzáférési jogkivonat lejárata után szerzi be a további hozzáférési jogkivonatokat. Refresh_tokens hosszú élettartamú, és az erőforrásokhoz való hozzáférés megőrzése hosszabb ideig is használható. További részletekért tekintse meg a [v2.0 jogkivonat referenciái](v2-id-and-access-tokens.md). <br> **Megjegyzés:** csak a megadott if `offline_access` a kért hatókörhöz.                                               |
| id_token      | Az előjel nélküli JSON webes jogkivonat (JWT). Az alkalmazás is base64Url dekódolni a szegmensek a token a bejelentkezett felhasználóval kapcsolatos információkat. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. Id_tokens kapcsolatos további információkért lásd: a [v2.0-végpont jogkivonat referenciái](v2-id-and-access-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` a kért hatókörhöz. |
#### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok ehhez hasonló lesz:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához.          |
| error_codes       | A diagnosztikát segítő STS-specifikus hibakódok listáját.                                                |
| időbélyeg         | Az idő, a hiba történt.                                                                           |
| trace_id          | A kérelem, amelyek segítik a diagnosztikai egyedi azonosítója.                                               |
| correlation_id    | A különböző összetevők a diagnosztikát segítő kérelem egyedi azonosítója.                             |

#### <a name="error-codes-for-token-endpoint-errors"></a>A jogkivonat-végpont hibákat hibakódok
| Hibakód              | Leírás                                                                                                           | Ügyfélművelet                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Protokollhiba történt, például egy hiányzó kötelező paraméter.                                                               | Javítsa ki és küldje el újra a kérelmet                                                                                                                                                                                                                |
| invalid_grant           | Az engedélyezési kód vagy PKCE kód ellenőrző érvénytelen vagy lejárt.                                             | Próbálkozzon egy új kérelmet a `/authorize` végpont, és ellenőrizze, hogy megfelelő volt-e a code_verifier paramétert.                                                                                                                                   |
| unauthorized_client     | A hitelesített ügyfél nem jogosult használni a engedélyezés engedélyezési típusa.                                     | Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| invalid_client          | Ügyfél-hitelesítés nem sikerült.                                                                                        | Az ügyfél-hitelesítő adatok nem érvényesek. Meg az alkalmazás-rendszergazda hitelesítő adatokat frissíti.                                                                                                                                       |
| unsupported_grant_type  | Az engedélyezési kiszolgáló nem támogatja az engedélyezés engedélyezési típusa.                                              | A kérelem az engedélyezési típus módosítása Ezen hibatípus csak a fejlesztés során megtörténik, és a kezdeti tesztelés során észlelt.                                                                                                      |
| invalid_resource        | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem található, vagy azt nem megfelelően van konfigurálva. | Ez azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával.                                           |
| interaction_required    | A kérelem felhasználói beavatkozást igényel. Ha például egy további hitelesítési lépésre szükség.                   | Próbálja megismételni a kérelmet, ugyanazt az erőforrást.                                                                                                                                                                                                  |
| temporarily_unavailable | A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése.                                                            | Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik.                                                                                                                |

## <a name="use-the-access-token"></a>A hozzáférési jogkivonattal
Most, hogy sikeresen szerezte- `access_token`, használhatja a jogkivonatot a webes API-kérések, beleértve a `Authorization` fejléc:

> [!TIP]
> A kérelem végrehajtása a Postmanben! (Cserélje le a `Authorization` fejléc első) [ ![Postman futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>A hozzáférési jogkivonat frissítéséhez
Access_tokens rövid életűek, és frissítenie kell azokat után folytatja az erőforrások eléréséhez. Ha elküldi egy másik megteheti `POST` kérelmet a `/token` végpont, ezúttal biztosítása a `refresh_token` helyett a `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása! (Ne felejtse el lecserélni a `refresh_token`) [ ![Postman futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paraméter     |                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő        | szükséges              | A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                  |
| client_id     | szükséges              | Az alkalmazás AZONOSÍTÓJÁT, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazás hozzárendelve.                                                                                                                                                                                                                                                            |
| grant_type    | szükséges              | Meg kell `refresh_token` esetében ez a hitelesítési kódfolyamat alapját képező.                                                                                                                                                                                                                                                                                                                                                                                                    |
| scope         | szükséges              | Hatókörök szóközzel elválasztott listáját. A hatókörök, ez a szakasz a kért megfelelő vagy a hatókörök, az eredeti authorization_code kérelem alapját képező kért egy részét kell lennie. Ha az ebben a kérelemben megadott hatókörök span több erőforrás-kiszolgáló, akkor a v2.0-végpont vissza az első hatókörében megadott erőforrás-jogkivonat. A hatókörök részletes magyarázatát, tekintse meg [engedélyek, beleegyezése és hatókörök](v2-permissions-and-consent.md). |
| refresh_token | szükséges              | A folyamat második szakasz beszerzett refresh_token.                                                                                                                                                                                                                                                                                                                                                                                                      |
| redirect_uri  | szükséges              | Az azonos redirect_uri használt értékkel beszerezni a authorization_code.                                                                                                                                                                                                                                                                                                                                                                                            |
| client_secret | a web apps szükséges | Az alkalmazás titkos, amelyet az alkalmazás az alkalmazás regisztrációs portálon létrehozott. Azt kell nem használható egy natív alkalmazást, mert client_secrets megbízhatóan nem tárolható az eszközökön. Web apps és a webes API-kat, amelynek a titkos ügyfélkódot tárolja biztonságos helyen a kiszolgálói oldalon lehetősége.                                                                                                                                                    |

#### <a name="successful-response"></a>A sikeres válasz
Token sikeres válasz fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paraméter     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | A kért hozzáférési jogkivonatot. Az alkalmazás a jogkivonat használatával hitelesítik magukat a védett erőforráshoz, például a webes API-t.                                                                                                                                                                                                                                                                                                                                     |
| token_type    | Typ tokenu értékét jelöli. Az egyetlen típus, amely támogatja az Azure ad-ben tulajdonosi                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                        |
| scope         | A hatókörök, amely a access_token érvényes.                                                                                                                                                                                                                                                                                                                                                                                                          |
| refresh_token | Egy új OAuth 2.0-s frissítési jogkivonatot. Ez újonnan megszerzett frissítési jogkivonat annak érdekében, hogy a frissítési biztonsági jogkivonat továbbra is érvényesek, amíg a régi frissítési jogkivonat kell cserélni. <br> **Megjegyzés:** csak a megadott if `offline_access` a kért hatókörhöz.                                                                                                                                                                                                |
| id_token      | Az előjel nélküli JSON webes jogkivonat (JWT). Az alkalmazás is base64Url dekódolni a szegmensek a token a bejelentkezett felhasználóval kapcsolatos információkat. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. Id_tokens kapcsolatos további információkért lásd: a [v2.0-végpont jogkivonat referenciái](v2-id-and-access-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` a kért hatókörhöz. |

#### <a name="error-response"></a>Hiba történt a válasz

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paraméter         | Leírás                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához.           |
| error_codes |A diagnosztikát segítő STS-specifikus hibakódok listáját. |
| időbélyeg |Az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztikai egyedi azonosítója. |
| correlation_id |A különböző összetevők a diagnosztikát segítő kérelem egyedi azonosítója. |

A hibakódok és a javasolt művelet leírását, [hibakódok a jogkivonat-végpont hibákat](#error-codes-for-token-endpoint-errors).
