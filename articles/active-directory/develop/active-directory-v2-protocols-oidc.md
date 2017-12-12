---
title: "Az Azure Active Directory v2.0 és az OpenID Connect protokoll |} Microsoft Docs"
description: "A webalkalmazások Azure AD v2.0 végrehajtása az OpenID Connect hitelesítési protokoll használatával."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 568c2128a12abd4f3c366eae943e3ea8c1af2532
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Az Azure Active Directory v2.0 és az OpenID Connect protokoll
OpenID Connect hitelesítési protokoll segítségével biztonságosan jelentkezzen be a felhasználót, hogy egy webes alkalmazás OAuth 2.0-s épülő. A v2.0-végpontra végrehajtása az OpenID Connect használatakor adhat hozzá bejelentkezési és API-hozzáférés a webalapú alkalmazások. Ebben a cikkben azt mutatja be a független nyelv tegye. Azt ismerteti, hogyan küldésére és fogadására a HTTP-üzenetek a Microsoft nyílt forráskódú kódtárai használata nélkül.

> [!NOTE]
> A v2.0-végpontra nem támogatja az összes Azure Active Directory forgatókönyvek és funkciók. Annak megállapításához, hogy a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) kibővíti az OAuth 2.0 *engedélyezési* használandó protokoll egy *hitelesítési* protokollt, így egyetlen hajthat végre bejelentkezés OAuth protokollt használó. OpenID Connect jelenik meg az egy *azonosító token*, amelyen egy biztonsági jogkivonatot, amely lehetővé teszi, hogy az ügyfél számára, hogy a felhasználó identitásának ellenőrzéséhez. Az azonosító token is lekérdezi a felhasználó alapvető profiladataihoz adatait. OAuth 2.0 OpenID Connect terjeszti ki, mert az alkalmazások biztonságos lekérheti *hozzáférési jogkivonatok*, által védett erőforrások eléréséhez használható egy [engedélyezési server](active-directory-v2-protocols.md#the-basics). Javasoljuk, hogy használjon OpenID Connect készítésekor egy [webes alkalmazás](active-directory-v2-flows.md#web-apps) , a kiszolgáló által üzemeltetett és böngészőalapú hozzáférést biztosít.

## <a name="protocol-diagram-sign-in"></a>Protokoll diagramja: bejelentkezés
A legalapvetőbb bejelentkezési folyamata rendelkezik a lépéseket, a következő ábrán is látható. Azt írja le a részletes információkat ebben a cikkben minden lépést.

![OpenID Connect protokoll: bejelentkezés](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentum beolvasása
A legtöbb alkalmazás bejelentkezési végrehajtásához szükséges adatokat tartalmazó metaadat-dokumentum OpenID Connect ismerteti. Ide tartoznak például az URL-címek használatára és a szolgáltatás nyilvános aláírási kulcsok helyét. A v2.0-végpontra ez pedig az OpenID Connect metaadat-dokumentum kell használnia:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

A `{tenant}` négy értékek valamelyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` |A felhasználók a személyes Microsoft-fiókkal, és a munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) is jelentkezzen be az alkalmazást. |
| `organizations` |Csak a felhasználók munkahelyi vagy iskolai fiókok Azure ad-jelentkezhetnek be az alkalmazást. |
| `consumers` |Csak a személyes Microsoft-fiókkal rendelkező felhasználók is jelentkezzen be az alkalmazást. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` |Csak a felhasználók a munkahelyi vagy iskolai fiókkal, egy adott Azure ad-bérlő jelentkezhetnek be az alkalmazást. Az Azure AD-bérlő rövid tartomány nevét vagy GUID-azonosító a bérlő is használható. |

Egy egyszerű JavaScript Object Notation (JSON) dokumentum metaadatai. Tekintse meg az alábbi részlet egy példát. A kódrészletet tartalma részletesen a [OpenID Connect specification](https://openid.net).

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

Általában szeretné használni a metaadat-dokumentum konfigurálása az OpenID Connect könyvtárban vagy SDK; a könyvtár a munkájuk elvégzéséhez használja a metaadatok. Ha az OpenID Connect könyvtár előtti nem használja, követheti használatával történő végrehajtásához bejelentkezési használnak egy webalkalmazásban a v2.0-végpontra a cikk fennmaradó lépéseit.

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése
Ha a webalkalmazás kell hitelesíteni a felhasználót, utasíthatja-e a felhasználót, hogy a `/authorize` végpont. A kérelem hasonlít az első szakasza a [OAuth 2.0 hitelesítésikód-folyamata](active-directory-v2-protocols-oauth-code.md), az alábbi fontos különbség:

* A kérelem tartalmaznia kell a `openid` a hatókör a `scope` paraméter.
* A `response_type` paraméternek tartalmaznia kell `id_token`.
* A kérelem tartalmaznia kell a `nonce` paraméter.

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
> A kérelem végrehajtása a következő hivatkozásra kattintva. Miután bejelentkezik, a böngésző https://localhost/myapp/ címsor Azonosítóját tokenhez irányítja. Vegye figyelembe, hogy ehhez a kérelemhez használt `response_mode=query` (bemutatási célokra csak). Azt javasoljuk, hogy használjon `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://Login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>
> 
> 

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| Bérlői |Szükséges |Használhatja a `{tenant}` személyek jelentkezhetnek be az alkalmazás a kérelem elérési útjában szereplő értéket. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat. További információkért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints). |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz hozzárendelni. |
| response_type |Szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet. Is tartalmazhat más `response_types` értékek, például a `code`. |
| redirect_uri |Ajánlott |Az átirányítási URI-t, az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Az pontosan egyeznie kell az átirányítási URI-azonosítók regisztrálta a portálon, azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája. Az OpenID Connect, magában kell foglalnia a hatókör `openid`, amely az eszköz a hozzájárulási felhasználói felületén a "Bejelentkezés" engedélyt. A kérésben a hozzájárulás kérése kiterjedhetnek más hatókörök. |
| Nonce |Szükséges |A kérelem, az alkalmazás, az eredményül kapott id_token érték jogcímként szerepeltetni által generált szerepel érték. Az alkalmazás ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Az érték általában a véletlenszerű, egyedi karakterlánc, amely segítségével azonosíthatja a kérelem forrása. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott engedélyezési kód küldi vissza az alkalmazás. Egyike lehet `query`, `form_post`, vagy `fragment`. Webes alkalmazásokhoz, javasoljuk `response_mode=form_post`, annak érdekében, az alkalmazás a jogkivonatok legbiztonságosabb átvitelét. |
| state |Ajánlott |A kérelem is visszaad a biztonságijogkivonat-válaszban szereplő érték. Bármilyen tartalmat karakterlánc lehet. Egy véletlenszerűen generált egyedi érték általában szolgál [webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében](http://tools.ietf.org/html/rfc6749#section-10.12). Az állapot is kódolásához használatos a felhasználói állapot az alkalmazás információkat előtt a hitelesítési kérést, például az oldal vagy a felhasználó nem a nézet. |
| parancssor |Optional |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg csak az érvényes értékek a következők `login`, `none`, és `consent`. A `prompt=login` jogcím kényszeríti a felhasználó megadja a hitelesítő adataik adott kérelem, amely az egyszeri bejelentkezés ellentettjét adja. A `prompt=none` jogcím fordítottja. A jogcím biztosítja, hogy a felhasználó számára nem jelenik meg minden bármely interaktív kérdés. Ha a kérelem nem hajtható végre csendes keresztül egyszeri bejelentkezést, a v2.0-végpontra hibát ad vissza. A `prompt=consent` jogcímet a felhasználó bejelentkezése után az váltja ki az OAuth-hozzájárulás párbeszédpanel. A párbeszédpanelen kéri a felhasználót, hogy engedélyezze, hogy az alkalmazás. |
| login_hint |Optional |Ez a paraméter segítségével előre töltse ki a felhasználónevet és e-mail cím mező a bejelentkezési oldal a felhasználó számára, ha a felhasználónév időben tudja. Gyakran, alkalmazások használata során ismételt hitelesítés után már kibontása a felhasználónév egy korábbi bejelentkezés használatával ezt a paramétert a `preferred_username` jogcímek. |
| domain_hint |Optional |Az érték lehet `consumers` vagy `organizations`. Ha tartalmazza, azt az e-mail alapú felderítési folyamat, amely a felhasználó végighalad a bejelentkezési lapon v2.0 nagyobb jelentőséggel zökkenőmentes felhasználói élmény kihagyja. Gyakran, alkalmazások használja ezt a paramétert ismételt hitelesítés során kivonja a `tid` a Azonosítót jogkivonatban a jogcím. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`. Ellenkező esetben használja `domain_hint=organizations`. |

Ezen a ponton a felhasználótól a hitelesítő adataik megadására, és a hitelesítés végrehajtásához. A v2.0-végpontra ellenőrzi, hogy a felhasználó hozzájárult szerepelnek az engedélyeket a `scope` lekérdezési paraméter. Ha a felhasználó nem hozzájárult bármelyik ezeket az engedélyeket, a v2.0-végpontra kéri a felhasználó számára, hogy a szükséges engedélyekkel. További tudnivalók [engedélyek, beleegyezése és több-bérlős alkalmazásokhoz](active-directory-v2-scopes.md).

Miután a felhasználó hitelesíti és engedélyezi a hozzájárulási, a v2.0-végpontra adja vissza az alkalmazást a jelzett választ átirányítási URI-címe részben ismertetett módon használatával a `response_mode` paraméter.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatakor `response_mode=form_post` dolgozunk:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az alkalmazás által kért azonosítója jogkivonat. Használhatja a `id_token` paraméter, a felhasználó személyazonosságát, és a felhasználói munkamenet elindításához. Azonosító-jogkivonatokat és azok tartalmát kapcsolatos további tudnivalókért tekintse meg a [v2.0-végponttól jogkivonatok hivatkozás](active-directory-v2-tokens.md). |
| state |Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is lehet küldeni az átirányítási URI-t, hogy az alkalmazás képes kezelni azokat. Egy hiba történt egy válasz így néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc, amely a besorolására a felmerülő hibákat, és reagálni hibákat is használhatja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Engedélyezési végpont hibái hibakódok
A következő táblázat ismerteti a visszaadható hibakódok a `error` paramétere a hibaválaszba:

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például egy hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában kiszűri a fejlesztési hiba. |
| unauthorized_client |Az ügyfélalkalmazás az engedélyezési kód nem lehet kérni. |Ez általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználó az alkalmazás telepítéséhez, adja hozzá az Azure AD útmutatást. |
| ACCESS_DENIED |Az erőforrás tulajdonosa hozzájárulási megtagadva. |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem folytatható, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kérelem a válasz típusa. |Javítsa ki, és küldje el újra a kérelmet. Ez a kezdeti tesztelés során általában kiszűri a fejlesztési hiba. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezeket a hibákat okozhat az átmeneti állapotot. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes hiba miatt késik a válaszában. |
| temporarily_unavailable |A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában. |
| invalid_resource |A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználói utasításokat tartalmaz az alkalmazás telepítése és az Azure AD hozzáadni. |

## <a name="validate-the-id-token"></a>Az azonosító token ellenőrzése
Egy azonosító tokent fogadása túl kicsi a felhasználó hitelesítésére. Az azonosító jogkivonat-aláírás ellenőrzése és a jogcímek, az alkalmazás követelményeinek / token ellenőrzése is. Használja a v2.0-végpontra [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényes nyilvános kulccsal történő titkosítás.

Választhat, hogy az azonosító lexikális elem szerepel az Ügyfélkód érvényesítéséhez, de a gyakori eljárás a Azonosítót jogkivonatban küldeni egy háttér-kiszolgálónak, és hajtsa végre az érvényesítési hiba. Az azonosító jogkivonat aláírása ellenőrzését követően lesz szüksége lehet néhány jogcímek ellenőrzése. További információt, beleértve a több kapcsolatos [jogkivonatok ellenőrzése](active-directory-v2-tokens.md#validating-tokens) és [fontos információkat a kulcsváltás](active-directory-v2-tokens.md#validating-tokens), tekintse meg a [v2.0 jogkivonatok hivatkozás](active-directory-v2-tokens.md). Azt javasoljuk, hogy a szalagtárat használó elemezni és érvényesíthet jogkivonatokat. Van legalább egy ezeket a könyvtárakat legtöbb nyelvekhez és platformokhoz érhető el.
<!--TODO: Improve the information on this-->

Emellett előfordulhat, hogy szeretné érvényesíteni további jogcímeket, a forgatókönyvtől függően. Néhány gyakori érvényesítést a következők:

* Győződjön meg arról, hogy a felhasználó vagy a szervezet rendelkezik regisztrálta az alkalmazást.
* Győződjön meg arról, hogy a felhasználó szükséges engedélyezési vagy jogosultságokra.
* Győződjön meg arról, hogy bizonyos erősségével hitelesítési történt, például a többtényezős hitelesítést.

A jogcímek egy Azonosítót jogkivonatban kapcsolatos további információkért tekintse meg a [v2.0-végponttól jogkivonatok hivatkozás](active-directory-v2-tokens.md).

Teljesen érvényesítése a Azonosítót jogkivonatban után megkezdheti a munkamenet a felhasználó. Az Azonosítót jogkivonatban a jogcím segítségével az alkalmazás a felhasználó adatainak beolvasása. A kijelző, rekordok, engedélyek és így tovább használhatja ezeket az információkat.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha azt szeretné, az alkalmazás a felhasználó kijelentkezik, nem elegendő az alkalmazás cookie-k törléséhez, vagy ellenkező esetben az a felhasználói munkamenet befejezése. Meg kell is átirányítja a felhasználót a v2.0-végpontra jelentkezzen ki. Ha nem így tesz, a felhasználó újra hitelesíti magát az alkalmazás a hitelesítő adatok újbóli megadása nélkül, mert egy érvényes egyszeri bejelentkezési munkamenet a v2.0-végponttal rendelkeznek.

Irányíthatja át a felhasználót, hogy a `end_session_endpoint` az OpenID Connect metaadat-dokumentum szerepel:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Feltétel | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Ajánlott | Az URL-címet, amely a felhasználó sikeresen kijelentkezést követően van átirányítva. Ha a paraméter nincs megadva, a felhasználó a v2.0-végpontra által létrehozott általános üzenet jelenik meg. URL-címet meg kell egyeznie az átirányítási URI-azonosítók regisztrálva az alkalmazás az app-regisztrálási portál egyikét.  |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés
Ha átirányítja a felhasználót, hogy a `end_session_endpoint`, a v2.0-végpontra törli a felhasználói munkamenetet a böngészőből. Azonban a felhasználó esetleg még be van jelentkezve más alkalmazásokhoz, amelyek a hitelesítéshez használandó Microsoft-fiókok. Ahhoz, hogy ezek az alkalmazások beléptetni a felhasználót egy időben, a v2.0 kimenő végpont egy HTTP GET kérést küld a regisztrált `LogoutUrl` , a felhasználó van éppen bejelentkezve az alkalmazásokat. Alkalmazások bármely, amely azonosítja a felhasználói munkamenet és vissza ehhez a kérelemhez kell válaszolnia a `200` válasz.  Ha támogatja az egyszeri bejelentkezési ki az alkalmazásban, meg kell-e valósítania például egy `LogoutUrl` az alkalmazás kódjában.  Beállíthatja a `LogoutUrl` az alkalmazás regisztrációs portálon.

## <a name="protocol-diagram-token-acquisition"></a>Protokoll diagramja: lexikális elem az beszerzése
Sok webalkalmazások kell jelentkeznie nem csak a felhasználó, de is OAuth elérni egy webszolgáltatás-bővítmény, a felhasználó nevében. Ebben a forgatókönyvben egyesíti az OpenID Connect felhasználói hitelesítés egy engedélyezési kódot, amely segítségével a hozzáférési jogkivonatok lekérésére, az OAuth hitelesítésikód-folyamata használatakor egyidejűleg lekérése közben.

A teljes OpenID Connect bejelentkezési és jogkivonat-beszerzési folyamatot a következő ábra hasonlít. Azt írja le a cikk a következő szakaszokban részletesen lépésre.

![OpenID Connect protokoll: lexikális elem az beszerzése](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>A hozzáférési jogkivonatok lekérésére
Szerezzen be a hozzáférési jogkivonatok, módosítsa a bejelentkezési kérelem:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> A kérelem végrehajtása a következő hivatkozásra kattintva. Miután bejelentkezik, a rendszer átirányítja a böngésző https://localhost/myapp/, az ID-tokent és a címsorba kódot. Vegye figyelembe, hogy ehhez a kérelemhez használt `response_mode=query` (bemutatási célokra csak). Azt javasoljuk, hogy használjon `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://Login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>
> 
> 

Engedélyhatókörök ot helyez el a kérelmet, és az `response_type=id_token code`, a v2.0-végpontra biztosítja, hogy a felhasználó hozzájárult szerepelnek az engedélyeket a `scope` lekérdezési paraméter. Engedélyezési kódot az alkalmazásban, az exchange-hozzáférési tokent ad vissza.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=form_post` dolgozunk:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az alkalmazás által kért azonosítója jogkivonat. Az azonosító jogkivonat segítségével ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. Azonosító-jogkivonatokat és azok tartalmát, a további információkat találhat a [v2.0-végponttól jogkivonatok hivatkozás](active-directory-v2-tokens.md). |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot célerőforrás igényelhetnek. Az engedélyezési kód nagyon rövid élettartamú. Általában egy engedélyezési kód körülbelül 10 perc múlva lejár. |
| state |Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is lehet küldeni az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően. Egy hiba történt egy válasz így néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc, amely a besorolására a felmerülő hibákat, és reagálni hibákat is használhatja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |

A lehetséges hibakódok és ajánlott ügyfél válaszok ismertetését lásd: [engedélyezési végpont hibái hibakódok](#error-codes-for-authorization-endpoint-errors).

Van egy engedélyezési kódot és az azonosító tokent, ha a felhasználó bejelentkezhet, és a nevében a hozzáférési jogkivonatok lekérésére. A felhasználó bejelentkezni ellenőrizni kell a Azonosítót jogkivonatban [leírtak pontosan](#validate-the-id-token). Ahhoz, hogy a hozzáférési jogkivonatok, kövesse a témakörben ismertetett a [OAuth-protokoll dokumentációját](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
