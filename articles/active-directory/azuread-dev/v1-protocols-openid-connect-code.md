---
title: Webalkalmazás-hozzáférés engedélyezése az OpenID Connect & az Azure AD-vel | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan http-üzenetek használatával engedélyezheti a hozzáférést a webalkalmazások és webes API-k a bérlőben az Azure Active Directory és az OpenID Connect használatával.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154440"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Hozzáférés engedélyezése webalkalmazásoknak OpenID Connect és az Azure Active Directory használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Az OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) egy egyszerű identitásréteg, amely az OAuth 2.0 protokollra épül. Az OAuth 2.0 meghatározza a [**hozzáférési jogkivonatok**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) beszerzésének és használatának mechanizmusait a védett erőforrások eléréséhez, de nem határoznak meg szabványos módszereket az identitásadatok megadására. Az OpenID Connect az OAuth 2.0 engedélyezési folyamatának kiterjesztéseként valósítja meg a hitelesítést. Információt nyújt a végfelhasználóról egy olyan [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) formában, amely ellenőrzi a felhasználó identitását, és alapvető profilinformációkat a felhasználóról.

OpenID Connect a mi ajánlásunk, ha épít egy webes alkalmazás, amely otthont a szerveren, és keresztül érhető el a böngésző.

## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálja az alkalmazást az Azure Active Directory (Azure AD) bérlőjével. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
   
1. Válassza ki az Azure AD-bérlőt a fiók kiválasztásával a lap jobb felső sarkában, majd válassza ki a **Címtár-navigáció tikkasztása,** majd válassza ki a megfelelő bérlőt. 
   - Hagyja ki ezt a lépést, ha csak egy Azure AD-bérlő van a fiókjában, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.
   
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
   
1. Az **Azure Active Directory** bal oldali menüjében válassza az **Alkalmazásregisztrációk**lehetőséget, majd az **Új regisztráció**lehetőséget.
   
1. Kövesse az utasításokat az új alkalmazás létrehozásához. Nem számít, ha ez egy webes alkalmazás vagy egy nyilvános kliens (mobil & asztali) alkalmazás ez a bemutató, de ha szeretne konkrét példákat webes alkalmazások vagy nyilvános kliens alkalmazások, nézd meg [a quickstarts](v1-overview.md).
   
   - A **név** az alkalmazás neve, amely a végfelhasználók számára ad leírást az alkalmazásról.
   - A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
   - Adja meg az **átirányítási URI-t**. Webes alkalmazások esetén ez az alkalmazás alap URL-címe, ahol a felhasználók bejelentkezhetnek.  Például: `http://localhost:12345`. Nyilvános ügyfél (mobil & asztali), az Azure AD használja, hogy token válaszok at. Adja meg az alkalmazáshoz tartozó értéket.  Például: `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Miután befejezte a regisztrációt, az Azure AD egyedi ügyfélazonosítót **(az alkalmazásazonosítót)** rendel hozzá az alkalmazáshoz. Erre az értékre a következő szakaszokban van szükség, ezért másolja az alkalmazáslapról.
   
1. Ha meg szeretné találni az alkalmazást az Azure Portalon, válassza **az Alkalmazásregisztrációk**lehetőséget, majd válassza **az Összes alkalmazás megtekintése**lehetőséget.

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat az OpenID Connect használatával

A legalapvetőbb bejelentkezési folyamat a következő lépéseket tartalmazza – mindegyiket az alábbiakban részletesen ismertetjük.

![OpenId Connect hitelesítési folyamat](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect metaadat-dokumentum

Az OpenID Connect olyan metaadat-dokumentumot ír le, amely az alkalmazás bejelentkezéshez szükséges legtöbb információt tartalmazza. Ez olyan információkat is tartalmaz, mint például a használandó URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. Az OpenID Connect metaadat-dokumentum a következő helyen található:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
A metaadatok egy egyszerű JavaScript objektumjelölés (JSON) dokumentum. Tekintse meg a következő kódrészletet egy példát. A kódrészlet tartalmát teljes körűen leírjuk az [OpenID Connect specifikációban.](https://openid.net) Vegye figyelembe, hogy a fenti `common` {tenant} helyett egy bérlőazonosító megadása bérlőspecifikus URI-kat eredményez a visszaadott JSON-objektumban.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Ha az alkalmazás a [jogcím-leképezési](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) funkció használata miatt rendelkezik egyéni aláírási kulcsokkal, hozzá kell fűznie egy `appid` lekérdezési paramétert, amely tartalmazza az alkalmazásazonosítót, hogy `jwks_uri` az alkalmazás aláírási kulcsinformációira mutasson. Például: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` a `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`részét tartalmazza.

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, a felhasználónak a `/authorize` végponthoz kell irányítania. Ez a kérés hasonló az [OAuth 2.0 Engedélyezési kód folyamatának első szakaszához,](v1-protocols-oauth-code.md)néhány fontos különbséggel:

