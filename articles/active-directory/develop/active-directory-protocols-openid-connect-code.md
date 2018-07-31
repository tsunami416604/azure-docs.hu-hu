---
title: Az OpenID Connect hitelesítési kódfolyamat megismerheti az Azure ad-ben |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható a HTTP-üzenetek webalkalmazásokhoz és webes API-k a bérlő Azure Active Directory és az OpenID Connect használatával való hozzáférés engedélyezésére.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f304a57360f5c847d2a65eb9bd651496c86b1b4d
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357718"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>OpenID Connect és az Azure Active Directory használatával webes alkalmazásokhoz való hozzáférés engedélyezése
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) az OAuth 2.0 protokoll-ra épülő egyszerű személyazonosság rétege. OAuth 2.0 meghatározása beszerzése és használata mechanizmusok **hozzáférési jogkivonatokat** eléréséhez védett erőforrások, de nem határoznak meg, adja meg a azonosító adatok szabványos módszer. Hitelesítés az OAuth 2.0 engedélyezési folyamat bővítményeként OpenID Connect valósítja meg. Információt ad a végfelhasználónak formájában egy `id_token` , amely ellenőrzi a felhasználó identitását, és biztosítja a felhasználóval kapcsolatos főbb profiladatait.

OpenID Connect van, az ajánlott megoldás, ha hoz létre egy webalkalmazást, amely a kiszolgáló által üzemeltetett és a böngészőalapú hozzáférést biztosít.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat OpenID Connect használatával
A legalapvetőbb bejelentkezési folyamata a következő lépéseket tartalmazza: azok az alábbiakban ismertetettek szerint.

