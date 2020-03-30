---
title: Webes bejelentkezés az OpenID Connecttel – Azure Active Directory B2C
description: Webalkalmazásokat hozhat létre az Azure Active Directory B2C OpenID Connect hitelesítési protokolljával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264387"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webes bejelentkezés az OpenID Connect szolgáltatással az Azure Active Directory B2C-ben

Az OpenID Connect egy hitelesítési protokoll, amely az OAuth 2.0-s rendszerre épül, és amely a felhasználók biztonságos bejelentkezésére használható a webalkalmazásokba. Az OpenID Connect Azure Active Directory B2C (Azure AD B2C) implementációjával kiszervezheti a regisztrációt, a bejelentkezést és a webalkalmazásokegyéb identitáskezelési élményeit az Azure Active Directoryba (Azure AD). Ez az útmutató bemutatja, hogyan kell ezt nyelvtől függetlenül elvégezni. Leírja, hogyan küldhet és fogadhat HTTP-üzeneteket a nyílt forráskódú tárak használata nélkül.

[Az OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kiterjeszti az OAuth 2.0 *engedélyezési* protokollt hitelesítési protokollként való *használatra.* Ez a hitelesítési protokoll lehetővé teszi az egyszeri bejelentkezést. Bevezeti az azonosító *jogkivonat*fogalmát, amely lehetővé teszi az ügyfél számára a felhasználó identitásának ellenőrzését és a felhasználó alapvető profiladatainak beszerzését.

Mivel kiterjeszti az OAuth 2.0-s t, lehetővé teszi, hogy az alkalmazások biztonságosan *szerezzenek hozzáférési jogkivonatokat.* A hozzáférési jogkivonatokkal hozzáférhet az engedélyezési [kiszolgáló](protocols-overview.md)által védett erőforrásokhoz. Az OpenID Connect akkor ajánlott, ha olyan webalkalmazást hoz fel, amely et egy kiszolgálón üzemelteti, és amelyet böngészőn keresztül lehet elérni. A jogkivonatokról az [Azure Active Directory B2C tokenek áttekintése című témakörben olvashat bővebben.](tokens-overview.md)

Az Azure AD B2C kiterjeszti a szabványos OpenID Connect protokollt, hogy ne csak egyszerű hitelesítést és engedélyezést tegyen. Bemutatja a [felhasználói folyamat paramétert](user-flow-overview.md), amely lehetővé teszi az OpenID Connect használatát a felhasználói élmény hozzáadásához az alkalmazáshoz, például a regisztrációhoz, a bejelentkezéshez és a profilkezeléshez.

## <a name="send-authentication-requests"></a>Hitelesítési kérelmek küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, és `/authorize` futtatnia kell egy felhasználói folyamatot, a felhasználót a végponthoz irányíthatja. A felhasználó a felhasználói folyamattól függően műveletet tesz.

Ebben a kérésben az ügyfél jelzi az engedélyeket, amelyeket meg kell szereznie a felhasználótól a `scope` paraméterben, és megadja a futtatandó felhasználói folyamatot. A kérelem működésének megismeréséhez próbálja meg beilleszteni a kérelmet egy böngészőbe, és futtatni. Cserélje `{tenant}` le a bérlő nevét. Cserélje `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` le a bérlőben korábban regisztrált alkalmazás azonosítóját. Módosítsa a házirend`{policy}`nevét ( ) a bérlőben lévő házirendnévre is, például `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| {bérlő} | Igen | Az Azure AD B2C-bérlő neve |
| {házirend} | Igen | A futtatandó felhasználói folyamat. Adja meg az Azure AD B2C-bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`vagy . |
| client_id | Igen | Az alkalmazásazonosító, amelyaz [Azure Portalon](https://portal.azure.com/) rendelt az alkalmazáshoz. |
| nonce között | Igen | A kérelemben szereplő (az alkalmazás által létrehozott) érték, amely jogcímként szerepel az eredményül kapott azonosító jogkivonatban. Az alkalmazás ezután ellenőrizheti ezt az értéket a token replay támadások csökkentése érdekében. Az érték általában egy randomizált egyedi karakterlánc, amely a kérelem eredetének azonosítására használható. |
| response_type | Igen | Az OpenID Connect azonosító jogkivonatát is tartalmaznia kell. Ha a webalkalmazásnak is szüksége van jogkivonatokra `code+id_token`egy webes API hívásához, használhatja a használatát. |
| scope | Igen | A hatókörök térben elválasztott listája. A `openid` hatókör azt jelzi, hogy a felhasználó jelentkezzen be, és a felhasználó adatait azonosító jogkivonatok formájában kapja meg. A `offline_access` hatókör nem kötelező a webes alkalmazások számára. Azt jelzi, hogy az alkalmazásnak szüksége lesz egy *frissítési jogkivonatra* az erőforrások hoz való kiterjesztett hozzáféréshez. |
| Gyors | Nem | A szükséges felhasználói beavatkozás típusa. Az egyetlen érvényes érték `login`jelenleg a , amely arra kényszeríti a felhasználót, hogy adja meg a hitelesítő adatait a kéréshez. |
| redirect_uri | Nem | Az `redirect_uri` alkalmazás paramétere, ahol az alkalmazás hitelesítési válaszokat küldhet és fogad. Pontosan meg kell `redirect_uri` egyeznie az Azure Portalon regisztrált paraméterek egyikével, azzal a különbséggel, hogy URL-cím kódolásúnak kell lennie. |
| response_mode | Nem | Az eredményül kapott engedélyezési kód nak az alkalmazásnak való visszaküldéséhez használt módszer. Ez lehet `query`a `form_post`, `fragment`vagy a .  A `form_post` legjobb biztonság érdekében a válaszmód ajánlott. |
| state | Nem | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszaadja. Bármilyen tartalom karakterlánca lehet. A véletlenszerűen generált egyedi értéket általában a helyek közötti kérelmek hamisítási támadásainak megelőzésére használják. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt, amelyen voltak. |

Ezen a ponton a felhasználónak el kell végeznie a munkafolyamatot. Előfordulhat, hogy a felhasználónak meg kell adnia a felhasználónevét és jelszavát, be kell jelentkeznie egy közösségi identitással, vagy regisztrálnia kell a címtárra. A felhasználói folyamat definiálásától függően bármilyen más lépések száma is lehet.

Miután a felhasználó befejezte a felhasználói folyamatot, a `redirect_uri` válasz a megadott paraméteren, a paraméterben megadott módszerrel válasz érkezik az `response_mode` alkalmazáshoz. A válasz ugyanaz az egyes előző esetekben, függetlenül a felhasználói folyamat.

A sikeres `response_mode=fragment` válasz használata a következőkre vallana:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| id_token | Az alkalmazás által kért azonosító jogkivonat. Az azonosító jogkivonat segítségével ellenőrizheti a felhasználó identitását, és munkamenetet kezdhet a felhasználóval. |
| code | Az alkalmazás által kért engedélyezési kód, ha a használt . `response_type=code+id_token` Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet egy célerőforráshoz. Az engedélyezési kódok általában körülbelül 10 perc elteltével járnak le. |
| state | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

A hibaválaszok is elküldhetők a `redirect_uri` paraméterre, így az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A előforduló hibatípusok osztályozására használható kód. |
| error_description | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| state | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

## <a name="validate-the-id-token"></a>Az azonosítótoken ellenőrzése

Csak egy azonosító jogkivonat fogadása nem elegendő a felhasználó hitelesítéséhez. Ellenőrizze az azonosító jogkivonat aláírását, és ellenőrizze a jogcímeket a jogkivonatban az alkalmazás követelményeinek megfelelően. Az Azure AD B2C [JSON webtokeneket (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú kriptográfia segítségével írja alá a jogkivonatokat, és ellenőrizze, hogy azok érvényesek-e. Számos nyílt forráskódú kódtár áll rendelkezésre a JWT-k érvényesítéséhez, a ttól függően, hogy milyen nyelvet részesítsen el. Azt javasoljuk, hogy vizsgálja meg ezeket a lehetőségeket, nem pedig a saját érvényesítési logika megvalósítása.

Az Azure AD B2C rendelkezik egy OpenID Connect metaadat-végpont, amely lehetővé teszi, hogy egy alkalmazás információkat kapjon az Azure AD B2C futásidőben. Ez az információ végpontokat, jogkivonat-tartalmat és jogkivonat-aláíró kulcsokat tartalmaz. A B2C-bérlő minden egyes felhasználói folyamatához van egy JSON-metaadat-dokumentum. A felhasználói folyamat metaadat-dokumentuma `fabrikamb2c.onmicrosoft.com` például a `b2c_1_sign_in` következő helyen található:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

A konfigurációs dokumentum egyik `jwks_uri`tulajdonsága a , amelynek értéke ugyanahhoz a felhasználói folyamathoz a következő:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Annak meghatározásához, hogy melyik felhasználói folyamatot használta egy azonosító jogkivonat aláírása (és honnan kell beszerezni a metaadatokat), két lehetősége van. Először is, a felhasználói `acr` folyamat neve szerepel a jogcím az azonosító jogkivonatban. A másik lehetőség az, hogy a felhasználói `state` folyamatot a paraméter értékében kódolja a kérelem kiadásakor, majd dekódolja azt, hogy melyik felhasználói folyamatot használta. Mindkét módszer érvényes.

Miután beszerezte a metaadat-dokumentumot az OpenID Connect metaadat-végpontról, az RSA 256 nyilvános kulcsokkal ellenőrizheti az azonosítójogkivonat aláírását. Lehet, hogy több kulcs szerepel ezen a végponton, mindegyik egy `kid` jogcím által azonosított. Az azonosító jogkivonat fejléce `kid` is tartalmaz egy jogcímet, amely jelzi, hogy ezek közül a kulcsok közül melyiket használták az azonosító jogkivonat aláírásához.

Az Azure AD B2C jogkivonatjainak ellenőrzéséhez létre kell hoznia a nyilvános kulcsot a kitevő(e) és a modulus(n) használatával. Meg kell határoznia, hogyan kell ezt a megfelelő programozási nyelven kell. A nyilvános kulcs létrehozásáról és az RSA protokollal kapcsolatos hivatalos dokumentáció itt található:https://tools.ietf.org/html/rfc3447#section-3.1

Miután ellenőrizte az azonosító jogkivonat aláírását, számos jogcím, amelyet ellenőriznie kell. Ilyenek például a következők:

- Ellenőrizze `nonce` a jogcímet a token-visszajátszásos támadások megelőzése érdekében. Az érték nek meg kell adnia a bejelentkezési kérelemben megadott értéket.
- Ellenőrizze `aud` a jogcímet, hogy megbizonyosodjon arról, hogy az azonosító jogkivonat ot kiadták az alkalmazáshoz. Az alkalmazás azonosítójának az alkalmazás azonosítójának kell lennie.
- Ellenőrizze `iat` a `exp` és a jogcímeket, hogy megbizonyosodjon arról, hogy az azonosító jogkivonat nem járt le.

Számos további ellenőrzést is végre kell hajtania. Az érvényesítéseket részletesen ismerteti az [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Előfordulhat, hogy további jogcímeket is szeretne érvényesíteni, a forgatókönyvtől függően. Néhány gyakori érvényesítés:

- Annak biztosítása, hogy a felhasználó/szervezet feliratkozott az alkalmazásra.
- Annak biztosítása, hogy a felhasználó megfelelő jogosultságokkal/jogosultságokkal rendelkezzen.
- Győződjön meg arról, hogy a hitelesítés bizonyos erőssége megtörtént, például az Azure többtényezős hitelesítés.

Az azonosító jogkivonat ellenőrzése után megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatban lévő jogcímek segítségével információkat kaphat a felhasználóról az alkalmazásban. Az adatok felhasználási céljai közé tartozik a megjelenítés, a rekordok és az engedélyezés.

## <a name="get-a-token"></a>Token beszereznie

Ha szüksége van a webalkalmazás csak a felhasználói folyamatok futtatásához, kihagyhatja a következő néhány szakaszt. Ezek a szakaszok csak olyan webes alkalmazásokra vonatkoznak, amelyeknek hitelesített hívásokat kell kezdeményezniük egy webes API-hoz, és az Azure AD B2C is védelmealatt állnak.

A beszerzett engedélyezési kódot (használatával) `response_type=code+id_token`a kívánt erőforrásnak egy `POST` kérés elküldésével `/token` válthatja be a kívánt erőforráshoz. Az Azure AD B2C-ben a szokásos módon [kérhet hozzáférési jogkivonatokat más API-khoz,](access-tokens.md#request-a-token) ha megadja azok hatókörét(i)t a kérelemben.

Az alkalmazás saját háttérwebes API-jához hozzáférési jogkivonatot is kérhet, ha az alkalmazás ügyfélazonosítóját használja a kért hatókörként (ami egy hozzáférési jogkivonatot eredményez, amelyben az ügyfélazonosító "közönség"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| {bérlő} | Igen | Az Azure AD B2C-bérlő neve |
| {házirend} | Igen | Az engedélyezési kód beszerzéséhez használt felhasználói folyamat. Ebben a kérésben nem használhat másik felhasználói folyamatot. Ezt a paramétert adja hozzá a lekérdezési karakterlánchoz, ne a POST törzshöz. |
| client_id | Igen | Az alkalmazásazonosító, amelyaz [Azure Portalon](https://portal.azure.com/) rendelt az alkalmazáshoz. |
| client_secret | Igen, a Web Apps alkalmazásban | Az [Azure Portalon](https://portal.azure.com/)létrehozott alkalmazástitok. Az ügyféltitkos kulcsok ebben a folyamatban webalkalmazás-forgatókönyvekben használatosak, ahol az ügyfél biztonságosan tárolhat egy ügyféltitkot. Natív alkalmazás (nyilvános ügyfél) forgatókönyvek esetén az ügyféltitkos kulcsok nem tárolhatók biztonságosan, a threfore nem használható ebben a folyamatban. Ha ügyféltitkot használ, kérjük, rendszeresen módosítsa azt. |
| code | Igen | A felhasználói folyamat elején beszerzett engedélyezési kód. |
| grant_type | Igen | A támogatás típusa, amelynek az engedélyezési kód folyamatához kell tartaszta. `authorization_code` |
| redirect_uri | Igen | Annak `redirect_uri` az alkalmazásnak a paramétere, amelyhez az engedélyezési kódot kapta. |
| scope | Nem | A hatókörök térben elválasztott listája. A `openid` hatókör azt jelzi, hogy a felhasználó bejelentkezik, és adatokat kap a felhasználóról id_token paraméterek formájában. Segítségével jogkivonatokat kaphat az alkalmazás saját háttérwebes API-hoz, amelyet az ügyfél azonos alkalmazásazonosítója képvisel. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak szüksége van egy frissítési jogkivonatra az erőforrások hoz való kiterjesztett hozzáféréshez. |

A sikeres jogkivonat-válasz így néz ki:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| not_before | A token érvényességének időpontja, időszerint. |
| token_type | A token típusának értéke. `Bearer`az egyetlen támogatott típus. |
| access_token | A kért aláírt JWT-token. |
| scope | Azok a hatókörök, amelyekre a jogkivonat érvényes. |
| expires_in | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| refresh_token | Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további jogkivonatok beszerzéséhez az aktuális jogkivonat lejárta után. A frissítési jogkivonatok az erőforrásokhoz való hozzáférés hosszabb ideig történő megőrzésére használhatók. A `offline_access` hatókört az engedélyezési és a jogkivonat-kérelmekben is használni kell a frissítési jogkivonat fogadásához. |

A hibaválaszok a következőkre néznek:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A felmerülő hibatípusok osztályozására használható kód. |
| error_description | Egy üzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="use-the-token"></a>A token használata

Most, hogy sikeresen beszerzett egy hozzáférési jogkivonatot, használhatja a jogkivonatot a háttérwebes API-knak a háttérwebes API-kra irányuló kérelmekben, ha a `Authorization` fejlécbe is belehelyezi:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>A token frissítése

Az azonosító jogkivonatok rövid időn belül lejárnak. Frissítse a jogkivonatokat azok lejárta után, hogy továbbra is képes hozzáférni az erőforrásokhoz. Egy jogkivonatot frissíthet `POST` egy másik `/token` kérés elküldésével a végpontra. Ezúttal adja `refresh_token` meg a `code` paramétert a paraméter helyett:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| {bérlő} | Igen | Az Azure AD B2C-bérlő neve |
| {házirend} | Igen | Az eredeti frissítési jogkivonat beszerzéséhez használt felhasználói folyamat. Ebben a kérésben nem használhat másik felhasználói folyamatot. Ezt a paramétert adja hozzá a lekérdezési karakterlánchoz, ne a POST törzshöz. |
| client_id | Igen | Az alkalmazásazonosító, amelyaz [Azure Portalon](https://portal.azure.com/) rendelt az alkalmazáshoz. |
| client_secret | Igen, a Web Apps alkalmazásban | Az [Azure Portalon](https://portal.azure.com/)létrehozott alkalmazástitok. Az ügyféltitkos kulcsok ebben a folyamatban webalkalmazás-forgatókönyvekben használatosak, ahol az ügyfél biztonságosan tárolhat egy ügyféltitkot. Natív alkalmazás (nyilvános ügyfél) forgatókönyvek esetén az ügyféltitkos kulcsok nem tárolhatók biztonságosan, a threfore nem használható ebben a hívásban. Ha ügyféltitkot használ, kérjük, rendszeresen módosítsa azt. |
| grant_type | Igen | A támogatás típusa, amelynek az engedélyezési kódfolyamat ezen részéhez frissítési jogkivonatnak kell lennie. |
| refresh_token | Igen | A folyamat második részében beszerzett eredeti frissítési jogkivonat. A `offline_access` hatókört az engedélyezési és a jogkivonat-kérelmekben is használni kell a frissítési jogkivonat fogadásához. |
| redirect_uri | Nem | Annak `redirect_uri` az alkalmazásnak a paramétere, amelyhez az engedélyezési kódot kapta. |
| scope | Nem | A hatókörök térben elválasztott listája. A `openid` hatókör azt jelzi, hogy a felhasználó jelentkezzen be, és a felhasználó adatait azonosító jogkivonatok formájában kapja meg. Segítségével jogkivonatokat küldhet az alkalmazás saját háttérwebes API-jára, amelyet az ügyfélazonos alkalmazásazonosító képvisel. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak szüksége van egy frissítési jogkivonatra az erőforrások hoz való kiterjesztett hozzáféréshez. |

A sikeres jogkivonat-válasz így néz ki:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| not_before | A token érvényességének időpontja, időszerint. |
| token_type | A token típusának értéke. `Bearer`az egyetlen támogatott típus. |
| access_token | A kért aláírt JWT-token. |
| scope | Az a hatókör, amelyre a jogkivonat érvényes. |
| expires_in | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| refresh_token | Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további jogkivonatok beszerzéséhez az aktuális jogkivonat lejárta után. A frissítési jogkivonatok az erőforrásokhoz való hozzáférés hosszabb ideig történő megőrzésére használhatók. |

A hibaválaszok a következőkre néznek:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A felmerülő hibatípusok osztályozására használható kód. |
| error_description | Egy üzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha ki szeretné jelentkezni a felhasználót az alkalmazásból, nem elegendő az alkalmazás cookie-jainak törléséhez vagy a munkamenet más módon történő befejezéséhez. Irányítsa át a felhasználót az Azure AD B2C kijelentkezéshez. Ha ezt nem teszi meg, előfordulhat, hogy a felhasználó újra hitelesítheti magát az alkalmazáshoz anélkül, hogy újra meg kellene adnia a hitelesítő adatait.

A felhasználó kijelentkeztetéséhez irányítsa át a felhasználót arra a `end_session` végpontra, amely a korábban ismertetett OpenID Connect metaadat-dokumentumban szerepel:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| {bérlő} | Igen | Az Azure AD B2C-bérlő neve |
| {házirend} | Igen | A felhasználó által az alkalmazásból való kijelentkeztetéséhez használni kívánt felhasználói folyamat. |
| id_token_hint| Nem | Egy korábban kiadott azonosító jogkivonat, amely átkerül a kijelentkezési végpontra, a végfelhasználó aktuális hitelesített munkamenetével kapcsolatos tippként az ügyféllel. A `id_token_hint` biztosítja, `post_logout_redirect_uri` hogy a regisztrált válasz URL-címet az Azure AD B2C alkalmazás beállításait. |
| client_id | Nem* | Az alkalmazásazonosító, amelyaz [Azure Portalon](https://portal.azure.com/) rendelt az alkalmazáshoz.<br><br>\**Erre akkor van `Application` szükség, ha elkülönítési egyszeri bejelentkezés konfigurációját `No`használja, és a kijelentkezési kérelemben az azonosító token _megkövetelése_ értéke a.* |
| post_logout_redirect_uri | Nem | Az URL-címet, amelya felhasználót a sikeres kijelentkezés után át kell irányítani. Ha nem tartalmazza, az Azure AD B2C egy általános üzenetet jelenít meg a felhasználónak. Ha nem `id_token_hint`ad meg egy, nem kell regisztrálni ezt az URL-t válasz URL-címként az Azure AD B2C alkalmazás beállításaiban. |
| state | Nem | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

### <a name="secure-your-logout-redirect"></a>A kijelentkezési átirányítás biztosítása

A kijelentkezés után a felhasználó átlesz irányítva `post_logout_redirect_uri` a paraméterben megadott URI-ra, függetlenül az alkalmazáshoz megadott válasz URL-címektől. Ha azonban egy `id_token_hint` érvényes átvan ad, az Azure AD B2C ellenőrzi, hogy az alkalmazás egyik konfigurált átirányítási URI-k értéke az átirányítás végrehajtása előtt `post_logout_redirect_uri` megegyezik-e. Ha nem lett beállítva egyező válasz URL-cím az alkalmazáshoz, hibaüzenet jelenik meg, és a felhasználó nem lesz átirányítva.

### <a name="external-identity-provider-sign-out"></a>Külső identitásszolgáltató kijelentkezése

A felhasználó irányítása a `end_session` végponttörli a felhasználó egyszeri bejelentkezési állapotának egy részét az Azure AD B2C-vel, de nem írja alá a felhasználót a közösségi identitásszolgáltató (IDP) munkamenetből. Ha a felhasználó ugyanazt az IDP-t választja ki egy későbbi bejelentkezés során, a hitelesítő adatok megadása nélkül újra hitelesíti magát. Ha egy felhasználó ki akar jelentkezni az alkalmazásból, az nem feltétlenül jelenti azt, hogy ki akar jelentkezni a Facebook-fiókjából. Ha azonban helyi fiókokat használ, a felhasználó munkamenete megfelelően befejeződik.