* A kérelemnek tartalmaznia kell a hatókört `openid` a `scope` paraméterben.
* A `response_type` paraméternek `id_token`tartalmaznia kell a .
* A kérelemnek `nonce` tartalmaznia kell a paramétert.

Tehát egy minta kérés így nézne ki:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| Bérlő |kötelező |A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek például bérlői `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` azonosítók, vagy `common` bérlőfüggetlen jogkivonatok |
| client_id |kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító, amikor regisztrálta az Azure AD-vel. Ez az Azure Portalon található. Kattintson **az Azure Active Directory**, kattintson az **Alkalmazásregisztrációk**, válassza ki az alkalmazást, és keresse meg az alkalmazás azonosítóját az alkalmazás lapon. |
| response_type |kötelező |`id_token` Tartalmaznia kell az OpenID Connect bejelentkezéshez. Más response_types is tartalmazhat, `code` például vagy `token`. |
| scope | Ajánlott | Az OpenID Connect specifikáció `openid`megköveteli a hatókört , amely a hozzájárulási felhasználói felület "Bejelentkezés" engedélyére utal. Ezt és más OIDC-hatóköröket a v1.0-s végpont figyelmen kívül hagyja, de továbbra is ajánlott eljárás a szabványoknak megfelelő ügyfelek számára. |
| nonce között |kötelező |Az alkalmazás által létrehozott, a kérelemben szereplő érték, `id_token` amely az eredményként jogcímként szerepel. Az alkalmazás ezután ellenőrizheti ezt az értéket a token-visszajátszási támadások csökkentése érdekében. Az érték általában egy randomizált, egyedi karakterlánc vagy GUID, amely a kérelem eredetének azonosítására használható. |
| redirect_uri | Ajánlott |Az alkalmazás redirect_uri, ahol az alkalmazás elküldheti és fogadhatja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_uris egyikével, kivéve, hogy url-kódolni kell. Ha hiányzik, a felhasználói ügynök véletlenszerűen visszaküldi az alkalmazáshoz regisztrált átirányítási URI-k egyikét. A maximális hossz 255 bájt |
| response_mode |választható |Megadja azt a módszert, amelyet az eredményül kapott authorization_code visszaküldéséhez kell használni az alkalmazásba. A támogatott `form_post` értékek a `fragment` *HTTP-űrlapbejegyzésre* és az *URL-töredékre*szolgálnak. Webes alkalmazások esetén azt `response_mode=form_post` javasoljuk, hogy a tokenek legbiztonságosabb átvitelének biztosítása érdekében. A id_token is beleértve az `fragment`alapértelmezett folyamatot is, az .|
| state |Ajánlott |A kérelemben szereplő érték, amely a jogkivonat-válaszban kerül vissza. Ez lehet egy sor bármilyen tartalmat, amit szeretne. A véletlenszerűen generált egyedi értéket általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)használják. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak. |
| Gyors |választható |A szükséges felhasználói beavatkozás típusát jelzi. Jelenleg az egyetlen érvényes érték a "login", a "none" és a "consent". `prompt=login`kényszeríti a felhasználót, hogy adja meg hitelesítő adatait, hogy a kérelem, negating egyszeri bejelentkezés. `prompt=none`az ellenkezője - ez biztosítja, hogy a felhasználó nem jelenik meg semmilyen interaktív prompt egyáltalán. Ha a kérés nem hajtható végre csendben egyszeri bejelentkezéssel, a végpont hibát ad vissza. `prompt=consent`elindítja az OAuth hozzájárulási párbeszédpanelt, miután a felhasználó bejelentkezik, és arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| login_hint |választható |A felhasználó bejelentkezési lapjának felhasználónév/e-mail cím mezőjének előzetes kitöltésére használható, ha előre ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután már kinyerték a felhasználónevet egy korábbi bejelentkezésből a `preferred_username` jogcím használatával. |

