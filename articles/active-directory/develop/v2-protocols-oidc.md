---
title: Microsoft Identity platform és OpenID Connect protokoll | Azure
titleSuffix: Microsoft identity platform
description: Hozzon létre webalkalmazásokat az OpenID Connect hitelesítési protokoll Microsoft Identity platform-implementációjának használatával.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88f647bbb72c92db194407b677e533a867261ce4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926493"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform és OpenID Connect protokoll

Az OpenID Connect (OIDC) egy OAuth 2,0-re épülő hitelesítési protokoll, amely segítségével biztonságosan lehet bejelentkezni egy felhasználó webalkalmazásba. Ha az OpenID Connect Microsoft Identity platform-végpontjának megvalósítását használja, hozzáadhat bejelentkezési és API-hozzáférést a webalapú alkalmazásokhoz. Ebből a cikkből megtudhatja, hogyan teheti meg ezt a nyelvet, és leírja, hogyan küldhet és fogadhat HTTP-üzeneteket a Microsoft nyílt forráskódú kódtárai használata nélkül.

Az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kibővíti a *OAuth 2,0 hitelesítési protokollját* , *hogy az egyszeri* bejelentkezést a OAuth használatával végezze el. Az OpenID Connect bevezeti az *azonosító jogkivonat*fogalmát, amely egy biztonsági jogkivonat, amely lehetővé teszi az ügyfél számára a felhasználó identitásának ellenőrzését. Az azonosító jogkivonat a felhasználó alapszintű profiljának adatait is beolvassa. Mivel az OpenID Connect kiterjeszti a OAuth 2,0, az alkalmazások biztonságosan kaphatnak *hozzáférési jogkivonatokat*, amelyek az [engedélyezési kiszolgáló](active-directory-v2-protocols.md#the-basics)által védett erőforrások elérésére használhatók. A Microsoft Identity platform végpontja lehetővé teszi, hogy az Azure AD-ben regisztrált harmadik féltől származó alkalmazások hozzáférési jogkivonatokat adjanak ki a biztonságos erőforrások, például a webes API-k számára. További információ arról, hogyan állíthat be egy alkalmazást hozzáférési tokenek kiküldéséhez: alkalmazások [regisztrálása a Microsoft Identity platform-végponttal](quickstart-register-app.md). Javasoljuk, hogy az OpenID connectet használja, ha olyan [webalkalmazást](v2-app-types.md#web-apps) hoz létre, amely egy kiszolgálón fut, és böngészőn keresztül érhető el.

## <a name="protocol-diagram-sign-in"></a>Protokoll diagramja: bejelentkezés

A legalapvetőbb bejelentkezési folyamat a következő ábrán látható lépéseket mutatja be. A jelen cikk részletesen ismerteti az egyes lépéseket.

![OpenID Connect protokoll: bejelentkezés](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentumának beolvasása

Az OpenID Connect egy metaadat-dokumentumot tartalmaz, amely tartalmazza az alkalmazás bejelentkezéséhez szükséges információk nagy részét. Ide tartozik például a használni kívánt URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. A Microsoft Identity platform végpontja esetében ez az OpenID Connect metaadat-dokumentum:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki! Ide [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) kattintva megtekintheti a `common` bérlők konfigurációját.

A `{tenant}` négy érték egyikét is elvégezheti:

| Érték | Leírás |
| --- | --- |
| `common` |Az Azure AD-ben személyes Microsoft-fiók és munkahelyi vagy iskolai fiókkal rendelkező felhasználók is bejelentkezhetnek az alkalmazásba. |
| `organizations` |Csak az Azure AD-ből munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `consumers` |Csak a személyes Microsoft-fiók rendelkező felhasználók jelentkezhetnek be az alkalmazásba. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak egy adott Azure AD-bérlőből származó felhasználók (függetlenül attól, hogy a címtár tagjai munkahelyi vagy iskolai fiókkal, vagy a címtárban lévő vendégek személyes Microsoft-fiók) bejelentkezhetnek az alkalmazásba. Az Azure AD-bérlő vagy a bérlő GUID azonosítójának felhasználóbarát tartományneve is használható. A `consumers` bérlő helyett használhatja a fogyasztói bérlőt `9188040d-6c67-4c5b-b112-36a304b66dad`is.  |

A metaadatok egy egyszerű JavaScript Object Notation (JSON) dokumentum. Példaként tekintse meg a következő kódrészletet. A kódrészlet tartalma teljes mértékben le van írva az [OpenID Connect specifikációban](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
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

Ha az alkalmazás a [jogcím-leképezési](active-directory-claims-mapping.md) funkció használata miatt egyéni aláíró kulcsokkal rendelkezik, akkor az alkalmazás azonosítóját `appid` tartalmazó lekérdezési paramétert hozzá kell fűzni ahhoz, hogy `jwks_uri` az alkalmazáshoz tartozó aláíró kulcs adataira mutasson. Például a a `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` következőt `jwks_uri` tartalmazza `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`:.

Ez a metaadat-dokumentum általában egy OpenID Connect-függvénytár vagy SDK konfigurálására használható. a könyvtár a metaadatokat fogja használni a munkája elvégzéséhez. Ha azonban nem használ előre elkészített OpenID Connect-függvénytárat, a cikk további részében ismertetett lépéseket követve a Microsoft Identity platform végpontján keresztül is bejelentkezhet a webes alkalmazásba.

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, akkor a felhasználó a `/authorize` végpontra irányíthatja a felhasználót. Ez a kérelem hasonló a [OAuth 2,0 engedélyezési kód](v2-oauth2-auth-code-flow.md)első szakaszához, ezekkel a fontos különbségekkel:

* A kérelemnek tartalmaznia kell `openid` a hatókört `scope` a paraméterben.
* A `response_type` paraméternek tartalmaznia `id_token`kell a következőket:.
* A kérelemnek tartalmaznia kell `nonce` a paramétert.

> [!IMPORTANT]
> Ahhoz, hogy sikeresen igényeljen egy azonosító jogkivonatot a/Authorization-végponttól, a [regisztrációs portálon](https://portal.azure.com) lévő alkalmazás-regisztrációnak a hitelesítés lapon engedélyeznie kell a id_tokens engedélyezését ( `oauth2AllowIdTokenImplicitFlow` amely a jelölőt az [alkalmazás jegyzékfájljában](reference-app-manifest.md) adja meg `true`). Ha nincs engedélyezve, `unsupported_response` hibaüzenetet kap: "a (z) response_type bemeneti paraméter megadott értéke nem engedélyezett ehhez az ügyfélhez. A várt érték a "code" (kód)

Például:

```HTTP
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
> A kérelem végrehajtásához kattintson a következő hivatkozásra. A bejelentkezést követően a böngésző átirányítja a `https://localhost/myapp/`-re, és a címsorban egy azonosító jogkivonat jelenik meg. Vegye figyelembe, hogy ez `response_mode=fragment` a kérelem (csak demonstrációs célokra) használja. Javasoljuk, hogy használja `response_mode=form_post`a-t.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | A kérelem elérési `{tenant}` útjának értékét követve szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek `common` `organizations` `consumers`:,, és bérlői azonosítók. További információ: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `response_type` | Kötelező | Tartalmaznia `id_token` kell az OpenID Connect bejelentkezést. Más `response_type` értékeket is tartalmazhatnak, például: `code`. |
| `redirect_uri` | Ajánlott | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-címet kell kódolni. Ha nem található, a végpont véletlenszerűen kiválaszt egy regisztrált redirect_uri, hogy visszaküldje a felhasználót a következőnek:. |
| `scope` | Kötelező | A hatókörök szóközzel tagolt listája. Az OpenID Connect esetében tartalmaznia kell a hatókört `openid`, amely a hozzájárulási felhasználói felületen a "Bejelentkezés" engedélyre van lefordítva. A kérelemben szereplő további hatóköröket is megadhatja a kéréshez a belefoglaláshoz. |
| `nonce` | Kötelező | Az alkalmazás által generált kérelemben szereplő érték, amely az eredményül kapott id_token értéket tartalmazza jogcímként. Az alkalmazás ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
| `response_mode` | Ajánlott | Meghatározza azt a módszert, amellyel az eredményül kapott engedélyezési kódot vissza lehet küldeni az alkalmazásnak. A következők egyike lehet: `form_post` vagy `fragment`. A webalkalmazások esetében javasoljuk, `response_mode=form_post`hogy a használatával biztosítsa a tokenek legbiztonságosabb átvitelét az alkalmazásba. |
| `state` | Ajánlott | A kérelemben szereplő érték, amely a jogkivonat-válaszban is szerepelni fog. A kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például a lap vagy a felhasználó megtekintése. |
| `prompt` | Optional | Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben `login` `none`az egyetlen érvényes érték a, a és `consent`a. A `prompt=login` jogcím arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, amely megtagadja az egyszeri bejelentkezést. A `prompt=none` jogcím az ellenkezője. Ez a jogcím biztosítja, hogy a felhasználó semmilyen interaktív kéréssel sem jelenik meg. Ha a kérést nem lehet csendes úton végrehajtani egyszeri bejelentkezéssel, a Microsoft Identity platform-végpont hibát jelez. A `prompt=consent` jogcím a felhasználó bejelentkezése után elindítja a OAuth-engedélyezési párbeszédpanelt. A párbeszédpanel arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint` | Optional | Ezzel a paraméterrel előre kitöltheti a felhasználó bejelentkezési oldalának username és e-mail-cím mezőjét, ha már ismeri a felhasználónevet az idő előtt. Az alkalmazások gyakran ezt a paramétert használják az újrahitelesítés során, miután már kicsomagolta a felhasználónevet egy korábbi bejelentkezésből `preferred_username` a jogcím használatával. |
| `domain_hint` | Optional | A felhasználó tartománya egy összevont címtárban.  Ezzel kihagyhatja a felhasználó e-mail-alapú felderítési folyamatát, amely egy valamivel egyszerűbb felhasználói élmény érdekében a bejelentkezési oldalon halad át. A helyszíni címtárban (például AD FS) összevont bérlők esetében ez gyakran zökkenőmentes bejelentkezést eredményez a meglévő bejelentkezési munkamenet miatt. |

Ekkor a rendszer felszólítja a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform végpontja ellenőrzi, hogy a felhasználó beleegyezett-e a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó nem járult hozzá ezen engedélyek bármelyikéhez, a Microsoft Identity platform végpontja kéri a felhasználót, hogy fogadja el a szükséges engedélyeket. További információ: [engedélyek, beleegyező és több-bérlős alkalmazások](v2-permissions-and-consent.md).

A felhasználó hitelesítése és a hozzájárulás engedélyezése után a Microsoft Identity platform végpontja az alkalmazásra adott választ küld vissza a megadott átirányítási URI-n a `response_mode` paraméterben megadott metódus használatával.

### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz, ha a következőt használja `response_mode=form_post` :

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. A `id_token` paraméter használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további információkért tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md). |
| `state` | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza

A hibaüzenetek az átirányítási URI-ba is küldhetők, hogy az alkalmazás kezelni tudja őket. A hiba a következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai

A következő táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer `error` a hiba válaszának paraméterében adhat vissza:

| Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- |
| `invalid_request` | Protokollhiba, például hiányzó, kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy olyan fejlesztési hiba, amelyet általában a kezdeti tesztelés során kapnak. |
| `unauthorized_client` | Az ügyfélalkalmazás nem kérhet engedélyezési kódot. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és adja hozzá az Azure AD-hez. |
| `access_denied` | Az erőforrás tulajdonosa megtagadta a jóváhagyást. |Az ügyfélalkalmazás értesíti a felhasználót, hogy csak akkor tud továbblépni, ha a felhasználó beleegyezett. |
| `unsupported_response_type` |Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. |Javítsa ki és küldje el újra a kérelmet. Ez egy olyan fejlesztési hiba, amelyet általában a kezdeti tesztelés során kapnak. |
| `server_error` | A kiszolgáló váratlan hibát észlelt. |Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás egy ideiglenes hiba miatt késlelteti a felhasználót, hogy a válasza késik. |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. |Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |
| `invalid_resource` | A célként megadott erőforrás érvénytelen, mert vagy nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. |Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához útmutatást adjon. |

## <a name="validate-the-id-token"></a>AZONOSÍTÓ jogkivonat ellenőrzése

Ha csak egy id_token kap, nem elegendő a felhasználó hitelesítéséhez; érvényesítse a id_token aláírását, és ellenőrizze, hogy az alkalmazás követelményei szerint milyen jogcímeket kell megadnia a jogkivonatban. A Microsoft Identity platform végpont [JSON web tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú titkosítást használ a jogkivonatok aláírásához, és ellenőrzi, hogy érvényesek-e.

Dönthet úgy is, hogy érvényesíti az `id_token` ügyfél kódját, de az általános gyakorlat az, hogy `id_token` elküldje a-t egy háttér-kiszolgálónak, és ott hajtja végre az ellenőrzést. Miután ellenőrizte a id_token aláírását, van néhány jogcím, amelyet ellenőrizni kell. További információért lásd a [ `id_token` hivatkozást](id-tokens.md) , beleértve a [jogkivonatok érvényesítését](id-tokens.md#validating-an-id_token) és az [aláíró kulcsok átváltásával kapcsolatos fontos információkat](active-directory-signing-key-rollover.md). Azt javasoljuk, hogy használjon egy könyvtárat a tokenek elemzéséhez és érvényesítéséhez – legalább egy elérhető a legtöbb nyelven és platformon.

A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Annak biztosítása, hogy a felhasználó/szervezet regisztrálva legyen az alkalmazásra.
* Annak biztosítása, hogy a felhasználó megfelelő jogosultságokkal rendelkezzen
* A hitelesítés bizonyos erősségének biztosítása, például a többtényezős hitelesítés.

Miután ellenőrizte a id_token, elindíthat egy munkamenetet a felhasználóval, és a id_token jogcímeit használva adatokat szerezhet be a felhasználóval kapcsolatban az alkalmazásban. Ezek az információk megjeleníthetők, rekordok, személyre szabás stb. esetén használhatók.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha a felhasználót az alkalmazásból szeretné kijelentkezni, nem elegendő az alkalmazás cookie-jai törléséhez, vagy más módon a felhasználó munkamenetének befejezéséhez. A felhasználót a Microsoft Identity platform végpontján is át kell irányítani a kijelentkezéshez. Ha ezt nem teszi meg, a felhasználó a hitelesítő adatok újbóli megadása nélkül újra hitelesíti az alkalmazást, mert a Microsoft Identity platform-végponttal érvényes egyszeri bejelentkezéses munkamenet lesz.

Átirányíthatja a felhasználót az OpenID `end_session_endpoint` Connect metadata dokumentum listájában szereplő listára:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Állapot | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Ajánlott | Az URL-cím, amelyet a felhasználó átirányított a sikeres kijelentkezés után. Ha a paraméter nincs feltüntetve, a felhasználó egy általános üzenetet jelenít meg, amelyet a Microsoft Identity platform végpontja generált. Ennek az URL-címnek meg kell egyeznie az alkalmazás regisztrációs portálján az alkalmazáshoz regisztrált átirányítási URI-k egyikével. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a felhasználót a `end_session_endpoint`alkalmazásba, a Microsoft Identity platform végpontja törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is bejelentkezett a Microsoft-fiókokat használó más alkalmazásokba a hitelesítéshez. Annak engedélyezéséhez, hogy az alkalmazások egyidejűleg írják alá a felhasználót, a Microsoft Identity platform végpontja egy HTTP GET kérelmet küld `LogoutUrl` az összes olyan alkalmazás regisztrálásához, amelyhez a felhasználó jelenleg be van jelentkezve. Az alkalmazásoknak válaszolnia kell erre a kérelemre a felhasználót azonosító munkamenetek törlésével `200` és a válasz visszaadásával. Ha szeretné támogatni az egyszeri kijelentkezést az alkalmazásban, akkor az alkalmazás kódjában be `LogoutUrl` kell vezetnie egy ilyen alkalmazást. Az `LogoutUrl` alkalmazást az alkalmazás regisztrációs portálján állíthatja be.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokoll diagramja: hozzáférési jogkivonat beszerzése

Számos webalkalmazásnak nem csak a felhasználónak kell aláírnia a szolgáltatást, hanem a felhasználó nevében egy webszolgáltatáshoz is hozzá kell férnie a OAuth használatával. Ez a forgatókönyv ötvözi az OpenID connectet a felhasználói hitelesítéshez, miközben olyan engedélyezési kódot kap, amelyet a hozzáférési jogkivonatok beszerzéséhez használhat, ha a OAuth engedélyezési kód folyamatát használja.

A teljes OpenID Connect-bejelentkezés és a jogkivonat-gyűjtési folyamat a következő diagramhoz hasonlóan néz ki. Az egyes lépéseket részletesen ismertetjük a cikk következő fejezeteiben.

![OpenID Connect protokoll: token beszerzése](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beolvasása
Hozzáférési jogkivonatok beszerzéséhez módosítsa a bejelentkezési kérést:

```HTTP
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
> A kérelem végrehajtásához kattintson a következő hivatkozásra. A bejelentkezést követően a rendszer átirányítja a böngészőt `https://localhost/myapp/`, és egy azonosító jogkivonatot és egy kódot a címsorba. Vegye figyelembe, hogy ez `response_mode=fragment` a kérelem csak demonstrációs célokat szolgál. Javasoljuk, hogy használja `response_mode=form_post`a-t.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

A kérelemben és a használatával `response_type=id_token code`a engedélyezési hatókörökkel együtt a Microsoft Identity platform Endpoint biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Egy hozzáférési jogkivonat cseréjére szolgáló engedélyezési kódot ad vissza az alkalmazásnak.

### <a name="successful-response"></a>Sikeres válasz

A használatból `response_mode=form_post` való sikeres válasz a következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | Az alkalmazás által kért azonosító jogkivonat. Az azonosító token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további részletekért tekintse meg a [ `id_tokens` hivatkozást](id-tokens.md). |
| `code` | Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. Az engedélyezési kód rövid életű. Az engedélyezési kód általában körülbelül 10 percen belül lejár. |
| `state` | Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza

A rendszer az átirányítási URI-nak is küldhet hibákat, hogy az alkalmazás megfelelően tudja kezelni őket. A hiba a következőképpen néz ki:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására és a hibákra való reagálásra használható. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

A lehetséges hibakódok és az ajánlott ügyfél-válaszok leírását lásd: [hibakódok az engedélyezési végpont hibáihoz](#error-codes-for-authorization-endpoint-errors).

Ha rendelkezik egy engedélyezési kóddal és egy azonosító jogkivonattal, akkor aláírhatja a felhasználót a alkalmazásban, és hozzáférési jogkivonatokat kaphat a nevében. A felhasználó a alkalmazásban való aláírásához [pontosan a leírtak szerint](id-tokens.md#validating-an-id_token)ellenőriznie kell az azonosító tokent. A hozzáférési jogkivonatok beszerzéséhez kövesse a [OAuth Code flow dokumentációjában](v2-oauth2-auth-code-flow.md#request-an-access-token)ismertetett lépéseket.
