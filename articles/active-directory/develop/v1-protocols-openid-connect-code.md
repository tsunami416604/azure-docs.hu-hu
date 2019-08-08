---
title: Az OpenID Connect hitelesítési kód folyamatának megismerése az Azure AD-ben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a bérlő webalkalmazásokhoz és webes API-khoz való hozzáférésének engedélyezéséhez Azure Active Directory és OpenID Connect használatával.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a82571260f5da679202e96f5e6f72aa2db6788a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834688"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Hozzáférés engedélyezése webalkalmazásokhoz OpenID Connect és Azure Active Directory használatával

Az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) egy egyszerű, az OAuth 2,0 protokollra épülő identitási réteg. A OAuth 2,0 a védett erőforrások eléréséhez szükséges [**hozzáférési**](access-tokens.md) jogkivonatok beszerzésére és használatára vonatkozó mechanizmusokat határoz meg, de nem határoz meg szabványos metódusokat az azonosító adatok biztosításához. Az OpenID Connect a OAuth 2,0 engedélyezési folyamatának kiterjesztéseként valósítja meg a hitelesítést. Információt nyújt a végfelhasználóról [`id_token`](id-tokens.md) , amely igazolja a felhasználó identitását, és alapszintű profilt biztosít a felhasználóról.

Az OpenID Connect ajánljuk, ha olyan webalkalmazást hoz létre, amely egy kiszolgálón fut, és böngészőn keresztül érhető el.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat OpenID Connect használatával

A legtöbb alapszintű bejelentkezési folyamat a következő lépéseket tartalmazza – mindegyiket alább részletesen ismertetjük.

