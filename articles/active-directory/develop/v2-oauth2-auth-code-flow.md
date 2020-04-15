---
title: OAuth engedélyezési kód folyamata - Microsoft identity platform | Azure
description: Webalkalmazásokat hozhat létre az OAuth 2.0 hitelesítési protokoll Microsoft identitásplatform-implementációjával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5241089ff3cc7826216fcadd6fd94116ee4a2c89
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309440"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft identity platform és OAuth 2.0 engedélyezési kód folyamat

Az OAuth 2.0 engedélyezési kód támogatás használható alkalmazások, amelyek egy eszközre telepített, hogy hozzáférjenek a védett erőforrások, például a webes API-k. Az OAuth 2.0 Microsoft identity platformimplementációjával bejelentkezési és API-hozzáférést adhat a mobil- és asztali alkalmazásokhoz. Ez az útmutató nyelvfüggetlen, és leírja, hogyan küldhet és fogadhat HTTP-üzeneteket az [Azure nyílt forráskódú hitelesítési kódtárai](reference-v2-libraries.md)használata nélkül.

Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen.  Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

> [!NOTE]
> Nem minden Azure Active Directory-forgatókönyvet, & a microsoftidentity platform végpontja támogatja. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

Az OAuth 2.0 engedélyezési kódfolyamatát [az OAuth 2.0 specifikáció 4.1 szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. A hitelesítés és az engedélyezés végrehajtására szolgál a legtöbb alkalmazástípusban, beleértve a [webalkalmazásokat](v2-app-types.md#web-apps) és [a natívan telepített alkalmazásokat.](v2-app-types.md#mobile-and-native-apps) A folyamat lehetővé teszi, hogy az alkalmazások biztonságosan beszerezzék a access_tokens, amelyek a Microsoft identity platform végpontja által védett erőforrások elérésére használhatók.

## <a name="protocol-diagram"></a>Protokolldiagram

Magas szinten a natív/mobilalkalmazások teljes hitelesítési folyamata egy kicsit így néz ki:

![OAuth hitelesítési kódfolyamat](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Engedélyezési kód kérése

Az engedélyezési kód folyamata azzal kezdődik, `/authorize` hogy az ügyfél a végpontra irányítja a felhasználót. Ebben a kérésben az `openid` `offline_access`ügyfél `https://graph.microsoft.com/mail.read `a , és engedélyeket kér a felhasználótól.  Egyes engedélyek rendszergazdai korlátozások, például adatok írása a `Directory.ReadWrite.All`szervezet címtárába a használatával. Ha az alkalmazás hozzáférést kér egy szervezeti felhasználótól az engedélyek egyikéhez, a felhasználó hibaüzenetet kap, amely szerint nincs engedélye az alkalmazás engedélyeinek hozzájárulására. A korlátozott rendszergazdai hatókörökhöz való hozzáférés kéréséhez közvetlenül a vállalati rendszergazdától kell kérnie őket.  További információért olvassa el [a Rendszergazdai engedélyek című.](v2-permissions-and-consent.md#admin-restricted-permissions)

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
> Kattintson az alábbi linkre, hogy végre ezt a kérést! A bejelentkezés után a böngészőt át `https://localhost/myapp/` kell `code` irányítani egy címsorba.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter    | Kötelező/nem kötelező | Leírás |
|--------------|-------------|--------------|
| `tenant`    | kötelező    | A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További részletekért olvassa el [a protokoll alapjai című témakört.](active-directory-v2-protocols.md#endpoints)  |
| `client_id`   | kötelező    | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény.  |
| `response_type` | kötelező    | `code` Tartalmaznia kell az engedélyezési kód folyamat.       |
| `redirect_uri`  | kötelező | Az alkalmazás redirect_uri, ahol az alkalmazás elküldheti és fogadhatja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_uris egyikével, kivéve, hogy url-kódolni kell. Natív & mobilalkalmazások esetén a alapértelmezett `https://login.microsoftonline.com/common/oauth2/nativeclient`értéket kell használnia.   |
| `scope`  | kötelező    | A [hatókörek](v2-permissions-and-consent.md) területválasztó listája, amelyhez a felhasználónak hozzá kell járulnia.  A `/authorize` kérelem szakasza, ez több erőforrást is lefedhet, így az alkalmazás beleegyezését kapja a hívni kívánt több webes API-hoz. |
| `response_mode`   | Ajánlott | Megadja azt a módszert, amelyet az eredményül kapott jogkivonat nak az alkalmazásnak való visszaküldéséhez kell használni. A következők egyike lehet:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`a kódot lekérdezési karakterlánc-paraméterként adja meg az átirányítási URI-n. Ha azonosító tokent kér az implicit folyamat használatával, akkor `query` nem használhatja az [OpenID specifikációban megadottak szerint.](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations) Ha csak a kódot kéri, használhatja `query` `fragment`a `form_post`, vagy a . `form_post`végrehajtegy POST-et, amely tartalmazza a kódot az átirányítási URI-ba. További információ: [OpenID Connect protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is vissza kerül. Ez lehet egy sor bármilyen tartalmat, amit szeretne. A véletlenszerűen generált egyedi értéket általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)használják. Az érték a hitelesítési kérelem bekövetkezése előtt is kódolhatja a felhasználó állapotára vonatkozó információkat az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak. |
| `prompt`  | választható    | A szükséges felhasználói beavatkozás típusát jelzi. Jelenleg csak a , `login` `none`és `consent`a érvényes értékek érvényesek.<br/><br/>- `prompt=login`arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kéréshez, és negating egyszeri bejelentkezés.<br/>- `prompt=none`az ellenkezője - ez biztosítja, hogy a felhasználó nem jelenik meg semmilyen interaktív prompt egyáltalán. Ha a kérés nem hajtható végre csendben egyszeri bejelentkezéssel, a Microsoft `interaction_required` identity platform végpont jain egy hiba.<br/>- `prompt=consent`a felhasználó bejelentkezése után elindítja az OAuth hozzájárulási párbeszédpanelt, és arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak.<br/>- `prompt=select_account`megszakítja az egyszeri bejelentkezést, és az összes fiókot felsorolva felsorolhatja az összes fiókot a munkamenetben vagy bármely megjegyzett fiókban, vagy egy lehetőséget, hogy teljesen más fiókot használjon.<br/> |
| `login_hint`  | választható    | A felhasználó bejelentkezési lapjának felhasználónév/e-mail cím mezőjének előzetes kitöltésére használható, ha előre ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután `preferred_username` már kinyerték a felhasználónevet egy korábbi bejelentkezési jogcím használatával.   |
| `domain_hint`  | választható    | Lehet az `consumers` egyik `organizations`vagy .<br/><br/>Ha tartalmazza, akkor kihagyja az e-mail-alapú felderítési folyamat, amely a felhasználó megy keresztül a bejelentkezési oldalon, ami egy kicsit egyszerűbb felhasználói élményt. Az alkalmazások gyakran használják ezt a paramétert `tid` az újrahitelesítés során, egy korábbi bejelentkezésből való kibontással. Ha `tid` a jogcím `9188040d-6c67-4c5b-b112-36a304b66dad`értéke, `domain_hint=consumers`akkor a használatát kell használnia. Ellenkező esetben `domain_hint=organizations`használja a használatát.  |
| `code_challenge_method` | választható    | A `code_verifier` `code_challenge` paraméter kódolására használt módszer. Az alábbi értékek egyike lehet:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Ha kizárt, `code_challenge` akkor a program `code_challenge` egyszerű szövegnek tekinti, ha szerepel. A Microsoft identity `plain` platform `S256`támogatja mind a és a. További információt a [PKCE RFC című](https://tools.ietf.org/html/rfc7636)témakörben talál. |
| `code_challenge`  | választható | Az engedélyezési kódtámogatások biztonságossá tétele a Kódcsere próbakulcsán (PKCE) keresztül egy natív ügyféltől. Kötelező, `code_challenge_method` ha benne van. További információt a [PKCE RFC című](https://tools.ietf.org/html/rfc7636)témakörben talál. |

Ezen a ponton a rendszer felkéri a felhasználót, hogy adja meg hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft identity platform végpontja azt is biztosítja, hogy a `scope` felhasználó hozzájárult a lekérdezési paraméterben megjelölt engedélyekhez. Ha a felhasználó nem járult hozzá ezekhez az engedélyekhez, meg fogja kérni a felhasználót, hogy járuljon hozzá a szükséges engedélyekhez. Az [engedélyek, a jóváhagyás és a több-bérlős alkalmazások](v2-permissions-and-consent.md)részletei itt találhatók.

Miután a felhasználó hitelesíti magát, és megadja a jóváhagyást, a Microsoft `redirect_uri`identity platform végpont javallott, válasz az alkalmazás a megadott , a `response_mode` paraméterben megadott módszerrel.

#### <a name="successful-response"></a>Sikeres válasz

A sikeres `response_mode=query` válasz a következőképpen néz ki:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paraméter | Leírás  |
|-----------|--------------|
| `code` | Az alkalmazás által kért authorization_code. Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet a célerőforráshoz. Authorization_codes rövid életűek, általában körülbelül 10 perc után járnak le. |
| `state` | Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

#### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető a, `redirect_uri` így az alkalmazás tudja kezelni őket megfelelően:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás  |
|----------|------------------|
| `error`  | Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hibakódok az engedélyezési végponthibáihoz

Az alábbi táblázat a hibaválasz `error` paraméterében visszaadható különböző hibakódokat ismerteti.

| Hibakód  | Leírás    | Ügyfélművelet   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokollhiba, például hiányzó szükséges paraméter. | Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, amely et általában a kezdeti tesztelés során észlelték. |
| `unauthorized_client` | Az ügyfélalkalmazás nem kérheti az engedélyezési kódot. | Ez a hiba általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| `access_denied`  | Az erőforrás tulajdonosa megtagadta a hozzájárulást  | Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem folytatható, ha a felhasználó nem járul hozzá. |
| `unsupported_response_type` | Az engedélyezési kiszolgáló nem támogatja a kérelemben lévő választípust. | Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, amely et általában a kezdeti tesztelés során észlelték.  |
| `server_error`  | A kiszolgáló váratlan hibát észlelt.| Próbálkozzon újra a kéréssel. Ezek a hibák ideiglenes körülményekből eredhetnek. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz a késésben van egy ideiglenes hibával. |
| `temporarily_unavailable`   | A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. | Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |
| `invalid_resource`  | A célerőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. | Ez a hiba azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| `login_required` | Túl sok vagy egyáltalán nem található felhasználó | Az ügyfél néma`prompt=none`hitelesítést ( ) kért, de egyetlen felhasználó nem talált. Ez azt jelentheti, hogy több felhasználó aktív a munkamenetben, vagy nincs enek. Ez figyelembe veszi a kiválasztott bérlőt (például ha két aktív Azure AD-fiók és egy Microsoft-fiók `consumers` van kiválasztva, a csendes hitelesítés működni fog). |
| `interaction_required` | A kérelem felhasználói beavatkozást igényel. | További hitelesítési lépésre vagy hozzájárulásra van szükség. Próbálkozzon újra `prompt=none`a kéréssel a nélkül. |

## <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése

Most, hogy beszerzett egy authorization_code, és a felhasználó engedélyt kapott, beválthatja a `code` kívánt `access_token` erőforrást. Ehhez küldjön `POST` egy kérést a `/token` végpontnak:

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
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman! (Ne felejtsd el `code`kicserélni a) [Próbálja meg futtatni ezt a kérést a Postman ben ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Paraméter  | Kötelező/nem kötelező | Leírás     |
|------------|-------------------|----------------|
| `tenant`   | kötelező   | A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További részletekért olvassa el [a protokoll alapjai című témakört.](active-directory-v2-protocols.md#endpoints)  |
| `client_id` | kötelező  | Az Alkalmazás (ügyfél) azonosító, amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap az alkalmazáshoz rendelt. |
| `grant_type` | kötelező   | Az `authorization_code` engedélyezési kód folyamatához kell lenni.   |
| `scope`      | kötelező   | A hatókörök térben elválasztott listája. Az ebben a lábban kért hatóköröknek meg kell egyeznek az első szakaszban kért hatóköröknek vagy azok egy részhalmazának. A hatóköröknek egyetlen erőforrásból kell származniuk, az`profile` `openid`OIDC-hatókörökkel ( , , `email`. ) együtt. A hatókörök részletesebb magyarázatát az [engedélyek, a hozzájárulás és a hatókörök című dokumentumban tájékot kell részletezni.](v2-permissions-and-consent.md) |
| `code`          | kötelező  | A authorization_code, amit az áramlás első szakaszában szereztél. |
| `redirect_uri`  | kötelező  | Ugyanaz a redirect_uri érték, mint amelyet a authorization_code megszerzéséhez használtak. |
| `client_secret` | szükséges a webalkalmazásokhoz | Az alkalmazás regisztrációs portálján létrehozott alkalmazástitkos kulcsot. Ne használja az alkalmazás titkos kulcsot egy natív alkalmazásban, mert client_secrets nem lehet megbízhatóan tárolni az eszközökön. Ez szükséges a webes alkalmazások és webes API-k, amelyek képesek a client_secret biztonságosan a kiszolgálóoldalon.  Az ügyféltitok nak URL-kódolásúnak kell lennie az elküldés előtt. További információkért kattintson [ide](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | választható  | Ugyanaz a code_verifier, mint a authorization_code megszerzéséhez. Kötelező, ha pkce-t használtak az engedélyezési kód engedélyezési kérelmében. További információt a [PKCE RFC című](https://tools.ietf.org/html/rfc7636)témakörben talál. |

### <a name="successful-response"></a>Sikeres válasz

A sikeres jogkivonat-válasz így fog kinézni:

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

| Paraméter     | Leírás   |
|---------------|------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot a biztonságos erőforrás, például egy webes API hitelesítéséhez.  |
| `token_type`    | A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a bemutatóra |
| `expires_in`    | Mennyi ideig érvényes a hozzáférési jogkivonat (másodpercben). |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes. |
| `refresh_token` | Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további hozzáférési jogkivonatokat az aktuális hozzáférési jogkivonat lejárta után. Refresh_tokens hosszú élettartamúak, és hosszabb ideig használhatók az erőforrásokhoz való hozzáférés megőrzésére. A hozzáférési jogkivonat frissítésével kapcsolatos további részletekért tekintse meg az [alábbi szakaszt.](#refresh-the-access-token) <br> **Megjegyzés:** Csak akkor `offline_access` adják meg, ha a hatókört kérték. |
| `id_token`      | Egy JSON webes token (JWT). Az alkalmazás dekódolhatja a token szegmenseit, hogy információt kérjen a bejelentkezett felhasználóról. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti azokat, de nem támaszkodhat rájuk semmilyen engedélyezési vagy biztonsági határesetén. A id_tokens kapcsolatos további [`id_token reference`](id-tokens.md)tudnivalókat a . <br> **Megjegyzés:** Csak akkor `openid` adják meg, ha a hatókört kérték. |

### <a name="error-response"></a>Hibaválasz

A hibaválaszok így fognak kinézni:

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

| Paraméter         | Leírás    |
|-------------------|----------------|
| `error`       | Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikában.  |
| `timestamp`   | A hiba bekövetkezésének időpontja. |
| `trace_id`    | A kérelem egyedi azonosítója, amely segíthet a diagnosztikában. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők közötti diagnosztikában. |

### <a name="error-codes-for-token-endpoint-errors"></a>Hibakódok a tokenvégpont-hibákhoz

| Hibakód         | Leírás        | Ügyfélművelet    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollhiba, például hiányzó szükséges paraméter. | A kérelem javítása és újraküldése   |
| `invalid_grant`    | Az engedélyezési kód vagy a PKCE-kód ellenőrzője érvénytelen vagy lejárt. | Próbáljon ki egy `/authorize` új kérést a végpontra, és ellenőrizze, hogy a code_verifier paraméter helyes-e.  |
| `unauthorized_client` | A hitelesített ügyfél nem jogosult az engedélyezési engedély típusának használatára. | Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| `invalid_client` | Az ügyfélhitelesítés nem sikerült.  | Az ügyfél hitelesítő adatai érvénytelenek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat.   |
| `unsupported_grant_type` | Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. | Módosítsa a támogatás típusát a kérelemben. Ez a hibatípus csak a fejlesztés során fordulhat elő, és a kezdeti tesztelés során észlelhető. |
| `invalid_resource` | A célerőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. | Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához.  |
| `interaction_required` | A kérelem felhasználói beavatkozást igényel. Például egy további hitelesítési lépés szükséges. | Próbálkozzon újra a kérelemmel ugyanazzal az erőforrással.  |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. | Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |

## <a name="use-the-access-token"></a>A hozzáférési jogkivonat használata

Most, hogy sikeresen beszerzett egy, `access_token`használhatja a jogkivonatot a webes `Authorization` API-kra irányuló kérelmekben, ha a fejlécbe is bekerül:

> [!TIP]
> Hajtsa végre ezt a kérést postás! (Először `Authorization` cserélje le a fejlécet) [Próbálja meg futtatni ezt a kérést a Postman ben ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>A hozzáférési jogkivonat frissítése

Access_tokens rövid életűek, és frissítenie kell őket a lejáratuk után, hogy továbbra is hozzáférhessen az erőforrásokhoz. Ezt úgy teheti meg, `POST` hogy egy `/token` másik kérést küld `refresh_token` a végpontnak, ezúttal a helyett a. `code`  A frissítési jogkivonatok minden olyan engedélyre érvényesek, amelyhez az ügyfél már `scope=mail.read` megkapta a beleegyezését – `scope=api://contoso.com/api/UseResource`így a kérelemre kiadott frissítési jogkivonat új hozzáférési jogkivonat kérésére használható.  

A frissítési jogkivonatok nem rendelkeznek megadott élettartammal. A frissítési jogkivonatok élettartama általában viszonylag hosszú. Bizonyos esetekben azonban a frissítési jogkivonatok lejárnak, visszavonásra kerülnek, vagy nem rendelkeznek megfelelő jogosultságokkal a kívánt művelethez. Az alkalmazásnak megfelelően kell várnia és kezelnie [a tokenkiállítási végpont által visszaadott hibákat.](#error-codes-for-token-endpoint-errors) 

Bár a frissítési jogkivonatok nem vonják vissza, ha új hozzáférési jogkivonatok beszerzésére használják, a régi frissítési jogkivonat ot kell elvetni. Az [OAuth 2.0 specifikáció](https://tools.ietf.org/html/rfc6749#section-6) ja: "Az engedélyezési kiszolgáló új frissítési jogkivonatot adhat ki, amely esetben az ügyfélnek el kell vetnie a régi frissítési jogkivonatot, és le kell cserélnie az új frissítési jogkivonatra. Az engedélyezési kiszolgáló visszavonhatja a régi frissítési jogkivonatot, miután új frissítési jogkivonatot adott ki az ügyfélnek."  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman! (Ne felejtsd el `refresh_token`kicserélni a) [Próbálja meg futtatni ezt a kérést a Postman ben ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Paraméter     |                | Leírás        |
|---------------|----------------|--------------------|
| `tenant`        | kötelező     | A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További részletekért olvassa el [a protokoll alapjai című témakört.](active-directory-v2-protocols.md#endpoints)   |
| `client_id`     | kötelező    | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `grant_type`    | kötelező    | Az `refresh_token` engedélyezési kód folyamatának ezen szakaszához kell tartanom. |
| `scope`         | kötelező    | A hatókörök térben elválasztott listája. Az ebben a lábban kért hatóköröknek egyenértékűnek kell lenniük az eredeti authorization_code kérelemszakaszban kért hatókörök egy részével vagy részhalmazával. Ha a kérelemben megadott hatókörök több erőforrás-kiszolgálóra is kiterjednek, akkor a Microsoft identity platform végpontja visszaad egy jogkivonatot az első hatókörben megadott erőforráshoz. A hatókörök részletesebb magyarázatát az [engedélyek, a hozzájárulás és a hatókörök című dokumentumban tájékot kell részletezni.](v2-permissions-and-consent.md) |
| `refresh_token` | kötelező    | A refresh_token, amit az áramlás második szakaszában szereztél. |
| `client_secret` | szükséges a webalkalmazásokhoz | Az alkalmazás regisztrációs portálján létrehozott alkalmazástitkos kulcsot. Nem használható natív alkalmazásban, mert client_secrets nem lehet megbízhatóan tárolni az eszközökön. Ez szükséges a webes alkalmazások és webes API-k, amelyek képesek a client_secret biztonságosan a kiszolgálóoldalon. Ez a titok kell URL-kódolt, további információkért kattintson [ide](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Sikeres válasz

A sikeres jogkivonat-válasz így fog kinézni:

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
| Paraméter     | Leírás         |
|---------------|-------------------------------------------------------------|
| `access_token`  | A kért hozzáférési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot a biztonságos erőforrás, például egy webes API hitelesítéséhez. |
| `token_type`    | A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a bemutatóra |
| `expires_in`    | Mennyi ideig érvényes a hozzáférési jogkivonat (másodpercben).   |
| `scope`         | Azok a hatókörök, amelyekre a access_token érvényes.    |
| `refresh_token` | Új OAuth 2.0 frissítési jogkivonat. A régi frissítési jogkivonatot le kell cserélnie ezzel az újonnan beszerzett frissítési jogkivonattal, hogy a frissítési jogkivonatok a lehető legtovább érvényesek maradjanak. <br> **Megjegyzés:** Csak akkor `offline_access` adják meg, ha a hatókört kérték.|
| `id_token`      | Aláíratlan JSON webtoken (JWT). Az alkalmazás dekódolhatja a token szegmenseit, hogy információt kérjen a bejelentkezett felhasználóról. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti azokat, de nem támaszkodhat rájuk semmilyen engedélyezési vagy biztonsági határesetén. A id_tokens kapcsolatos további [`id_token reference`](id-tokens.md)tudnivalókat a . <br> **Megjegyzés:** Csak akkor `openid` adják meg, ha a hatókört kérték. |

#### <a name="error-response"></a>Hibaválasz

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

| Paraméter         | Leírás                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában.           |
| `error_codes` |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikában. |
| `timestamp` | A hiba bekövetkezésének időpontja. |
| `trace_id` | A kérelem egyedi azonosítója, amely segíthet a diagnosztikában. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők közötti diagnosztikában. |

A hibakódok és az ajánlott ügyfélművelet leírását a [tokenvégpont-hibák hibakódjai című témakörben tartalmazza.](#error-codes-for-token-endpoint-errors)