![OpenId Connect hitelesítési folyamat](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect metaadat-dokumentum

A metaadat-dokumentum, amely tartalmazza az alkalmazás bejelentkezési végrehajtásához szükséges adatokat a legtöbb OpenID Connect ismerteti. Ez magában foglalja az adatokat, például a használandó URL-címek és a szolgáltatás nyilvános aláírási kulcsok helyét. Az OpenID Connect metaadat-dokumentumban tekinthet meg:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Egy egyszerű JavaScript Object Notation (JSON) a dokumentum metaadatai. Tekintse meg példaként az alábbi kódrészletet. A kódtöredék tartalma részletesen a [OpenID Connect specifikáció](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem küldése
A webalkalmazás kell hitelesíteni a felhasználót, amikor azt kell irányítania a felhasználót, hogy a `/authorize` végpont. Ezt a kérelmet az első alapját képező hasonlít a [OAuth 2.0 engedélyezési kód Flow](active-directory-protocols-oauth-code.md), az néhány fontos különbség:

* A kérésnek tartalmaznia kell a hatókör `openid` a a `scope` paraméter.
* A `response_type` paraméternek tartalmaznia kell `id_token`.
* A kérésnek tartalmaznia kell a `nonce` paraméter.

Így egy mintakérelmet kellene kinéznie:

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
| bérlő |szükséges |A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. A megengedett értékek: bérlő azonosítókat, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |szükséges |Az alkalmazásazonosítót az alkalmazáshoz rendelt Azure AD-vel való regisztrációja. Az Azure Portalon találja. Kattintson a **Azure Active Directory**, kattintson a **Alkalmazásregisztrációk**, válassza ki az alkalmazást, és keresse meg az alkalmazás azonosítóját az alkalmazás oldalán található. |
| response_type |szükséges |Tartalmaznia kell `id_token` OpenID Connect bejelentkezhet. Például a más response_types is tartalmazhat `code`. |
| scope |szükséges |Hatókörök szóközzel elválasztott listáját. Az OpenID Connect, tartalmaznia kell a hatókör `openid`, amelyet a rendszer lefordítja arra a jóváhagyási felhasználói felület a "Bejelentkezés" engedélyt. Más hatókörök is tartalmazhatnak, ha a kérelem hozzájárulás kérése. |
| egyszeri |szükséges |A kérésben, a létrejövő megtalálható az alkalmazás által generált értéket `id_token` jogcímként. Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Az értéke általában egy véletlenszerű, egyedi karakterlánc vagy egy GUID Azonosítót, amelyet a kérés eredetének azonosítására használhatók. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat redirect_uri tulajdonsága. Pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve azt az URL-kódolású kell lennie. |
| response_mode |Ajánlott |Meghatározza az eredményül kapott authorization_code küldi vissza az alkalmazáshoz használandó módszert. Támogatott értékei a következők `form_post` a *HTTP post-űrlap* és `fragment` a *URL-cím töredék*. A webes alkalmazásokhoz, javasoljuk `response_mode=form_post` a legbiztonságosabb átvitelét jogkivonatok az alkalmazás biztosításához. Az alapértelmezett beállítás, ha `response_mode` lehetőség nem része, a `fragment`.|
| state |Ajánlott |A kérésben a token válaszban visszaadott érték. Bármilyen tartalmat, akinél karakterlánc lehet. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások](http://tools.ietf.org/html/rfc6749#section-10.12). Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet is szolgál. |
| parancssor |választható |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg az egyetlen érvényes értékek a következők "bejelentkezés", "none", és a "jóváhagyás". `prompt=login` a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem negating egyszeri bejelentkezéses kényszeríti. `prompt=none` Ellenkező – biztosítja, hogy a felhasználó el minden olyan interaktív kérdés nem egyike. Ha a kérés nem lehet végrehajtani csendes egyszeri bejelentkezéses keresztül, a végpont hibát ad vissza. `prompt=consent` Eseményindítók az OAuth hozzájárulás párbeszédpanel, amelyen felkéri a felhasználót az alkalmazás számára, a felhasználó bejelentkezése után. |
| login_hint |választható |Segítségével előre töltse ki a felhasználónév, e-mail-cím mező a bejelentkezési oldal a felhasználó számára, ha ismeri a kívánt időben felhasználóneve. Gyakran alkalmazások újrahitelesítés, hogy már kinyert a felhasználónevet egy előző jelentkezzen be az során használja ezt a paramétert a `preferred_username` jogcím. |

A felhasználó ezen a ponton megadják hitelesítő adataikat, és válaszában meg kell adnia.

### <a name="sample-response"></a>Mintaválasz
Egy mintaválasz rendelkezik a felhasználó hitelesítése után nézhet ki:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` , amely az alkalmazás kéri. Használhatja a `id_token` ellenőrizze a felhasználó identitását, és a felhasználói munkamenet megkezdéséhez. |
| state |A kérésben is a token válaszban visszaadott érték. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások](http://tools.ietf.org/html/rfc6749#section-10.12). Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet is szolgál. |

### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is elküldheti az `redirect_uri` , az alkalmazás képes kezelni őket megfelelően:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hitelesítési végpont hibák hibakódok
A következő táblázat ismerteti a különböző visszaadható hibakódok a `error` válaszként küldött hibaüzenetben paraméterében.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például egy hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. Ez fejlesztési hiba, és a kezdeti tesztelés során általában történt. |
| unauthorized_client |Az ügyfélalkalmazás számára nem engedélyezett az engedélyezési kódot kér. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| ACCESS_DENIED |Erőforrás tulajdonosának jóváhagyás elutasítva |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem lehet folytatni, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kód választípusához a kérésben. |Javítsa ki, és küldje el újra a kérelmet. Ez fejlesztési hiba, és a kezdeti tesztelés során általában történt. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Ismételje meg a kérelmet. Ezeket a hibákat okozhat az ideiglenes feltételek. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy átmeneti hiba miatt késik. |
| temporarily_unavailable |A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése. |Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem található, vagy azt nem megfelelően van konfigurálva. |Ez azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |

## <a name="validate-the-idtoken"></a>A id_token ellenőrzése
Érkező egy `id_token` azonban nem hitelesíti a felhasználót; kell érvényesíteni az aláírást és a benne szereplő igénylések ellenőrizze a `id_token` az alkalmazáskövetelmények szerint. Az Azure AD-végpont JSON webes jogkivonatainak (JWTs) és a nyilvános kulcsú hitelesítésen használja a jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek.

Ha szeretné ellenőrizni a `id_token` ügyfél kódot, de általános gyakorlat az, hogy küldjön a `id_token` háttérkiszolgálóhoz, és végezze el az érvényesítési hiba. Miután aláírásának ellenőrzése után a `id_token`, van néhány jogcímeket kell ellenőrizni.

Érdemes ellenőrizni a forgatókönyvtől függően további jogcímek is. Néhány gyakori ellenőrzések a következők:

* Annak biztosítása, a felhasználó és szervezet regisztrált az alkalmazáshoz.
* Biztosítása a felhasználó rendelkezik a megfelelő engedélyezési vagy jogosultságokkal
* Egy bizonyos hitelesítés erőssége biztosító történt, például többtényezős hitelesítést.

Miután ellenőrizte a `id_token`, megkezdheti a felhasználói munkamenetet, és használja a jogcímeket a `id_token` információkat kaphat a felhasználó az alkalmazásban. Ez az információ használható megjelenített, a rekordokat, engedélyek, stb. A token típusát és a jogcímek kapcsolatos további információkért olvassa el a [támogatott jogkivonatok és jogcímtípusok](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha szeretne bejelentkezni a felhasználó ki az alkalmazásból, akkor sem elegendő ahhoz, hogy az alkalmazás a cookie-k vagy egyéb célból a munkamenet a felhasználó törlése. Is irányíthatja át a felhasználót, hogy a `end_session_endpoint` a kijelentkezést. Ehhez nem, ha a felhasználó fogja tudni hitelesítse magát újra az alkalmazásba a hitelesítő adatok újbóli megadása nélkül, mert egy érvényes egyszeri bejelentkezés munkamenetet az Azure AD-végpont rendelkeznek.

A felhasználónak egyszerűen csak átirányíthatja a `end_session_endpoint` az OpenID Connect metaadat-dokumentumban felsorolt:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paraméter |  | Leírás |
| --- | --- | --- |
| post_logout_redirect_uri |Ajánlott |Az URL-cím, amely a felhasználó sikeres kijelentkezést követően a rendszer átirányítja. Ha nem tartalmazza, a felhasználó egy általános üzenet jelenik meg. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés
Amikor átirányítja a felhasználót, hogy a `end_session_endpoint`, az Azure AD törli a felhasználói munkamenetet a böngészőből. Azonban a előfordulhat, hogy továbbra is lehet bejelentkezett felhasználó egyéb alkalmazásokhoz, amelyek az Azure ad-ben használnak a hitelesítéshez. Ahhoz, hogy ezek az alkalmazások a felhasználó egyszerre jelentkezzen ki, az Azure AD egy HTTP GET kérést küld a regisztrált `LogoutUrl` , a felhasználó jelenleg be van jelentkezve az alkalmazásokat. Alkalmazások válaszolnia kell ehhez a kérelemhez, amely azonosítja a felhasználói munkamenettől és visszaadó egy `200` választ. Ha szeretne egyszeri bejelentkezés támogatására ki az alkalmazásban, meg kell valósítani például egy `LogoutUrl` az alkalmazás kódjában. Beállíthatja a `LogoutUrl` az Azure Portalról:

1. Keresse meg a [az Azure Portal](https://portal.azure.com).
2. Válassza ki az Active Directory kattintson a fiókra az oldal jobb felső sarkában található.
3. A bal oldali navigációs panelen válassza a **Azure Active Directory**, majd válassza a **alkalmazásregisztrációk** , és válassza ki az alkalmazását.
4. Kattintson a **beállítások**, majd **tulajdonságok** , és keresse meg a **kijelentkezési URL-címe** szövegmezőben. 

## <a name="token-acquisition"></a>Token beszerzése
Számos webalkalmazások nem csak a felhasználó jelentkezzen, de is elérheti az OAuth használatával a felhasználó nevében egy webszolgáltatás kell. Ebben a forgatókönyvben egyesíti az OpenID Connect használata felhasználói hitelesítéshez egy időben Alkalmazászárolás beszerzése során egy `authorization_code` , amely beolvasásához használható `access_tokens` használatával a [OAuth-engedélyezési kód Flow](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Hozzáférési jogkivonat beszerzése
Hozzáférési jogkivonatok beszerzésére, módosítania a bejelentkezési kérés a fent:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

-Engedélyhatókörök, beleértve a kérésben, és használatával `response_type=code+id_token`, a `authorize` végpont biztosítja, hogy a felhasználó hozzájárult a megadott engedélyeket a `scope` lekérdezési paraméter, és vissza az alkalmazás az exchange-hozzáférési kód hozzáférési jogkivonat.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=form_post` következőhöz hasonló:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` , amely az alkalmazás kéri. Használhatja a `id_token` ellenőrizze a felhasználó identitását, és a felhasználói munkamenet megkezdéséhez. |
| Kód |A authorization_code, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód használatával a célként megadott erőforrás-hozzáférési jogkivonat kérése. Authorization_codes rövid életűek, és általában körülbelül 10 perc múlva lejár. |
| state |Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak. |

### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is elküldheti az `redirect_uri` , az alkalmazás képes kezelni őket megfelelően:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

A lehetséges hibakódok és azok javasolt művelet leírását, [hibakódok hitelesítési végpont hibák](#error-codes-for-authorization-endpoint-errors).

Miután is megkapta egy engedélyezési `code` és a egy `id_token`, jelentkezzen be a felhasználó és a hozzáférési tokenek beszerzése a felhasználók nevében. A felhasználó a aláírásához, ellenőrizni kell a `id_token` pontosan a fent leírtak szerint. Hozzáférési jogkivonatok beszerzéséhez kövesse "Az engedélyezési kód használata a hozzáférési jogkivonat kérése" szakaszában leírt lépéseket a [OAuth-protokoll dokumentációját](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
