---
title: "Az OpenID Connect hitelesítési folyamata megismerése az Azure AD |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan használható a HTTP-üzenetek webalkalmazások és webes API-k használata az Azure Active Directory és az OpenID Connect-bérlőben hozzáférés hitelesítése."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3a813d73dc8a80c46e1b7500ec72ccb2a47bc6d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Az OpenID Connect és az Azure Active Directory használatával webes alkalmazásokhoz való hozzáférés engedélyezésére
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) egy egyszerű identitásrétegének az OAuth 2.0 protokollt platformra épül. OAuth 2.0 meghatározása mechanizmusok az beszerzése és használata **hozzáférési jogkivonatok** hozzáférni védett erőforrásokhoz, de azok nem határoznak meg szabványos módszerek azonosító adatok megadása. Hitelesítési OpenID Connect valósítja meg az OAuth 2.0 hitelesítési folyamat részeként. Információt ad a végfelhasználónak formájában egy `id_token` , amely ellenőrzi a felhasználó és a felhasználó alapvető profiladataihoz tájékoztatást.

Az ajánlott megoldás OpenID Connect, ha böngészőalapú hozzáférést biztosít, és a kiszolgálón futtatott webalkalmazás.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Használja az OpenID Connect hitelesítési folyamat
A legalapvetőbb bejelentkezési folyamata a következő lépéseket tartalmazza, mert azok az alábbiakban ismertetettek.

