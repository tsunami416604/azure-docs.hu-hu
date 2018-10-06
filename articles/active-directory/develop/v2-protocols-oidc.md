---
title: Az Azure Active Directory 2.0-s verzió és az OpenID Connect protokoll |} A Microsoft Docs
description: Webes alkalmazásokat hozhat létre az Azure AD v2.0 végrehajtása az OpenID Connect hitelesítési protokoll használatával.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 91979d46a341f0892d4e5774246bac5a7897f698
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815613"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Az Azure Active Directory 2.0-s verzió és az OpenID Connect protokoll

OpenID Connect nem épül, amely segítségével biztonságosan jelentkezzen be a felhasználót, hogy egy webes alkalmazás OAuth 2.0 hitelesítési protokoll. OpenID Connect megvalósítását a v2.0-végpont használata esetén is hozzáadhat be- és API-hozzáférés a webalapú alkalmazások. Ez a cikk bemutatja, hogyan hajtsa végre a nyelvi független, és ismerteti, hogyan lehet az üzenetek küldése és fogadása HTTP-könyvtárra sem a Microsoft nyílt forráskódú használata nélkül.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory (Azure AD-) forgatókönyveket és funkciókat. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) kibővíti az OAuth 2.0 *engedélyezési* protokollt használja, mint egy *hitelesítési* protokoll, megteheti, hogy egyszeri bejelentkezés OAuth használatával. OpenID Connect bemutatja a egy *azonosító jogkivonat*, amelynek egy biztonsági jogkivonatot, amely lehetővé teszi, hogy az ügyfél számára, hogy a felhasználó identitásának ellenőrzéséhez. Az azonosító jogkivonat is lekéri a felhasználóval kapcsolatos főbb profiladatait. OpenID Connect kibővíti az OAuth 2.0, mert az alkalmazások biztonságos beszerezhető *hozzáférési jogkivonatokat*, által védett erőforrások eléréséhez használható egy [az engedélyezési kiszolgáló](active-directory-v2-protocols.md#the-basics). A v2.0-végpontra is lehetővé teszi, hogy a külső alkalmazások, amelyek az Azure ad-vel védett erőforrások, például webes API-k számára a hozzáférési jogkivonatok kiállításához. Hogyan állítható be egy alkalmazás hozzáférési jogkivonatok kiállításához kapcsolatos további információkért lásd: [alkalmazás regisztrálása a v2.0-végponttal](quickstart-v2-register-an-app.md). Javasoljuk, hogy használjon OpenID Connect készítésekor egy [webes alkalmazás](v2-app-types.md#web-apps) , amelyet a kiszolgáló által üzemeltetett és böngészőalapú hozzáférést biztosít.

## <a name="protocol-diagram-sign-in"></a>Diagram protokoll: bejelentkezés

A legalapvetőbb bejelentkezési folyamat rendelkezik a lépéseket, a következő ábrán látható. Az egyes lépések Ez a cikk részletesen ismerteti.

![OpenID Connect protokoll: bejelentkezés](./media/v2-protocols-oidc/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentum beolvasása

A metaadat-dokumentum, amely tartalmazza az alkalmazás bejelentkezési végrehajtásához szükséges adatokat a legtöbb OpenID Connect ismerteti. Ez magában foglalja az adatokat, például a használandó URL-címek és a szolgáltatás nyilvános aláírási kulcsok helyét. A v2.0-végpont Ez az az OpenID Connect metaadat-dokumentumokról kell használnia:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Próbálja ki! Kattintson a [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) megtekintéséhez a `common` bérlők konfiguráció.

A `{tenant}` négy értékek valamelyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` |Személyes Microsoft-fiókkal és a egy munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) rendelkező felhasználók jelentkezhetnek be az alkalmazáshoz. |
| `organizations` |Csak a felhasználók munkahelyi vagy iskolai fiókok Azure AD-ből az alkalmazás jelentkezhetnek be. |
| `consumers` |Csak a személyes Microsoft-fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazáshoz. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` |Csak a felhasználók egy munkahelyi vagy iskolai fiókot egy adott Azure AD-ből a bérlői jelentkezhetnek be az alkalmazást. Az Azure AD-bérlő rövid tartományneve, vagy a bérlő GUID-azonosító is használható. |

Egy egyszerű JavaScript Object Notation (JSON) a dokumentum metaadatai. Tekintse meg példaként az alábbi kódrészletet. A kódtöredék tartalma részletesen a [OpenID Connect specifikáció](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Általában használna a metaadat-dokumentum-könyvtár OpenID Connect vagy az SDK; konfigurálása a könyvtár a metaadatokat használja ezt a munkát. Ha a létrehozás előtti OpenID Connect tár nem használ, követheti a lépések végrehajtásához jelentkezzen be egy webes alkalmazásban a v2.0-végpont használatával Ez a cikk további részében.

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem küldése

A webalkalmazás kell hitelesíteni a felhasználót, amikor azt a felhasználót, hogy irányíthatók a `/authorize` végpont. Ezt a kérelmet az első alapját képező hasonlít a [OAuth 2.0 hitelesítési kódfolyamat](v2-oauth2-auth-code-flow.md), az alábbi fontos különbség:

* A kérésnek tartalmaznia kell a `openid` hatókörét a a `scope` paraméter.
* A `response_type` paraméternek tartalmaznia kell `id_token`.
* A kérésnek tartalmaznia kell a `nonce` paraméter.

> [!IMPORTANT]
> Ahhoz, hogy sikerült-azonosító jogkivonat, az alkalmazás regisztrációját a kérelem a [regisztrációs portál](https://apps.dev.microsoft.com) kell rendelkeznie a **[típusú Implicit engedélyezés](v2-oauth2-implicit-grant-flow.md)** a webes ügyfél engedélyezve. Ha nincs engedélyezve, egy `unsupported_response` visszaadott hiba: "a"response_type"bemeneti paraméter megadott értéke nem engedélyezett ennél az ügyfélnél. Várt érték "code" "

Példa:

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
> Kattintson az alábbi hivatkozásra a kérelem végrehajtásához. Miután bejelentkezett, a böngésző átirányítja https://localhost/myapp/, egy azonosító jogkivonat címet a címsorba. Vegye figyelembe, hogy a kérelem használ `response_mode=fragment` (bemutatási célokra csak). Javasoljuk, hogy használjon `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |Használhatja a `{tenant}` szabályozza, ki is jelentkezzen be az alkalmazásba irányuló kérelem elérési útja értéket. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További információkért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints). |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| response_type |Szükséges |Tartalmaznia kell `id_token` OpenID Connect bejelentkezhet. Emellett tartalmazhat más `response_type` értékek, mint például `code`. |
| redirect_uri |Ajánlott |Az átirányítási URI-ját az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell az átirányítási URI-k a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| scope |Szükséges |Hatókörök szóközzel elválasztott listáját. Az OpenID Connect, tartalmaznia kell a hatókör `openid`, amelyet a rendszer lefordítja arra a jóváhagyási felhasználói felület a "Bejelentkezés" engedélyt. Más hatókörök tartalmazhatják a hozzájárulás kérése a kéréshez. |
| egyszeri |Szükséges |A kérésben, az eredményül kapott id_token érték jogcímként szerepeljenek, az alkalmazás által generált érték. Az alkalmazás ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Az érték általában véletlenszerű, egyedi karakterlánc, amely a kérés eredetének azonosítására használhatók. |
| response_mode |Ajánlott |Meghatározza az eredményül kapott engedélyezési kód küldi vissza az alkalmazáshoz használandó módszert. A következők egyike lehet: `form_post` vagy `fragment`. A webes alkalmazásokhoz, javasoljuk `response_mode=form_post`, annak érdekében, a jogkivonatok legbiztonságosabb átvitelét az alkalmazáshoz. |
| state |Ajánlott |A kérelem is visszaadott a jogkivonat-válaszban szereplő érték. Bármilyen tartalmat karakterlánc lehet. Egy véletlenszerűen generált egyedi érték általában szolgál [webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében](http://tools.ietf.org/html/rfc6749#section-10.12). Az állapot is szolgál a felhasználói állapot az alkalmazás információ kódolása előtt a hitelesítési kérelmet, például a lapot vagy a nézet a felhasználó nem található. |
| parancssor |Optional |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg az egyetlen érvényes értékek a következők `login`, `none`, és `consent`. A `prompt=login` jogcím kényszeríti a felhasználónak meg kell adnia a hitelesítő adataikat a kérelmet, amely egyszeri bejelentkezés ellentettjét adja. A `prompt=none` jogcím ennek az ellenkezője. Ez a jogcím biztosítja, hogy a felhasználó el minden olyan interaktív kérdés nem egyike. Ha a kérés nem lehet végrehajtani csendes keresztül egyszeri bejelentkezést, a v2.0-végpont hibát ad vissza. A `prompt=consent` jogcím elindítja az OAuth-hozzájárulási párbeszédpanel a felhasználó bejelentkezése után az. A párbeszédpanel megkérdezi a felhasználót, engedélyeket az alkalmazáshoz. |
| login_hint |Optional |Ez a paraméter használatával előre töltse ki a felhasználónevét és e-mail cím mező, a bejelentkezési oldal a felhasználó számára, ha a felhasználónév előre tudja. Gyakran alkalmazások használata során újbóli hitelesítés után már kinyerését a felhasználónevet egy korábbi bejelentkezési használatával ezt a paramétert a `preferred_username` jogcím. |
| domain_hint |Optional |Ez az érték lehet `consumers` vagy `organizations`. Ha a csomagban foglalt, kihagyja az az e-mail-alapú felderítési folyamat, amely a felhasználó végighalad a bejelentkezési lapon v2.0 némileg több zökkenőmentes felhasználói élményt. Gyakran alkalmazások használja ezt a paramétert az újrahitelesítés során oly módon, a `tid` jogcím a azonosító jogkivonat. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad` (a Microsoft Account fogyasztói bérlő), használjon `domain_hint=consumers`. Ellenkező esetben használjon `domain_hint=organizations`. |

Ezen a ponton a felhasználótól megadják hitelesítő adataikat, és a hitelesítés végrehajtásához. A v2.0-végpont ellenőrzi, hogy a felhasználó hozzájárult a megadott engedélyeket a `scope` lekérdezési paraméter. Ha a felhasználó nem egyezett bele bármelyik ezeket az engedélyeket, a v2.0-végpont kéri a felhasználó beleegyezését, a szükséges engedélyekkel. További információ [engedélyek, beleegyezése és több-bérlős alkalmazások](v2-permissions-and-consent.md).

Miután a felhasználó hitelesíti és engedélyezi a jóváhagyás, a v2.0-végpont választ küld az alkalmazást a jelzett átirányítási URI-t a megadott módszer használatával a `response_mode` paraméter.

### <a name="successful-response"></a>A sikeres válasz

A sikeres válasz használatakor `response_mode=form_post` kell kinéznie:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az azonosító jogkivonat, amely az alkalmazás kéri. Használhatja a `id_token` paraméter segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. Azonosító-jogkivonatokat, és azok tartalmát kapcsolatos további információkért lásd: a [ `id_tokens` referencia](id-tokens.md). |
| state |Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak. |

### <a name="error-response"></a>Hiba történt a válasz

Hibaválaszok is előfordulhat, hogy az átirányítási URI-JÁNAK küldött, úgy, hogy az alkalmazás képes kezelni őket. Egy hibaválasz a következőhöz hasonló:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát besorolása a fellépő hibákat, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Hitelesítési végpont hibák hibakódok

A következő táblázat ismerteti azokat a hibakódokat, a visszaadható a `error` válaszként küldött hibaüzenetben paraméteréhez:

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például egy hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. Ez az egy fejlesztési hiba, amely a kezdeti tesztelés során általában történt. |
| unauthorized_client |Az ügyfélalkalmazás egy hozzáférési kód nem lehet kérni. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás is kéri a felhasználót az utasításokat az alkalmazás telepítéséhez, és adja hozzá az Azure AD. |
| ACCESS_DENIED |Az erőforrás tulajdonosa jóváhagyás elutasítva. |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem lehet folytatni, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kód választípusához a kérésben. |Javítsa ki, és küldje el újra a kérelmet. Ez az egy fejlesztési hiba, amely a kezdeti tesztelés során általában történt. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Ismételje meg a kérelmet. Ezeket a hibákat okozhat az ideiglenes feltételek. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy átmeneti hiba miatt késik. |
| temporarily_unavailable |A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése. |Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy azt nem megfelelően van konfigurálva. |Ez azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználói utasításokat az alkalmazás telepítése és az Azure ad-hez adásával. |

## <a name="validate-the-id-token"></a>Az azonosító jogkivonat érvényesítése

Id_token érkező azonban nem hitelesíti a felhasználót; kell érvényesíteni az aláírást a id_token, és ellenőrizze a jogcímeket az alkalmazáskövetelmények szerint. Használja a v2.0-végpont [JSON webes jogkivonatainak (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú hitelesítésen jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek.

Ha szeretné ellenőrizni a `id_token` ügyfél kódot, de általános gyakorlat az, hogy küldjön a `id_token` háttérkiszolgálóhoz, és végezze el az érvényesítési hiba. A id_token aláírásának ellenőrzése után, ha nincsenek néhány jogcímek ellenőrzéséhez meg kell adni. Tekintse meg a [ `id_token` referencia](id-tokens.md) további információért többek között [jogkivonatok érvényesítése](id-tokens.md#validating-an-idtoken) és [fontos információkat kapcsolatos aláíró kulcs váltása](active-directory-signing-key-rollover.md). Javasoljuk, hogy a jogkivonatok használata a tár elemzés és ellenőrzése – nincs legalább egy elérhető a legtöbb nyelvekhez és platformokhoz.
<!--TODO: Improve the information on this-->

Érdemes ellenőrizni a forgatókönyvtől függően további jogcímek is. Néhány gyakori ellenőrzések a következők:

* Annak biztosítása, a felhasználó és szervezet regisztrált az alkalmazáshoz.
* Biztosítása a felhasználó rendelkezik a megfelelő engedélyezési vagy jogosultságokkal
* Egy bizonyos hitelesítés erőssége biztosító történt, például többtényezős hitelesítést.

Teljesen ellenőrzése a id_token, után megkezdheti a felhasználói munkamenetet, és a jogcímek használata a id_token az információkat kaphat az alkalmazás a felhasználó. Ez az információ használható megjelenített, a rekordokat, a személyre szabása, stb.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése

Ha meg szeretné ki a felhasználót az alkalmazásból, nem elegendő az alkalmazás cookie-k törléséhez, vagy ellenkező esetben az a felhasználói munkamenet befejezése. Meg kell is átirányítja a felhasználót a v2.0-végpont, jelentkezzen ki. Ha nem ezt teszi, a felhasználó újrahitelesít az alkalmazásba a hitelesítő adatok újbóli megadása nélkül, mert egy érvényes egyszeri bejelentkezési munkamenet a v2.0-végponttal rendelkeznek.

Irányíthatja át a felhasználót, hogy a `end_session_endpoint` az OpenID Connect metaadat-dokumentumban felsorolt:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Állapot | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Ajánlott | Az URL-cím, amely a felhasználó sikeres bejelentkezés után van átirányítva. Ha a paraméter nem tartalmaz, a felhasználó a v2.0-végpont által létrehozott általános üzenet jelenik meg. Az URL-címet meg kell egyeznie az átirányítási URI-k az alkalmazás az app-regisztrációs portál regisztrált egyikét. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a felhasználót, hogy a `end_session_endpoint`, a v2.0-végpont törlése a felhasználó munkamenetének a böngészőből. Azonban a előfordulhat, hogy továbbra is lehet bejelentkezett felhasználó egyéb alkalmazásokhoz, amelyek a Microsoft-fiókokat használnak a hitelesítéshez. Ahhoz, hogy ezek az alkalmazások aláírására a felhasználó ki egyszerre, a v2.0 végpont egy HTTP GET kérést küld a regisztrált `LogoutUrl` , a felhasználó jelenleg be van jelentkezve az alkalmazásokat. Alkalmazások válaszolnia kell ehhez a kérelemhez, amely azonosítja a felhasználói munkamenettől és visszaadó egy `200` választ. Ha szeretne támogatja az egyszeri kijelentkezéshez az alkalmazásban, meg kell valósítani például egy `LogoutUrl` az alkalmazás kódjában. Beállíthatja a `LogoutUrl` az alkalmazás regisztrációs portálon.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokoll ábra: a hozzáférési token beszerzése

Számos webes alkalmazás kell nem csak a felhasználó, de még a felhasználó nevében egy webszolgáltatás eléréséhez az OAuth használatával. Ebben a forgatókönyvben egyesíti a OpenID Connect-felhasználói hitelesítést az engedélyezési kódot, amellyel hozzáférési tokenekhez, az OAuth hitelesítési kódfolyamat használatakor egyidejűleg beolvasása közben.

A következő diagram a teljes OpenID Connect bejelentkezést és a jogkivonat beszerzéséhez folyamat hasonlít. A cikk a következő szakaszokban részletesen lépéseinek ismertetünk.

![OpenID Connect protokoll: Token beszerzése](./media/v2-protocols-oidc/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Hozzáférési jogkivonat beszerzése
Hozzáférési jogkivonatok beszerzésére, módosítsa a bejelentkezési kérelem:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kattintson az alábbi hivatkozásra a kérelem végrehajtásához. Miután bejelentkezett, a rendszer átirányítja a böngésző https://localhost/myapp/, egy azonosító jogkivonat és a egy kód a címsorba. Vegye figyelembe, hogy a kérelem használ `response_mode=fragment` (bemutatási célokra csak). Javasoljuk, hogy használjon `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

-Engedélyhatókörök, beleértve a kérésben és segítségével `response_type=id_token code`, a v2.0-végpont biztosítja, hogy a felhasználó hozzájárult a megadott engedélyeket a `scope` lekérdezési paraméter. Az engedélyezési kódot az alkalmazásba és az exchange-hozzáférési jogkivonatot ad vissza.

### <a name="successful-response"></a>A sikeres válasz

Használatával a sikeres válasz `response_mode=form_post` kell kinéznie:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az azonosító jogkivonat, amely az alkalmazás kéri. Az azonosító jogkivonat segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. Azonosító-jogkivonatokat és azok tartalmát, a további információt találhat a [ `id_tokens` referencia](id-tokens.md). |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód használatával a célként megadott erőforrás-hozzáférési jogkivonat kérése. Az engedélyezési kódot nagyon rövid ideig tartó. Általában egy hozzáférési kód körülbelül 10 perc múlva lejár. |
| state |Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak. |

### <a name="error-response"></a>Hiba történt a válasz

Hibaválaszok is előfordulhat, hogy az átirányítási URI-JÁNAK küldött, úgy, hogy az alkalmazás képes kezelni őket megfelelően. Egy hibaválasz a következőhöz hasonló:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát besorolása a fellépő hibákat, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |

Egy lehetséges hibakódok és ajánlott ügyfél válaszok ismertetését lásd: [hibakódok hitelesítési végpont hibák](#error-codes-for-authorization-endpoint-errors).

Ha az engedélyezési kódot, és a egy azonosító jogkivonat, jelentkezzen be a felhasználó, és hozzáférési tokenek beszerzése a felhasználók nevében. A felhasználó a aláírásához, ellenőrizni kell az azonosító jogkivonat [leírtak szerint pontosan](id-tokens.md#validating-an-idtoken). Hozzáférési jogkivonatok beszerzéséhez kövesse a leírt lépéseket követve [OAuth kód flow dokumentáció](v2-oauth2-auth-code-flow.md#request-an-access-token).