Ezen a ponton a felhasználónak meg kell adnia a hitelesítő adatait, és befejezni a hitelesítést.

### <a name="sample-response"></a>Mintaválasz

A felhasználó hitelesítése `redirect_uri` után a bejelentkezési kérelemben megadott mintaválasz a következőkre néz ki:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az, `id_token` amit az alkalmazás kért. Használhatja a `id_token` a felhasználó identitásának ellenőrzésére, és megkezdheti a munkamenetet a felhasználóval. |
| state |A kérelemben szereplő érték, amely a jogkivonat-válaszban is megjelenik. A véletlenszerűen generált egyedi értéket általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)használják. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak. |

### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető a, `redirect_uri` így az alkalmazás tudja kezelni őket megfelelően:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| error_description |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hibakódok az engedélyezési végponthibáihoz

Az alábbi táblázat a hibaválasz `error` paraméterében visszaadható különböző hibakódokat ismerteti.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó szükséges paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában fogott a kezdeti tesztelés során. |
| unauthorized_client |Az ügyfélalkalmazás nem kérhet engedélyezési kódot. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| access_denied |Az erőforrás tulajdonosa megtagadta a hozzájárulást |Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem folytatható, ha a felhasználó nem járul hozzá. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kérelemben lévő választípust. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában fogott a kezdeti tesztelés során. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezek a hibák ideiglenes körülményekből eredhetnek. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz átmeneti hiba miatt késik. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |
| invalid_resource |A célerőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |

## <a name="validate-the-id_token"></a>A id_token ellenőrzése

Csak fogadó `id_token` egy nem elegendő ahhoz, hogy hitelesítse a felhasználót; ellenőriznie kell az aláírást, `id_token` és ellenőriznie kell a jogcímeket az alkalmazás követelményeiben. Az Azure AD-végpont JSON webtokenek (JWTs) és nyilvános kulcsú kriptográfia segítségével írja alá a jogkivonatokat, és ellenőrizze, hogy azok érvényesek-e.

Dönthet úgy, hogy `id_token` érvényesíti az ügyfélkódban, de `id_token` általános gyakorlat, hogy elküldi a háttérkiszolgálóra, és ott hajtja végre az ellenőrzést. 

Előfordulhat, hogy a forgatókönyvtől függően további jogcímeket is érvényesíteni szeretne. Néhány gyakori érvényesítés:

* Annak biztosítása, hogy a felhasználó/szervezet feliratkozott az alkalmazásra.
* Annak biztosítása, hogy a felhasználó `wids` megfelelő `roles` jogosultságokkal/jogosultságokkal rendelkezzen a vagy a jogcímek használatával. 
* A hitelesítés bizonyos erősségének, például a többtényezős hitelesítésnek a biztosítása.

