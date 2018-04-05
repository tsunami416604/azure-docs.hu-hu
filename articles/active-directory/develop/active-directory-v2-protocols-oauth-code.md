---
title: Az Azure AD v2.0 OAuth-engedélyezési folyamata Code |} Microsoft Docs
description: Épület webes alkalmazásokat az Azure AD-megvalósítással az OAuth 2.0 hitelesítési protokoll.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6af6fb6ab957d79d71c816e6a23b0526f016a71
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v2.0 protokoll - OAuth 2.0 Hitelesítésikód-folyamata
Az OAuth 2.0 hitelesítésengedélyezési kód a védett erőforrások, például webes API-k eléréséhez az eszköz a telepített alkalmazások is használható.  Az app model v2.0 megvalósítással OAuth 2.0 hozzáadhat bejelentkezhet, és a mobil- és asztali alkalmazásokhoz való hozzáférés API.  Ez az Útmutató nyelvtől független, és ismerteti, hogyan lehet üzeneteket küldjön és fogadjon HTTP nélkül használja a [Azure nyílt forráskódú hitelesítési tárakat](active-directory-authentication-libraries.md).

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

Az OAuth 2.0 hitelesítésikód-folyamata ismertetett [az OAuth 2.0-s szabvány 4.1 szakasz](http://tools.ietf.org/html/rfc6749).  A legtöbb alkalmazástípusok, beleértve a hitelesítési és engedélyezési végrehajtásához használatos [webalkalmazások](active-directory-v2-flows.md#web-apps) és [natívan telepített alkalmazásokat](active-directory-v2-flows.md#mobile-and-native-apps).  A folyamat lehetővé teszi, hogy az alkalmazások biztonságos szerezni a v2.0-végpontra által védett erőforrások eléréséhez használható access_tokens.  

## <a name="protocol-diagram"></a>Protokoll diagramja
Magas szinten az egész hitelesítési folyamat natív/mobile alkalmazás néz ki egy kicsit:

![OAuth hitelesítési folyamata](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Az engedélyezési kód kérése
A hitelesítésikód-folyamata kezdődik irányítja a felhasználót, hogy az ügyfél a `/authorize` végpont.  A kérelem az ügyfél azt jelzi, kell szerzi be a felhasználói engedélyek:

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
> A kérelem végrehajtása az alábbi hivatkozásra kattintva! Történő bejelentkezés után a böngésző át kell irányítani `https://localhost/myapp/` rendelkező egy `code` a böngésző címsorába.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Paraméter             |             | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő                | Szükséges    | A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat.  További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                      |
| client_id             | Szükséges    | Az alkalmazás-D, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazáshoz hozzárendelt.                                                                                                                                                                                                                                                                                                                                                                                                |
| response_type         | Szükséges    | Tartalmaznia kell `code` a a hitelesítésikód-folyamata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| redirect_uri          | Ajánlott | Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszok redirect_uri.  Ez pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve az url-kódolású kell lennie.  Natív & mobileszköz-alkalmazások esetén az alapértelmezett értéket használjon `https://login.microsoftonline.com/common/oauth2/nativeclient`.                                                                                                                                                                                                                                                              |
| Hatókör                 | Szükséges    | Szóközökkel elválasztott listáját [hatókörök](active-directory-v2-scopes.md) , amelyet a felhasználó beleegyezését számára.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| response_mode         | Ajánlott | Megadja azt a módszert, amelynek használatával az eredményül kapott jogkivonat vissza küldése az alkalmazásnak.  A következők egyike lehet: `query` vagy `form_post`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| state                 | Ajánlott | A kérelemhez, amely a token válaszul is visszaadott szerepel érték.  Bármely, a kívánt tartalmat karakterlánc lehet.  Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások megelőzése](http://tools.ietf.org/html/rfc6749#section-10.12).  Az érték is az alkalmazásban a felhasználó állapotával kapcsolatos információk is kódolására, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a.                                                                                                                                          |
| parancssor                | választható    | Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges.  Jelenleg csak érvényes értékei a "bejelentkezés", "none", és "".  `prompt=login` arra kényszeríti a felhasználó megadja a hitelesítő adataikat, hogy kérésre nem lehet negálni egyszeri bejelentkezést.  `prompt=none` Ellenkező - biztosítja, hogy a felhasználó számára nem jelenik meg minden bármely interaktív kérdés.  Ha a kérelem nem hajtható végre csendes keresztül egyszeri bejelentkezést, akkor a v2.0-végpontra hibát adnak vissza.  `prompt=consent` az OAuth-hozzájárulás párbeszédpanel akkor indul el, miután a felhasználó jelentkezik be, amely kéri a felhasználót, hogy engedélyezze, hogy az alkalmazás. |
| login_hint            | választható    | Segítségével előre töltse ki a bejelentkezési oldal a felhasználó a felhasználónév vagy e-mail cím mező, ha tudja, hogy időben a felhasználónevét.  Gyakran alkalmazások ismételt hitelesítés, hogy már kivont a felhasználónév egy korábbi bejelentkezési használatával során fogja használni ezt a paramétert a `preferred_username` jogcímek.                                                                                                                                                                                                                                                                                                      |
| domain_hint           | választható    | Egyike lehet `consumers` vagy `organizations`.  Ha tartalmazza, azt az e-mail alapú felderítési folyamat kihagyja, hogy a felhasználó végighalad a bejelentkezési lapon v2.0 egy nagyobb jelentőséggel zökkenőmentes felhasználói élményt vezet.  Gyakran alkalmazások használja ezt a paramétert ismételt hitelesítés során kivonja a `tid` az előző bejelentkezés.  Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`.  Ellenkező esetben használja `domain_hint=organizations`.                                                                                                                |
| code_challenge_method | választható    | Kódolja használt módszer a `code_verifier` a a `code_challenge` paraméter. Egyike lehet `plain` vagy `S256`.  Ha ki van zárva, `code_challenge` adottnak egyszerű szöveges Ha `code_challenge` tartalmazza.  Az Azure AAD v2.0 egyaránt támogat `plain` és `S256`. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | választható    | Egy natív ügyfél a kód Exchange (PKCE) engedélyezési kód biztosít igazolása kulccsal védelméhez használt. Kötelező, ha `code_challenge_method` tartalmazza.  További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                      |

Ezen a ponton a kérni fogja a felhasználótól kell adnia a hitelesítő adatait, és a hitelesítés végrehajtásához.  A v2.0-végpontra is biztosítják, hogy a felhasználó hozzájárult szerepelnek az engedélyeket a `scope` lekérdezési paraméter.  Ha a felhasználó nem hozzájárult bármelyik ezeket az engedélyeket, azt kéri a felhasználó számára, hogy a szükséges engedélyekkel.  A részletek [engedélyek, beleegyezése és több-bérlős alkalmazásokhoz itt megadott](active-directory-v2-scopes.md).

Miután a felhasználó hitelesíti és engedélyezi a hozzájárulási, a v2.0-végpontra ad vissza az alkalmazást a jelzett választ `redirect_uri`, az ismertetett módon a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=query` láthatóhoz hasonló:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paraméter | Leírás                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kód      | A authorization_code, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot célerőforrás igényelhetnek.  Authorization_codes nagyon rövid élettartamú, általában azok körülbelül 10 perc múlva lejár. |
| state     | Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek.                                              |

#### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , az alkalmazás megfelelően tudja ezeket kezelni:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter         | Leírás                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához.            |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Engedélyezési végpont hibái hibakódok
A következő táblázat ismerteti a különböző hibakódok a visszaadható a `error` paramétere a hibaüzenetet.

| Hibakód                | Leírás                                                                                                           | Ügyfélművelet                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Protokollhiba történt, például a hiányzó kötelező paraméter.                                                                 | Javítsa ki, és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában észlelés fejlesztési hiba.                                                                                                                                       |
| unauthorized_client       | Az ügyfélalkalmazás az engedélyezési kód kérése nem engedélyezett.                                             | Ez a hiba általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| access_denied             | Erőforrás tulajdonosa hozzájárulási megtagadva                                                                                         | Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem folytatható, kivéve, ha a felhasználó.                                                                                                                                                 |
| unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja a kérelem a válasz típusa.                                           | Javítsa ki, és küldje el újra a kérelmet. Ez az fejlesztői hiba általában kezdeti tesztelés során lépett fel.                                                                                                                                       |
| server_error              | A kiszolgáló váratlan hibát észlelt.                                                                           | Próbálkozzon újra a kéréssel. Ezeket a hibákat okozhat az átmeneti állapotot. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy a válasz egy ideiglenes hiba késleltetett.                                                                  |
| temporarily_unavailable   | A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére.                                                             | Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában.                                                                                                                 |
| invalid_resource          | A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. | Ez a hiba azt jelzi, hogy az erőforrás, ha létezik, nem konfiguráltak a bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás.                                            |

## <a name="request-an-access-token"></a>A kérelem egy hozzáférési jogkivonatot:
Most, hogy egy authorization_code jut hozzá, és a felhasználó a engedélyt kaptak, beváltani a `code` a egy `access_token` a kívánt erőforráshoz. Ehhez úgy, hogy küld egy `POST` elküldeni a kérelmet a `/token` végpont:

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
> Próbálja meg a kérelem végrehajtása a Postman! (Ne felejtse el lecserélni a `code`) [ ![Postman futtatása](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paraméter     |                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő        | Szükséges              | A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat.  További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints).                                                                                                                                                     |
| client_id     | Szükséges              | Az alkalmazás azonosítója, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazáshoz hozzárendelt.                                                                                                                                                                                                                               |
| grant_type    | Szükséges              | Kell `authorization_code` a a hitelesítésikód-folyamata.                                                                                                                                                                                                                                                                                                                                                                              |
| Hatókör         | Szükséges              | Hatókörök szóközökkel elválasztott listája.  A kért Ez engedélyezi a hatókörök a hatókörök, az első engedélyezi a kért egy részhalmazát vagy egyenértékű kell lennie.  Ha a kérelemben megadott hatókörök span több erőforrás-kiszolgáló, akkor a v2.0-végpontra vissza az első hatókörében megadott erőforrás jogkivonat.  A hatókörök részletes ismertetése, tekintse meg [engedélyek, beleegyezése és hatókörök](active-directory-v2-scopes.md). |
| Kód          | Szükséges              | A folyamat első szakasza beszerzett authorization_code.                                                                                                                                                                                                                                                                                                                                                                     |
| redirect_uri  | Szükséges              | Az azonos redirect_uri érték szerezni a authorization_code használttal.                                                                                                                                                                                                                                                                                                                                                               |
| client_secret | a web Apps szükséges | Az alkalmazás az app-regisztrációs portálon létrehozott alkalmazáskulcsot.  Akkor kell nem használható natív alkalmazás, mert client_secrets megbízhatóan nem tárolható az eszközökön.  Akkor szükséges a webalkalmazások és webes API-k, amely képes a client_secret tárolja biztonságos helyen a kiszolgáló oldalán.                                                                                                                        |
| code_verifier | választható              | Az beszerzése a authorization_code használt azonos code_verifier.  Szükséges, ha az engedélyezési kód támogatási kérelmet PKCE használta.  További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>A sikeres válasz
A sikeres token válasz hasonlóan fog kinézni:

```
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
| access_token  | A kért hozzáférési jogkivonat. Az alkalmazás a token használatával hitelesítik magukat a védett erőforrások, például egy webes API.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | A jogkivonat típusa értékét jelöli. Csak az Azure AD támogató típus tulajdonosi                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                         |
| Hatókör         | A hatókörök, amely a access_token érvényes.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás képes használni a token szerezzen be további hozzáférési jogkivonatok az aktuális jogkivonat lejárata után is.  Refresh_tokens hosszú élettartamú, és erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használható.  További részletekért tekintse meg a [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md). <br> **Megjegyzés:** csak a megadott if `offline_access` hatókör kérték.                                                 |
| id_token      | Az aláírás nélküli JSON webes jogkivonat (JWT). Az alkalmazás is base64Url dekódolni a bejelentkezett felhasználóval kapcsolatos információkat a token szegmensek. Az alkalmazás gyorsítótárazása az értékeket, és a megjelenítésükhöz, de azt nem igazolható a azokat bármilyen engedélyezési vagy a biztonsági határokat.  Id_tokens kapcsolatos további információkért tekintse meg a [v2.0 jogkivonat végponthivatkozás](active-directory-v2-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` hatókör kérték. |
#### <a name="error-response"></a>Hibaválaszba
Hibaválaszok hasonlóan fog kinézni:

```
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
| error             | Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához.            |
| error_codes       | STS-specifikus hibakódok, amelyek segítik a diagnosztika listáját.                                                  |
| időbélyeg         | Az az idő, a hiba történt.                                                                             |
| trace_id          | A kérelem, amelyek segítik a diagnosztika egyedi azonosítója.                                                 |
| correlation_id    | A kérelem, amelyek segítik a diagnosztika összetevői között egyedi azonosítója.                               |

#### <a name="error-codes-for-token-endpoint-errors"></a>A token-végpont hibákat hibakódok
| Hibakód              | Leírás                                                                                                           | Ügyfélművelet                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Protokollhiba történt, például a hiányzó kötelező paraméter.                                                                 | Javítsa ki, és küldje el újra a kérelmet                                                                                                                                                                                                                |
| invalid_grant           | Az engedélyezési kód PKCE kód hitelesítő érvénytelen vagy lejárt.                                               | Próbálja meg egy új kérelmet a `/authorize` végpontot, és ellenőrizze, hogy megfelelő volt-e a code_verifier paraméter.                                                                                                                                     |
| unauthorized_client     | A hitelesített ügyfél nem jogosult a engedélyezési grant típus használatára.                                      | Ez általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| invalid_client          | Nem sikerült ügyfél-hitelesítéshez.                                                                                         | Az ügyfél hitelesítő adatok érvénytelenek. Javítsa ki, az alkalmazás-rendszergazda frissítések a hitelesítő adatokat.                                                                                                                                        |
| unsupported_grant_type  | A hitelesítési kiszolgáló nem támogatja az engedélyezési grant típusát.                                               | A kérelem grant típusának módosítása. Hiba az ilyen típusú csak a fejlesztés során megtörténik, és a kezdeti tesztelés során észlelt.                                                                                                       |
| invalid_resource        | A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. | Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás.                                            |
| interaction_required    | A kérelem felhasználói beavatkozást igényel. Például egy további hitelesítési lépésre szükség.                    | Próbálja megismételni a kérelmet, amelynek ugyanerre az erőforrásra.                                                                                                                                                                                                   |
| temporarily_unavailable | A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére.                                                             | Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában.                                                                                                                 |

## <a name="use-the-access-token"></a>A hozzáférési jogkivonat használata
Most, hogy sikeresen jut hozzá egy `access_token`, azzal, hogy belefoglalja azt a webes API-k intézett kérésekben a token is használhatja a `Authorization` fejléc:

> [!TIP]
> A kérelem végrehajtása a Postman! (Cserélje le a `Authorization` fejléc első) [ ![Postman futtatása](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>A hozzáférési jogkivonat frissítése
Access_tokens rövid élt, és frissítenie kell őket után folytatja az erőforrások eléréséhez.  Megteheti egy másik elküldése `POST` elküldeni a kérelmet a `/token` végpont, hogy ezúttal megadása a `refresh_token` ahelyett, hogy a `code`:

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
> Próbálja meg a kérelem végrehajtása a Postman! (Ne felejtse el lecserélni a `refresh_token`) [ ![Postman futtatása](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paraméter     |                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő        | Szükséges              | A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat.  További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                   |
| client_id     | Szükséges              | Az alkalmazás azonosítója, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazáshoz hozzárendelt.                                                                                                                                                                                                                                                             |
| grant_type    | Szükséges              | Kell `refresh_token` az ezen szakasza a hitelesítésikód-folyamata.                                                                                                                                                                                                                                                                                                                                                                                                     |
| Hatókör         | Szükséges              | Hatókörök szóközökkel elválasztott listája.  A hatókörök, ez a szakasz a kért a hatókörök az eredeti authorization_code kérést engedélyezi a kért egy részhalmazát vagy egyenértékű kell lennie.  Ha a kérelemben megadott hatókörök span több erőforrás-kiszolgáló, akkor a v2.0-végpontra vissza az első hatókörében megadott erőforrás jogkivonat.  A hatókörök részletes ismertetése, tekintse meg [engedélyek, beleegyezése és hatókörök](active-directory-v2-scopes.md). |
| refresh_token | Szükséges              | A folyamat második szakasza beszerzett refresh_token.                                                                                                                                                                                                                                                                                                                                                                                                       |
| redirect_uri  | Szükséges              | Az azonos redirect_uri érték szerezni a authorization_code használttal.                                                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | a web Apps szükséges | Az alkalmazás az app-regisztrációs portálon létrehozott alkalmazáskulcsot.  Akkor kell nem használható natív alkalmazás, mert client_secrets megbízhatóan nem tárolható az eszközökön.  Akkor szükséges a webalkalmazások és webes API-k, amely képes a client_secret tárolja biztonságos helyen a kiszolgáló oldalán.                                                                                                                                                     |

#### <a name="successful-response"></a>A sikeres válasz
A sikeres token válasz hasonlóan fog kinézni:

```
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
| access_token  | A kért hozzáférési jogkivonat. Az alkalmazás a token használatával hitelesítik magukat a védett erőforrások, például egy webes API.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | A jogkivonat típusa értékét jelöli. Csak az Azure AD támogató típus tulajdonosi                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                         |
| Hatókör         | A hatókörök, amely a access_token érvényes.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Egy új OAuth 2.0-s frissítési jogkivonat. Az újonnan megvásárolt frissítési jogkivonat annak érdekében, hogy a frissítési jogkivonatokat továbbra is érvényesek, amíg a régi frissítési jogkivonat kell cserélni. <br> **Megjegyzés:** csak a megadott if `offline_access` hatókör kérték.                                                                                                                                                                                                 |
| id_token      | Az aláírás nélküli JSON webes jogkivonat (JWT). Az alkalmazás is base64Url dekódolni a bejelentkezett felhasználóval kapcsolatos információkat a token szegmensek. Az alkalmazás gyorsítótárazása az értékeket, és a megjelenítésükhöz, de azt nem igazolható a azokat bármilyen engedélyezési vagy a biztonsági határokat.  Id_tokens kapcsolatos további információkért tekintse meg a [v2.0 jogkivonat végponthivatkozás](active-directory-v2-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` hatókör kérték. |

#### <a name="error-response"></a>Hibaválaszba
```
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
| error             | Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description | Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához.            |
| error_codes |STS-specifikus hibakódok, amelyek segítik a diagnosztika listáját. |
| időbélyeg |Az az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztika egyedi azonosítója. |
| correlation_id |A kérelem, amelyek segítik a diagnosztika összetevői között egyedi azonosítója. |

A hibakódok és a javasolt művelet leírását, [token-végpont hibák hibakódok](#error-codes-for-token-endpoint-errors).