![OpenId Connect hitelesítési folyamat](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Az OpenID Connect metaadat-dokumentuma

Az OpenID Connect leírja azt a metaadat-dokumentumot, amely az alkalmazásnak a bejelentkezés elvégzéséhez szükséges információkat tartalmazza. Ide tartozik például a használni kívánt URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. Az OpenID Connect metaadat-dokumentum a következő címen érhető el:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
A metaadatok egy egyszerű JavaScript Object Notation (JSON) dokumentum. Példaként tekintse meg a következő kódrészletet. A kódrészlet tartalma teljes mértékben le van írva az [OpenID Connect specifikációban](https://openid.net). Vegye figyelembe, hogy a fenti {bérlő `common` } helyett a bérlői azonosító megadásával a rendszer a visszaadott JSON-objektumhoz tartozó bérlői URI azonosítókat eredményezi.

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

Ha az alkalmazás a [jogcím-leképezési](active-directory-claims-mapping.md) funkció használata miatt egyéni aláíró kulcsokkal rendelkezik, akkor az alkalmazás azonosítóját `appid` tartalmazó lekérdezési paramétert hozzá kell fűzni ahhoz, hogy `jwks_uri` az alkalmazáshoz tartozó aláíró kulcs adataira mutasson. Például a a `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` következőt `jwks_uri` tartalmazza `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`:.

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, a felhasználónak a `/authorize` végpontra kell irányítani. Ez a kérelem hasonló a [OAuth 2,0 engedélyezési kód folyamatának](v1-protocols-oauth-code.md)első szakaszához, néhány fontos különbséggel:

* A kérelemnek tartalmaznia kell a `openid` hatókört `scope` a paraméterben.
* A `response_type` paraméternek tartalmaznia `id_token`kell a következőket:.
* A kérelemnek tartalmaznia kell `nonce` a paramétert.

Így a minta kérés így fog kinézni:

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
| tenant |szükséges |A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek a bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `common` vagy `contoso.onmicrosoft.com` a bérlői független tokenek esetében. |
| client_id |szükséges |Az alkalmazáshoz hozzárendelt, az Azure AD-vel regisztrált alkalmazás azonosítója. Ezt a Azure Portalban találja. Kattintson a **Azure Active Directory**, majd az alkalmazás **regisztrálása**elemre, válassza ki az alkalmazást, és keresse meg az alkalmazás azonosítóját az alkalmazás lapon. |
| response_type |szükséges |Tartalmaznia `id_token` kell az OpenID Connect bejelentkezést. Más response_types is tartalmazhatnak, például `code` : vagy. `token` |
| scope | ajánlott | Az OpenID Connect specifikációja megköveteli a `openid`hatókört, amely a beleegyezési felhasználói felületen a "Bejelentkezés" engedéllyel van lefordítva. Ezt és a többi OIDC-hatókört a rendszer figyelmen kívül hagyja a 1.0-s végponton, de továbbra is ajánlott eljárás a szabványoknak megfelelő ügyfelek számára. |
| nonce |szükséges |A kérelemben szereplő, az alkalmazás által generált érték, amely az eredményül kapott `id_token` jogcímként szerepel. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc vagy GUID, amely a kérelem forrásának azonosítására szolgál. |
| redirect_uri | ajánlott |Az alkalmazás redirect_uri, ahol a hitelesítési válaszokat el lehet juttatni és fogadni tudja az alkalmazás. Pontosan egyeznie kell a portálon regisztrált redirect_uris, kivéve, ha az URL-címet kódolni kell. Ha hiányzik, a rendszer visszaküldi a felhasználói ügynököt az alkalmazáshoz regisztrált átirányítási URI-k egyikére, véletlenszerűen. A maximális hossz 255 bájt |
| response_mode |választható |Meghatározza azt a módszert, amelyet az eredményül kapott authorization_code az alkalmazásba való visszaküldéséhez kell használni. A támogatott értékek `form_post` a *http-űrlapok utáni* és `fragment` az *URL-töredékek*esetében használhatók. A webalkalmazások esetében javasoljuk, `response_mode=form_post` hogy a használatával biztosítsa a tokenek legbiztonságosabb átvitelét az alkalmazásba. Az alapértelmezett érték minden folyamathoz, beleértve az `fragment`id_token is.|
| state |ajánlott |A jogkivonat-válaszban visszaadott kérelemben szereplő érték. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| gyors |választható |Megadja a szükséges felhasználói beavatkozás típusát. Jelenleg az egyetlen érvényes érték a következők egyike: "bejelentkezhessen," None "és" beleegyezett ". `prompt=login`kényszeríti a felhasználót, hogy adja meg a kérelem hitelesítő adatait, és zárja be az egyszeri bejelentkezést. `prompt=none`Ez ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérés nélkül nem jelenik meg. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a végpont hibát ad vissza. `prompt=consent`a felhasználó bejelentkezése után elindítja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| login_hint |választható |A használatával előre kitöltheti a felhasználó bejelentkezési oldalának username/e-mail címe mezőjét, ha már ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután már kibontotta a felhasználónevet egy `preferred_username` korábbi bejelentkezésből a jogcím használatával. |

Ezen a ponton a felhasználónak meg kell adnia a hitelesítő adatait, és el kell végeznie a hitelesítést.

### <a name="sample-response"></a>Mintaválasz

A felhasználó hitelesítése után a bejelentkezési `redirect_uri` kérelemben megadott minta válasz a következőképpen néz ki:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` kért alkalmazás. A `id_token` használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. |
| state |A kérelemben szereplő, a jogkivonat-válaszban visszaadott érték. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |

### <a name="error-response"></a>Hiba válasza

A `redirect_uri` rendszer a hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Az engedélyezési végpont hibáinak kódjai

Az alábbi táblázat azokat a hibakódokat ismerteti, amelyeket a rendszer a hiba `error` válaszának paraméterében adhat vissza.

| Hibakód | Leírás | Ügyfél művelete |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában a kezdeti tesztelés során történik. |
| unauthorized_client |Az ügyfélalkalmazás nem jogosult engedélyezési kód igénylésére. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| access_denied |Erőforrás-tulajdonos által megtagadott jóváhagyás |Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem tud továbblépni, kivéve, ha a felhasználó beleegyezett. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a válasz típusát a kérelemben. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában a kezdeti tesztelés során történik. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálja megismételni a kérelmet. Ezek a hibák ideiglenes feltételekből származhatnak. Előfordulhat, hogy az ügyfélalkalmazás egy ideiglenes hiba miatt késlelteti a felhasználót, hogy a válasza késik. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. |Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késik. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. |Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |

## <a name="validate-the-id_token"></a>A id_token ellenőrzése

A csak az nem elegendő a felhasználó hitelesítéséhez; érvényesíteni kell az aláírást, és ellenőriznie kell a jogcímeket az alkalmazás követelményei `id_token` szerint. `id_token` Az Azure AD-végpont JSON webes tokeneket (JWTs) és nyilvános kulcsú titkosítást használ a jogkivonatok aláírásához, és ellenőrzi, hogy érvényesek-e.

Dönthet úgy is, hogy érvényesíti az `id_token` ügyfél kódját, de az általános gyakorlat az, ha a- `id_token` t egy háttér-kiszolgálónak küldi, és ott hajtja végre az ellenőrzést. 

A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Annak biztosítása, hogy a felhasználó/szervezet regisztrálva legyen az alkalmazásra.
* Annak biztosítása, hogy a felhasználó a vagy `wids` `roles` a jogcímek alapján megfelelő engedélyekkel/jogosultságokkal rendelkezzen. 
* A hitelesítés bizonyos erősségének biztosítása, például a többtényezős hitelesítés.

Miután ellenőrizte a `id_token`alkalmazást, megkezdheti a munkamenetet a felhasználóval, és a alkalmazásban található `id_token` jogcímek használatával szerezheti be a felhasználó adatait az alkalmazásban. Ezek az információk megjeleníthetők, rekordok, személyre szabás stb. esetén használhatók. További információ `id_tokens` a és a jogcímekről: [HRE id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha ki szeretné írni a felhasználót az alkalmazásból, nem elegendő az alkalmazás cookie-jait törölni, vagy más módon a munkamenetet a felhasználóval kizáróan. A felhasználót `end_session_endpoint` a kijelentkezéshez is át kell irányítani. Ha ezt nem teszi meg, a felhasználó a hitelesítő adatok újbóli megadása nélkül újra hitelesítheti az alkalmazást, mert az Azure AD-végponttal érvényes egyszeri bejelentkezéses munkamenet lesz.

Egyszerűen átirányíthatja a felhasználót `end_session_endpoint` az OpenID Connect metaadat-dokumentum listájában szereplő listára:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paraméter |  | Leírás |
| --- | --- | --- |
| post_logout_redirect_uri |ajánlott |Az URL-cím, amelyet a felhasználónak át kell irányítani a sikeres kijelentkezés után. Ha nem szerepel, a felhasználó általános üzenetet jelenít meg. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

Amikor átirányítja a felhasználót a `end_session_endpoint`szolgáltatásba, az Azure ad törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is bejelentkezett az Azure AD-t használó más alkalmazásokba a hitelesítéshez. Annak engedélyezéséhez, hogy az alkalmazások egyidejűleg írják alá a felhasználót, az Azure ad egy HTTP Get kérelmet küld `LogoutUrl` az összes olyan alkalmazás regisztrálásához, amelyhez a felhasználó jelenleg be van jelentkezve. Az alkalmazásoknak válaszolnia kell erre a kérelemre a felhasználót azonosító munkamenetek törlésével `200` és a válasz visszaadásával. Ha szeretné támogatni az egyszeri kijelentkezést az alkalmazásban, akkor az alkalmazás kódjában `LogoutUrl` be kell vezetnie egy ilyen alkalmazást. A `LogoutUrl` Azure Portal a következőt állíthatja be:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. A lap jobb felső sarkában található fiókra kattintva válassza ki a Active Directory.
3. A bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget, majd válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazást.
4. Kattintson a **Beállítások**, majd a **Tulajdonságok** elemre, és keresse meg a kijelentkezési **URL-cím** szövegmezőt. 

## <a name="token-acquisition"></a>Jogkivonat-beszerzés
Számos webalkalmazásnak nem csak a felhasználónak kell aláírnia a-t, hanem az adott felhasználó nevében egy webszolgáltatáshoz is hozzá kell férnie a OAuth használatával. Ez a forgatókönyv ötvözi az OpenID connectet a felhasználói hitelesítéshez, `authorization_code` miközben egy, a [OAuth engedélyezési kód](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) `access_tokens` használatával történő beszerzéséhez használható.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beolvasása
Hozzáférési jogkivonatok beszerzéséhez módosítania kell a fenti bejelentkezési kérést:

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

A kérelemben és a használatban `response_type=code+id_token`lévő jogosultsági hatókörökkel együtt a `authorize` végpont biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben jelzett engedélyekkel, és visszaadja az alkalmazásnak az Exchange-hez tartozó engedélyezési kódot. hozzáférési jogkivonat.

### <a name="successful-response"></a>Sikeres válasz

A szolgáltatásnak `response_mode=form_post`küldött sikeres válasz a `redirect_uri` következőképpen néz ki:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| id_token |A `id_token` kért alkalmazás. A `id_token` használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. |
| code |Az alkalmazás által kért authorization_code. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési jogkivonatot a célként megadott erőforráshoz. A Authorization_codes rövid életűek, és általában körülbelül 10 perc múlva lejárnak. |
| state |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza

A `redirect_uri` rendszer a hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

A lehetséges hibakódok és a javasolt ügyfél-művelet leírását lásd: [hibakódok az engedélyezési végpont hibáihoz](#error-codes-for-authorization-endpoint-errors).

Miután megkapta az engedélyt `code` `id_token`és a-t, aláírhatja a felhasználót a nevében, és elérheti a hozzáférési jogkivonatokat is. [](access-tokens.md) A felhasználó a alkalmazásban való aláírásához a fentiekben leírtak szerint pontosan kell érvényesíteni a `id_token` következőt:. A hozzáférési jogkivonatok beszerzéséhez kövesse az [OAuth Code flow dokumentációjának](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)"az engedélyezési kód használata hozzáférési token kérése" című szakaszának lépéseit.

## <a name="next-steps"></a>További lépések

* További információ a [hozzáférési tokenekről](access-tokens.md).
* További információ a [ `id_token` és](id-tokens.md)a jogcímekről.
