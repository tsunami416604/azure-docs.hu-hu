---
title: OpenID Connect protokoll – Microsoft identitásplatform | Azure
description: Webalkalmazásokat hozhat létre az OpenID Connect hitelesítési protokoll Microsoft identitásplatform-implementációjával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: fa42bf65ea5f4469f714dda4331d3cb66156d187
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535798"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft identity platform és OpenID Connect protokoll

Az OpenID Connect egy OAuth 2.0-s rendszerre épülő hitelesítési protokoll, amelynek segítségével biztonságosan bejelentkezhet egy felhasználóba egy webalkalmazásba. Ha a Microsoft identity platform végpont openid connect-implementációját használja, bejelentkezési és API-hozzáférést adhat a webalapú alkalmazásokhoz. Ez a cikk bemutatja, hogyan kell ezt a nyelvtől függetlenül ellátni, és http-üzenetek küldését és fogadását ismerteti a Microsoft nyílt forráskódú kódtárai használata nélkül.

> [!NOTE]
> A Microsoft identity platform végpont jatt talanminden Azure Active Directory (Azure AD) forgatókönyvek és szolgáltatások. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

[Az OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kiterjeszti az OAuth 2.0 *engedélyezési* protokollt *hitelesítési* protokollként való használatra, így az OAuth használatával egyszeri bejelentkezést tehet le. Az OpenID Connect bevezeti az *azonosító jogkivonat*fogalmát, amely egy biztonsági jogkivonat, amely lehetővé teszi az ügyfél számára a felhasználó identitásának ellenőrzését. Az azonosító jogkivonat is lekéréseket kap a felhasználó alapvető profiladatait. Mivel az OpenID Connect kiterjeszti az OAuth 2.0-s rendszert, az alkalmazások biztonságosan szerezhetnek *hozzáférési jogkivonatokat,* amelyek az [engedélyezési kiszolgáló](active-directory-v2-protocols.md#the-basics)által védett erőforrások elérésére használhatók. A Microsoft identity platform végpont is lehetővé teszi, hogy az Azure AD regisztrált külső alkalmazások hozzáférési jogkivonatokat a biztonságos erőforrások, például a webes API-k. A hozzáférési jogkivonatok kiadására szolgáló alkalmazások beállításáról az [alkalmazás regisztrálása a Microsoft identity platform végpontjával](quickstart-register-app.md)című témakörben talál további információt. Javasoljuk, hogy használja az OpenID Connect et, ha olyan [webalkalmazást](v2-app-types.md#web-apps) hoz fel, amely et egy kiszolgálón üzemelteti, és amelyet böngészőn keresztül érhet el.

## <a name="protocol-diagram-sign-in"></a>Protokolldiagram: Bejelentkezés

A legalapvetőbb bejelentkezési folyamat a következő diagramban látható lépéseket. Minden lépést részletesen ismertetett ebben a cikkben.

![OpenID Connect protokoll: Bejelentkezés](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentum beolvasása

Az OpenID Connect olyan metaadat-dokumentumot ír le, amely az alkalmazás bejelentkezéséhez szükséges legtöbb információt tartalmazza. Ez olyan információkat is tartalmaz, mint például a használandó URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. A Microsoft identity platform végpontja esetében ez az OpenID Connect metaadat-dokumentum, amelyet a következőknek kell használnia:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Próbálja ki! Kattintson [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) ide `common` a bérlők konfigurációjának megtekintéséhez.

A `{tenant}` négy érték egyikét veheti fel:

| Érték | Leírás |
| --- | --- |
| `common` |A személyes Microsoft-fiókkal és az Azure AD-ből egy munkahelyi vagy iskolai fiókkal rendelkező felhasználók bejelentkezhetnek az alkalmazásba. |
| `organizations` |Csak az Azure AD-ből származó munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `consumers` |Csak a személyes Microsoft-fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak egy adott Azure AD-bérlő felhasználói (függetlenül attól, hogy munkahelyi vagy iskolai fiókkal rendelkező címtártagjai-e, vagy személyes Microsoft-fiókkal rendelkező vendégek) jelentkezhetnek be az alkalmazásba. Az Azure AD-bérlő rövid tartományneve vagy a bérlő GUID-azonosítója használható. Használhatja a fogyasztói bérlőt `9188040d-6c67-4c5b-b112-36a304b66dad`is, `consumers` a bérlő helyett.  |

A metaadatok egy egyszerű JavaScript objektumjelölés (JSON) dokumentum. Tekintse meg a következő kódrészletet egy példát. A kódrészlet tartalmát teljes körűen leírjuk az [OpenID Connect specifikációban.](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Ha az alkalmazás a [jogcím-leképezési](active-directory-claims-mapping.md) funkció használata miatt rendelkezik egyéni aláírási kulcsokkal, hozzá kell fűznie egy `appid` lekérdezési paramétert, amely tartalmazza az alkalmazásazonosítót, hogy `jwks_uri` az alkalmazás aláírási kulcsinformációira mutasson. Például: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` a `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`részét tartalmazza.

Ezt a metaadat-dokumentumot általában openId connect tárak vagy SDK konfigurálására kell használni; a könyvtár a metaadatokat használja a munka során. Ha azonban nem egy előre elkészített OpenID Connect-könyvtárat használ, a cikk további részében ismertetett lépéseket követve a Microsoft identity platform végpontjának használatával bejelentkezhet egy webalkalmazásba.

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, a végpontra irányíthatja a `/authorize` felhasználót. Ez a kérés hasonló az [OAuth 2.0 engedélyezési kód folyamatának első szakaszához,](v2-oauth2-auth-code-flow.md)az alábbi fontos különbségekkel:

* A kérelemnek `openid` tartalmaznia `scope` kell a hatókört a paraméterben.
* A `response_type` paraméternek `id_token`tartalmaznia kell a .
* A kérelemnek `nonce` tartalmaznia kell a paramétert.

> [!IMPORTANT]
> Annak érdekében, hogy az /authorization végpontból sikeresen kérjen azonosítótokent, a [regisztrációs portálon](https://portal.azure.com) lévő alkalmazásregisztrációnak `oauth2AllowIdTokenImplicitFlow` engedélyeznie kell a id_tokens implicit megadását a Hitelesítés lapon (amely az [alkalmazásjegyzék](reference-app-manifest.md) jelzőjét állítja `true`be). Ha nincs engedélyezve, `unsupported_response` a rendszer hibát ad vissza: "A megadott érték a bemeneti paraméter "response_type" nem engedélyezett az ügyfél számára. A várt érték a "kód""

Például:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> A kérelem végrehajtásához kattintson az alábbi hivatkozásra. Miután bejelentkezett, a böngésző átlesz `https://localhost/myapp/`irányítva a , azonosító tokent a címsorban. Vegye figyelembe, `response_mode=fragment` hogy ez a kérés csak demonstrációs célokra szolgál. Javasoljuk, hogy `response_mode=form_post`használja a használatát.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | A kérelem `{tenant}` elérési útjának használatával szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További információt a [protokollalapjai című témakörben talál.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | Kötelező | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `response_type` | Kötelező | `id_token` Tartalmaznia kell az OpenID Connect bejelentkezéshez. Más `response_type` értékeket is tartalmazhat, `code`például . |
| `redirect_uri` | Ajánlott | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás hitelesítési válaszokat küldhet és fogadhat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy URL-kódolva kell lennie. Ha nincs jelen, a végpont véletlenszerűen kiválaszt egy regisztrált redirect_uri, amelynek a felhasználót visszaküldi. |
| `scope` | Kötelező | A hatókörök térben elválasztott listája. Az OpenID Connect esetében tartalmaznia kell a hatókört, `openid`amely a hozzájárulási felhasználói felület "Bejelentkezés" engedélyére fordítja le. A hozzájárulás kérésében más hatóköröket is megadhat. |
| `nonce` | Kötelező | Az alkalmazás által létrehozott kérelemben szereplő érték, amely jogcímként szerepel az eredményül kapott id_token értékben. Az alkalmazás ellenőrizheti ezt az értéket a token replay támadások csökkentése érdekében. Az érték általában egy randomizált, egyedi karakterlánc, amely a kérelem eredetének azonosítására használható. |
| `response_mode` | Ajánlott | Megadja azt a módszert, amelyet az eredményül kapott engedélyezési kód nak az alkalmazásba való visszaküldéséhez kell használni. A következők egyike lehet: `form_post` vagy `fragment`. Webes alkalmazások esetén azt `response_mode=form_post`javasoljuk, hogy a használatát, a tokenek legbiztonságosabb átvitelének biztosítása érdekében az alkalmazásba. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszakerül. Ez lehet egy sor bármilyen tartalmat akarsz. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt vagy nézetet, amelyen a felhasználó volt. |
| `prompt` | Optional | A szükséges felhasználói beavatkozás típusát jelzi. Jelenleg csak a , `login` `none`és `consent`a érvényes értékek érvényesek. A `prompt=login` jogcím kényszeríti a felhasználót, hogy adja meg a hitelesítő adatait, hogy a kérelem, amely tagadja az egyszeri bejelentkezés. Az `prompt=none` állítás az ellenkezője. Ez a jogcím biztosítja, hogy a felhasználó nem jelenik meg semmilyen interaktív kérdés. Ha a kérés nem hajtható végre csendben egyszeri bejelentkezéssel, a Microsoft identity platform végpont hibát ad vissza. A `prompt=consent` jogcím elindítja az OAuth hozzájárulási párbeszédablakot, miután a felhasználó bejelentkezik. A párbeszédpanel arra kéri a felhasználót, hogy adjon engedélyeket az alkalmazásnak. |
| `login_hint` | Optional | Ezzel a paraméterrel előre kitöltheti a felhasználó bejelentkezési lapjának felhasználónevét és e-mail-cím mezőjét, ha előre ismeri a felhasználónevet. Gyakran előfordul, hogy az alkalmazások ezt a paramétert használják az újrahitelesítés során, `preferred_username` miután már kinyerték a felhasználónevet egy korábbi bejelentkezésből a jogcím használatával. |
| `domain_hint` | Optional | A felhasználó birodalma összevont könyvtárban.  Ez kihagyja az e-mail-alapú felderítési folyamat, amely a felhasználó megy keresztül a bejelentkezési oldalon, egy kicsit egyszerűbb felhasználói élményt. A bérlők, amelyek egy helyszíni címtárban, például az AD FS-en keresztül, ez gyakran eredményez zökkenőmentes bejelentkezést a meglévő bejelentkezési munkamenet miatt. |

Ezen a ponton a rendszer kéri a felhasználót, hogy adja meg hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft identity platform végpontja ellenőrzi, hogy a felhasználó `scope` hozzájárult-e a lekérdezési paraméterben megadott engedélyekhez. Ha a felhasználó nem járult hozzá ezekhez az engedélyekhez, a Microsoft identity platform végpontja felszólítja a felhasználót, hogy járuljon hozzá a szükséges engedélyekhez. Az [engedélyekről, a hozzájárulásról és a több-bérlős alkalmazásokról](v2-permissions-and-consent.md)olvashat bővebben.

Miután a felhasználó hitelesíti és megadja a jóváhagyást, a Microsoft identity platform végpont javunkra választ `response_mode` ad az alkalmazásnak a megadott átirányítási URI-n a paraméterben megadott módszerrel.

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz `response_mode=form_post` használata a következőképpen néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. A `id_token` paraméter segítségével ellenőrizheti a felhasználó identitását, és munkamenetet kezdhet a felhasználóval. Az azonosító tokenekről és azok tartalmáról további információt a [ `id_tokens` hivatkozásban talál.](id-tokens.md) |
| `state` | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető az átirányítási URI-ba, hogy az alkalmazás kezelni tudja őket. A hibaválasz így néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amelya felmerülő hibatípusok osztályozására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Hibakódok az engedélyezési végponthibáihoz

Az alábbi táblázat a `error` hibaválasz paraméterében visszaadható hibakódokat ismerteti:

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| `invalid_request` | Protokollhiba, például hiányzó, kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, amely általában a kezdeti tesztelés során észlelésre kerül. |
| `unauthorized_client` | Az ügyfélalkalmazás nem kérhet engedélyezési kódot. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználótól az alkalmazás telepítésére vonatkozó utasításokat, és hozzáadhatja az Azure AD-hez. |
| `access_denied` | Az erőforrás tulajdonosa megtagadta a hozzájárulást. |Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem folytatható, ha a felhasználó nem járul hozzá. |
| `unsupported_response_type` |Az engedélyezési kiszolgáló nem támogatja a kérelemben lévő választípust. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, amely általában a kezdeti tesztelés során észlelésre kerül. |
| `server_error` | A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezek a hibák ideiglenes körülményekből eredhetnek. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz átmeneti hiba miatt késik. |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |
| `invalid_resource` | A célerőforrás érvénytelen, mert vagy nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználótól az alkalmazás telepítésére és az Azure AD-hez való hozzáadására vonatkozó utasításokat. |

## <a name="validate-the-id-token"></a>Az azonosítótoken ellenőrzése

A id_token fogadása nem elegendő a felhasználó hitelesítéséhez; ellenőriznie kell a id_token aláírását, és ellenőriznie kell a jogkivonatban lévő jogcímeket az alkalmazás követelményeinek megfelelően. A Microsoft identity platform végpont [json webtokenek (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú kriptográfia a jogkivonatok aláírására és annak ellenőrzésére, hogy azok érvényesek.

Dönthet úgy, hogy `id_token` érvényesíti az ügyfélkódban, de `id_token` általános gyakorlat, hogy elküldi a háttérkiszolgálóra, és ott végzi el az érvényesítést. Miután ellenőrizte a id_token aláírását, néhány jogcím szükséges az ellenőrzéshez. További információt a [ `id_token` hivatkozásban](id-tokens.md) talál, beleértve a [jogkivonatok érvényesítését](id-tokens.md#validating-an-id_token) és [a kulcsváltás aláírásával kapcsolatos fontos információkat.](active-directory-signing-key-rollover.md) Javasoljuk, hogy a tokenek elemzéséhez és érvényesítéséhez használjon könyvtárat – a legtöbb nyelvhez és platformhoz legalább egy elérhető.

Előfordulhat, hogy a forgatókönyvtől függően további jogcímeket is érvényesíteni szeretne. Néhány gyakori érvényesítés:

* Annak biztosítása, hogy a felhasználó/szervezet feliratkozott az alkalmazásra.
* A felhasználó megfelelő jogosultságokkal/jogosultságokkal való biztosítása
* A hitelesítés bizonyos erősségének, például a többtényezős hitelesítésnek a biztosítása.

Miután érvényesítette a id_token, megkezdheti a munkamenetet a felhasználóval, és a id_token jogcímek használatával információkat szerezhet a felhasználóról az alkalmazásban. Ez az információ használható megjelenítésre, rekordokra, személyre szabásra stb.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha ki szeretné jelentkezni a felhasználót az alkalmazásból, az nem elegendő az alkalmazás cookie-jainak törléséhez vagy a felhasználó munkamenetének más módon történő befejezéséhez. A kijelentkezéshez át kell irányítania a felhasználót a Microsoft identity platform végpontjára is. Ha ezt nem teszi meg, a felhasználó újra hitelesíti magát az alkalmazásba anélkül, hogy újra megadnák a hitelesítő adataikat, mert érvényes egyszeri bejelentkezési munkamenettel rendelkeznek a Microsoft identity platform végpontjával.

A felhasználóát átirányíthatja `end_session_endpoint` az OpenID Connect metaadat-dokumentumban felsoroltakhoz:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Állapot | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Ajánlott | Az URL-cím, ahhoz az URL-cím, amelyre a felhasználó a sikeres kijelentkezés után átirányítja. Ha a paraméter nem szerepel, a felhasználó egy általános üzenetet jelenít meg, amelyet a Microsoft identity platform végpontja hoz létre. Ennek az URL-címnek meg kell egyeznie az alkalmazáshoz az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a `end_session_endpoint`felhasználót a, a Microsoft identity platform végpont törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is be van jelentkezve más alkalmazásokba, amelyek Microsoft-fiókokat használnak hitelesítésre. Annak érdekében, hogy ezek az alkalmazások egyszerre jelentkezhessenek ki a felhasználóból, `LogoutUrl` a Microsoft identity platform végpontja HTTP GET-kérelmet küld az összes olyan alkalmazás regisztrált számára, amelybe a felhasználó jelenleg be van jelentkezve. Az alkalmazásoknak válaszolniuk kell erre a kérésre a `200` felhasználót azonosító munkamenetek törlésével és a válasz visszaküldésével. Ha szeretné támogatni az egyszeri kijelentkezést az alkalmazásban, végre kell hajtania egy `LogoutUrl` ilyen az alkalmazás kódjában. Beállíthatja az `LogoutUrl` alkalmazás regisztrációs portálon.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokolldiagram: Hozzáférési jogkivonat-beszerzés

Számos webalkalmazásnak nem csak a felhasználót kell bejelentkeznie, hanem a felhasználó nevében az OAuth használatával is hozzá kell férnie egy webszolgáltatáshoz. Ez a forgatókönyv egyesíti az OpenID Connect felhasználói hitelesítést, miközben egy engedélyezési kódot kap, amely segítségével hozzáférési jogkivonatokat kaphat, ha az OAuth engedélyezési kód folyamatot használ.

A teljes OpenID Connect bejelentkezési és tokenbeszerzési folyamat a következő diagramhoz hasonlóan néz ki. Az egyes lépéseket részletesen ismertetjük a cikk következő szakaszaiban.

![OpenID Connect protokoll: Token-beszerzés](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beszereznie
Hozzáférési jogkivonatok beszerzéséhez módosítsa a bejelentkezési kérelmet:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs
offline_access%20
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> A kérelem végrehajtásához kattintson az alábbi hivatkozásra. Miután bejelentkezett, a böngésző átlesz irányítva a , `https://localhost/myapp/`egy azonosító tokent és egy kódot a címsorban. Vegye figyelembe, `response_mode=fragment` hogy ez a kérelem csak demonstrációs célokra szolgál. Javasoljuk, hogy `response_mode=form_post`használja a használatát.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Az engedélyható körök nek a `response_type=id_token code`kérelembe való beengedésével és a használatával a Microsoft identity `scope` platform végpontja biztosítja, hogy a felhasználó hozzájárult a lekérdezési paraméterben megjelölt engedélyekhez. Egy engedélyezési kódot ad vissza az alkalmazásnak egy hozzáférési jogkivonat cseréjéhez.

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz `response_mode=form_post` használata így néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. Az azonosító jogkivonat segítségével ellenőrizheti a felhasználó identitását, és munkamenetet kezdhet a felhasználóval. Az azonosító tokenekről és azok tartalmáról további [ `id_tokens` ](id-tokens.md)részleteket talál a hivatkozásban. |
| `code` | Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet a célerőforráshoz. Az engedélyezési kód rövid életű. Az engedélyezési kód általában körülbelül 10 perc múlva lejár. |
| `state` | Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető az átirányítási URI-ba, hogy az alkalmazás megfelelően tudja kezelni őket. A hibaválasz így néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amelya felmerülő hibatípusok osztályozására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

A lehetséges hibakódok és az ajánlott ügyfélválaszok leírását az [engedélyezési végponthibák hibakódok ban olvassa](#error-codes-for-authorization-endpoint-errors)el.

Ha rendelkezik egy engedélyezési kóddal és egy azonosító jogkivonatgal, bejelentkezhet a felhasználóba, és hozzáférési jogkivonatokat kaphat a nevükben. A felhasználó bejelentkezéséhez pontosan a [leírtaknak meghatározott módon](id-tokens.md#validating-an-id_token)kell érvényesítenie az azonosítójogkivonatot. A hozzáférési jogkivonatok beszerzéséhez kövesse az [OAuth kódfolyamat dokumentációjában](v2-oauth2-auth-code-flow.md#request-an-access-token)leírt lépéseket.