Miután érvényesítette `id_token`a , megkezdheti a munkamenetet a `id_token` felhasználóval, és a jogcímek segítségével információt kaphat a felhasználóról az alkalmazásban. Ez az információ használható megjelenítésre, rekordokra, személyre szabásra stb. A jogcímekről és a jogcímekről `id_tokens` az [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című további információt talál.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha ki szeretné jelentkezni a felhasználót az alkalmazásból, nem elegendő törölni az alkalmazás cookie-jait, vagy más módon befejezni a munkamenetet a felhasználóval. Azt is át kell irányítania a felhasználót, hogy a `end_session_endpoint` kijelentkezés. Ha ezt nem teszi meg, a felhasználó újra hitelesítheti magát az alkalmazásba anélkül, hogy újra meg kellene adnia a hitelesítő adatait, mert érvényes egyszeri bejelentkezési munkamenettel rendelkezik az Azure AD-végponttal.

Egyszerűen átirányíthatja a felhasználót az `end_session_endpoint` OpenID Connect metaadat-dokumentumban felsoroltakra:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paraméter |  | Leírás |
| --- | --- | --- |
| post_logout_redirect_uri |Ajánlott |Az URL-címet, amelya felhasználót a sikeres kijelentkezés után át kell irányítani.  Ennek az URL-címnek meg kell egyeznie az alkalmazáshoz az alkalmazás regisztrációs portálján regisztrált átirányítási URI-k egyikével.  Ha *post_logout_redirect_uri* nem szerepel, a felhasználó egy általános üzenetet jelenít meg. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a `end_session_endpoint`felhasználót a , az Azure AD törli a felhasználó munkamenetét a böngészőből. Azonban a felhasználó továbbra is bejelentkezett más alkalmazások, amelyek az Azure AD hitelesítéshez. Annak érdekében, hogy ezek az alkalmazások egyszerre jelentkezzenek ki a `LogoutUrl` felhasználóból, az Azure AD http GET-kérelmet küld az összes olyan alkalmazás regisztrált, amelya felhasználó jelenleg be van jelentkezve. Az alkalmazásoknak válaszolniuk kell erre a kérésre a `200` felhasználót azonosító munkamenetek törlésével és a válasz visszaküldésével. Ha szeretné támogatni az egyetlen kijelentkezést az `LogoutUrl` alkalmazásban, végre kell hajtania egy ilyen az alkalmazás kódjában. Beállíthatja az `LogoutUrl` Azure Portalon:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Válassza ki az Active Directoryt a lap jobb felső sarkában található fiókra kattintva.
3. A bal oldali navigációs panelen válassza az **Azure Active Directory**, majd válassza az alkalmazás **regisztrációk,** és válassza ki az alkalmazást.
4. Kattintson a **Beállítások**gombra, majd a **Tulajdonságok** gombra, és keresse meg a **Kijelentkezés URL-címét.** 

## <a name="token-acquisition"></a>Token-akvizíció
Számos webalkalmazásnak nem csak a felhasználót kell bejelentkeznie, hanem az OAuth-ot használó felhasználó nevében is hozzá kell férnem egy webszolgáltatáshoz. Ez a forgatókönyv kombinálja az OpenID Connect `authorization_code` et a felhasználói `access_tokens` hitelesítéshez, miközben egyidejűleg beszerez egy olyan kódot, amely az [OAuth engedélyezési kódfolyamat](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)használatával használható.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beszereznie
A hozzáférési jogkivonatok beszerzéséhez felülről kell módosítania a bejelentkezési kérelmet:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Engedélyható körök a kérelemben, `response_type=code+id_token`és `authorize` a használata, a végpont biztosítja, hogy a `scope` felhasználó hozzájárult a lekérdezési paraméterben megjelölt engedélyeket, és visszaadja az alkalmazás egy engedélyezési kódot, hogy kicseréljék a hozzáférési jogkivonatot.

### <a name="successful-response"></a>Sikeres válasz

A használatával történő `redirect_uri` sikeres `response_mode=form_post`válasz a következőképpen néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az, `id_token` amit az alkalmazás kért. Használhatja a `id_token` a felhasználó identitásának ellenőrzésére, és megkezdheti a munkamenetet a felhasználóval. |
| code |Az alkalmazás által kért authorization_code. Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet a célerőforráshoz. Authorization_codes rövid életű, és általában lejár után körülbelül 10 perc. |
| state |Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető a, `redirect_uri` így az alkalmazás tudja kezelni őket megfelelően:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| error_description |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |

A lehetséges hibakódok és az ajánlott ügyfélművelet leírását az [engedélyezési végponthibák hibakódok című témakörében tartalmazza.](#error-codes-for-authorization-endpoint-errors)

Miután megkapta az `code` engedélyt `id_token`és a , bejelentkezhet a felhasználó, és hozzáférési [jogkivonatokat](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) kaphat a nevükben. A felhasználó bejelentkezéséhez pontosan `id_token` a fent leírt módon kell ellenőriznie. A hozzáférési jogkivonatok beszerzéséhez kövesse az [OAuth kódfolyamat dokumentációjának](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)"Az engedélyezési kód használata hozzáférési jogkivonat ának kérése" című részében leírt lépéseket.

## <a name="next-steps"></a>További lépések

* További információ a [hozzáférési jogkivonatokról.](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* További információ a [ `id_token` követelésekről és](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)a követelésekről.