![OpenId Connect hitelesítési folyamat](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect metaadat-dokumentum

A legtöbb alkalmazás bejelentkezési végrehajtásához szükséges adatokat tartalmazó metaadat-dokumentum OpenID Connect ismerteti. Ide tartoznak például az URL-címek használatára és a szolgáltatás nyilvános aláírási kulcsok helyét. Az OpenID Connect metaadat-dokumentum helyen találhatók:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Egy egyszerű JavaScript Object Notation (JSON) dokumentum metaadatai. Tekintse meg az alábbi részlet egy példát. A kódrészletet tartalma részletesen a [OpenID Connect specification](https://openid.net).

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

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése
Ha a webes alkalmazás kell hitelesíteni a felhasználót, azt kell irányítani a felhasználót, hogy a `/authorize` végpont. A kérelem hasonlít az első szakasza a [OAuth 2.0 hitelesítési kód Flow](active-directory-protocols-oauth-code.md), néhány fontos különbség a:

* A kérelem tartalmaznia kell a hatókör `openid` a a `scope` paraméter.
* A `response_type` paraméternek tartalmaznia kell `id_token`.
* A kérelem tartalmaznia kell a `nonce` paraméter.

Ezért egy minta kérelem néz ki:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| Bérlői |Szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |Szükséges |Az Azure ad-vel regisztrált az alkalmazáshoz hozzárendelt alkalmazásazonosító. Ez az Azure portálon találja meg. Kattintson a **Azure Active Directory**, kattintson a **App regisztrációk**, válassza ki az alkalmazást, és keresse meg az alkalmazásazonosítót a alkalmazás lapján. |
| response_type |Szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet.  Például a más response_types is tartalmazhat `code`. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája.  Az OpenID Connect, magában kell foglalnia a hatókör `openid`, amely az eszköz a hozzájárulási felhasználói felületén a "Bejelentkezés" engedélyt.  A kérésben a hozzájárulás kérése más hatókörök is. |
| Nonce |Szükséges |A kérelem, az alkalmazás, a létrejövő szereplő által generált szereplő érték `id_token` jogcímként.  Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket.  Az érték általában egy véletlenszerű, egyedi karakterlánc vagy a GUID Azonosítóját, a kérelem származási azonosítására használható. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszok redirect_uri.  Ez pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve az url-kódolású kell lennie. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott authorization_code küldi vissza az alkalmazás.  Támogatott értékek a következők `form_post` a *HTTP közzétett űrlapból* vagy `fragment` a *URL-cím töredék*.  Webes alkalmazásokhoz, javasoljuk `response_mode=form_post` ahhoz, hogy az alkalmazás a jogkivonatok legbiztonságosabb átvitelét. |
| state |Ajánlott |A token válaszként visszaadott a kérelemben szereplő érték.  Bármely, a kívánt tartalmat karakterlánc lehet.  Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások megelőzése](http://tools.ietf.org/html/rfc6749#section-10.12).  Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a is használatos. |
| parancssor |választható |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges.  Jelenleg a csak érvényes értékei a "bejelentkezés", "none", és "".  `prompt=login`arra kényszeríti a felhasználó megadja a hitelesítő adataikat, hogy kérésre nem lehet negálni egyszeri bejelentkezést.  `prompt=none`Ellenkező - biztosítja, hogy a felhasználó számára nem jelenik meg minden bármely interaktív kérdés.  Ha a kérelem nem hajtható végre csendes keresztül egyszeri bejelentkezést, a végpont hibát ad vissza.  `prompt=consent`Eseményindítók OAuth-alapú hozzájárulás párbeszédpanelen, a felhasználó bejelentkezése után az, amely kéri a felhasználót, hogy engedélyezze, hogy az alkalmazás. |
| login_hint |választható |Segítségével előre töltse ki a bejelentkezési oldal a felhasználó a felhasználónév vagy e-mail cím mező, ha tudja, hogy időben a felhasználónevét.  Gyakran alkalmazások újrahitelesítés, hogy már kivont a felhasználónév egy korábbi bejelentkezési használatával során használja ezt a paramétert a `preferred_username` jogcímek. |

Ezen a ponton a felhasználónak kapcsolatba kell adnia a hitelesítő adatait, és a hitelesítés végrehajtásához.

### <a name="sample-response"></a>Mintaválasz
A minta választ, a felhasználó rendelkezik hitelesítése után nézhet ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` , amely az alkalmazás kéri. Használhatja a `id_token` ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. |
| state |A kérelem is a biztonságijogkivonat-válaszban visszaadott szerepel érték. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások megelőzése](http://tools.ietf.org/html/rfc6749#section-10.12).  Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a is használatos. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , az alkalmazás megfelelően tudja ezeket kezelni:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Engedélyezési végpont hibái hibakódok
A következő táblázat ismerteti a különböző hibakódok a visszaadható a `error` paramétere a hibaüzenetet.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például a hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. A fejlesztési hiba, és a kezdeti tesztelés során általában kiszűri. |
| unauthorized_client |Az ügyfélalkalmazás az engedélyezési kód kérése nem engedélyezett. |Ez általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| ACCESS_DENIED |Erőforrás tulajdonosa hozzájárulási megtagadva |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem folytatható, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kérelem a válasz típusa. |Javítsa ki, és küldje el újra a kérelmet. A fejlesztési hiba, és a kezdeti tesztelés során általában kiszűri. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezeket a hibákat okozhat az átmeneti állapotot. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes hiba miatt késik a válaszában. |
| temporarily_unavailable |A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában. |
| invalid_resource |A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |

## <a name="validate-the-idtoken"></a>A id_token ellenőrzése
Csak fogadó egy `id_token` túl kicsi a felhasználó; kell az aláírás érvényesítése és a jogcímek, az ellenőrzése a `id_token` / az alkalmazás követelményeinek. Az Azure AD-végpont JSON Web Tokens (JWTs) és a nyilvános kulcsú hitelesítésen használja a jogkivonatok aláírásához, és győződjön meg arról, hogy azok érvényesek.

Ha szeretné ellenőrizni a `id_token` ügyfél kódot, de általános gyakorlat, hogy küldjön a `id_token` háttérkiszolgálóhoz, és végezze el az érvényesítési hiba. Amennyiben Ön aláírását, valamint ellenőrizte a `id_token`, van néhány jogcímeket kell ellenőrizni.

Először is célszerű további ellenőrizhesse a forgatókönyvtől függően. Néhány gyakori érvényesítést a következők:

* A felhasználó/szervezeti biztosítása rendelkezik regisztrált az alkalmazást.
* Biztosítása a felhasználó rendelkezik a megfelelő engedélyezési vagy jogosultságokkal
* Bizonyos erősségével hitelesítés úgy történt, például a többtényezős hitelesítést.

Ellenőrzése után a `id_token`, a felhasználói munkamenet elindításához, és a jogcímek használata a `id_token` megszerezni az alkalmazásban a felhasználó adatait. Ez az információ alkalmas megjelenített, rekordok, engedélyek, stb. A jogkivonat-típusok és a jogcímek kapcsolatos további információkért olvassa el a [támogatott jogkivonat és jogcímtípusok](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Szeretné beléptetni a felhasználót az alkalmazásból, esetén nem elegendő az alkalmazás cookie-k vagy más célból törölni kívánja a felhasználói munkamenet.  Is kell átirányítja a felhasználót, hogy a `end_session_endpoint` a kijelentkezési.  Ha nem sikerül, a felhasználó fog tudni újból hitelesítésre az alkalmazáshoz a hitelesítő adatok újbóli megadása nélkül mert érvényes egyszeri bejelentkezés munkamenetet az Azure AD-végponthoz rendelkeznek.

Egyszerűen irányíthatja át a felhasználót, hogy a `end_session_endpoint` az OpenID Connect metaadat-dokumentum szerepel:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paraméter |  | Leírás |
| --- | --- | --- |
| post_logout_redirect_uri |Ajánlott |Az URL-címet, a felhasználó sikeres kijelentkezési után a rendszer átirányítja.  Ha nem tartalmazza, a felhasználó egy általános üzenet jelenik meg. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés
Ha átirányítja a felhasználót, hogy a `end_session_endpoint`, az Azure AD törli a felhasználói munkamenetet a böngészőből. Azonban a felhasználó esetleg még be van jelentkezve más alkalmazásokhoz az Azure AD használatára a hitelesítéshez. Ahhoz, hogy ezek az alkalmazások a felhasználó egyidejűleg jelentkezzen ki, az Azure AD egy HTTP GET kérést küld a regisztrált `LogoutUrl` , a felhasználó van éppen bejelentkezve az alkalmazásokat. Alkalmazások bármely, amely azonosítja a felhasználói munkamenet és vissza ehhez a kérelemhez kell válaszolnia a `200` válasz.  Ha támogatja az egyszeri bejelentkezési ki az alkalmazásban, meg kell-e valósítania például egy `LogoutUrl` az alkalmazás kódjában.  Beállíthatja a `LogoutUrl` az Azure-portálon:

1. Keresse meg a [Azure-portálon](https://portal.azure.com).
2. Válassza ki az Active Directory kattintva a lap jobb felső sarkában a fiókba.
3. A bal oldali navigációs panelen válassza ki a **Azure Active Directory**, majd válassza **App regisztrációk** válassza ki az alkalmazást.
4. Kattintson a **tulajdonságok** keresse meg a **kijelentkezési URL-cím** szövegmezőben. 

## <a name="token-acquisition"></a>Token beszerzése
Sok webalkalmazások nem csak beléptetni a felhasználót, de emellett egy webszolgáltatás-bővítmény OAuth protokollt használó felhasználó nevében kell. Ebben a forgatókönyvben egyesíti az OpenID Connect felhasználói hitelesítés során egyidejűleg az beszerzése egy `authorization_code` , amely a beolvasandó használható `access_tokens` az OAuth hitelesítési kód Flow használatával.

## <a name="get-access-tokens"></a>A hozzáférési jogkivonatok lekérésére
Jogkivonatot szerezni, módosítania kell a bejelentkezési kérelem a fent:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Többek között a engedélyhatókörök a kérelem és használatával `response_type=code+id_token`, a `authorize` végpont biztosítja, hogy a felhasználó hozzájárult szerepelnek az engedélyeket a `scope` lekérdezésparaméter, és térjen vissza az alkalmazás egy engedélyezési kód a helyszíni Exchange olyan hozzáférési jogkivonatot.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=form_post` láthatóhoz hasonló:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` , amely az alkalmazás kéri. Használhatja a `id_token` ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. |
| Kód |A authorization_code, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot célerőforrás igényelhetnek. Authorization_codes rövid élt, és általában körülbelül 10 perc múlva lejár. |
| state |Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , az alkalmazás megfelelően tudja ezeket kezelni:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

A lehetséges hibakódokat és a javasolt művelet leírását, [engedélyezési végpont hibái hibakódok](#error-codes-for-authorization-endpoint-errors).

Miután egy engedélyezési igazoló `code` és egy `id_token`, a felhasználói bejelentkezés és a hozzáférési jogkivonatok lekérésére, ha a nevében.  A felhasználó bejelentkezni ellenőrizni kell a `id_token` pontosan a fent leírt módon. Ahhoz, hogy a hozzáférési jogkivonatok, hajtsa végre a "Az engedélyezési kód használata olyan hozzáférési jogkivonatot kérni" szakaszában leírt lépéseket a [OAuth-protokoll dokumentációját](active-directory-protocols-oauth-code.md).
