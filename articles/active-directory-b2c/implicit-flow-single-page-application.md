---
title: Egyoldalas bejelentkezés implicit folyamat használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá egyoldalas bejelentkezést az OAuth 2.0 implicit folyamat használatával az Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399000"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Egyoldalas bejelentkezés az OAuth 2.0 implicit folyamathasználatával az Azure Active Directory B2C-ben

Számos modern alkalmazás egyoldalas alkalmazás előtérrel rendelkezik, amely elsősorban JavaScript nyelven íródott. Gyakran előfordul, hogy az alkalmazás olyan keretrendszer használatával íródik, mint a React, az Angular vagy a Vue.js. Az egyoldalas alkalmazások és más, elsősorban a böngészőben futó JavaScript-alkalmazások további kihívást jelentenek a hitelesítés során:

- Ezeknek az alkalmazásoknak a biztonsági jellemzői eltérnek a hagyományos kiszolgálóalapú webalkalmazásoktól.
- Számos engedélyezési kiszolgáló és identitásszolgáltató nem támogatja az eredetbe való beépülő forrásmegosztásra (CORS) vonatkozó kérelmeket.
- A teljes oldalas böngészőátirányítások az alkalmazástól invazívak lehetnek a felhasználói élményre.

Ezek az alkalmazások támogatása érdekében az Azure Active Directory B2C (Azure AD B2C) az OAuth 2.0 implicit folyamatot használja. Az OAuth 2.0 engedélyezési implicit támogatási folyamatát [az OAuth 2.0 specifikáció 4.2 szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. Az implicit folyamat során az alkalmazás közvetlenül az Azure Active Directory (Azure AD) engedélyezett végponttól kap jogkivonatokat, kiszolgálók közötti csere nélkül. Minden hitelesítési logika és a munkamenet-kezelés teljes egészében a JavaScript-ügyfélben történik, vagy egy oldalátirányítással vagy egy előugró dobozzal.

Az Azure AD B2C a szabványos OAuth 2.0 implicit folyamatot nem csak egyszerű hitelesítésre és engedélyezésre terjeszti ki. Az Azure AD B2C bemutatja a [házirend-paramétert.](user-flow-overview.md) A szabályzati paraméterrel az OAuth 2.0 használatával szabályzatokat adhat hozzá az alkalmazáshoz, például a regisztrációt, a bejelentkezést és a profilkezelési felhasználói folyamatokat. A példában http-kérelmek ebben a cikkben, **{tenant}.onmicrosoft.com** példaként használható. Cserélje `{tenant}` le a bérlő nevét, ha rendelkezik ilyennel, és létrehozott egy felhasználói folyamatot is.

Az implicit bejelentkezési folyamat az alábbihoz hasonlóan néz ki. Minden lépést részletesen ismertetünk a cikk későbbi részében.

![Az OpenID Connect implicit folyamatát ábrázoló sávstílusú diagram](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Hitelesítési kérelmek küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, és `/authorize` futtatnia kell egy felhasználói folyamatot, a felhasználót a végponthoz irányíthatja. A felhasználó a felhasználói folyamattól függően műveletet tesz.

Ebben a kérésben az ügyfél jelzi az engedélyeket, amelyeket meg kell szereznie a felhasználótól a `scope` paraméterben és a felhasználói folyamat futtatásához. A kérelem működésének megismeréséhez próbálja meg beilleszteni a kérelmet egy böngészőbe, és futtatni. Cserélje `{tenant}` le az Azure AD B2C-bérlő nevét. Cserélje `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` le a bérlőben korábban regisztrált alkalmazás azonosítóját. Cserélje `{policy}` le például `b2c_1_sign_in`a bérlőben létrehozott házirend nevére.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
|{bérlő}| Igen | Az Azure AD B2C-bérlő neve|
|{házirend}| Igen| A futtatandó felhasználói folyamat. Adja meg az Azure AD B2C-bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`vagy . |
| client_id | Igen | Az alkalmazásazonosító, amelyaz [Azure Portalon](https://portal.azure.com/) rendelt az alkalmazáshoz. |
| response_type | Igen | `id_token` Tartalmaznia kell az OpenID Connect bejelentkezéshez. A válasz típusát `token`is tartalmazhatja. Ha a `token`használata, az alkalmazás azonnal kaphat egy hozzáférési jogkivonatot az engedélyezett végpont, anélkül, hogy egy második kérés t a authorize endpoint.  Ha a `token` választípust `scope` használja, a paraméternek tartalmaznia kell egy hatókört, amely jelzi, hogy melyik erőforrásnak adja ki a jogkivonatot. |
| redirect_uri | Nem | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás hitelesítési válaszokat küldhet és fogadhat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy URL-kódolásúnak kell lennie. |
| response_mode | Nem | Megadja az eredményül kapott jogkivonat visszaküldésének módját az alkalmazásnak.  Implicit folyamatok esetén `fragment`használja a használatát. |
| scope | Igen | A hatókörök térben elválasztott listája. Egyetlen hatókör értéke jelzi az Azure AD mindkét kért engedélyeket. A `openid` hatókör azt jelzi, hogy a felhasználó jelentkezzen be, és a felhasználó adatait azonosító jogkivonatok formájában kapja meg. A `offline_access` hatókör nem kötelező a webalkalmazások esetében. Azt jelzi, hogy az alkalmazásnak szüksége van egy frissítési jogkivonatra az erőforrások hosszú élettartamú eléréséhez. |
| state | Nem | A kérelemben szereplő érték, amely a jogkivonat-válaszban is visszaadja. Bármilyen használni kívánt tartalom karakterlánca lehet. Általában egy véletlenszerűen generált, egyedi értéket használnak, hogy megakadályozzák a helyek közötti kérelem hamisítási támadások. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotára vonatkozó információkat az alkalmazásban, például azt az oldalt, amelyen voltak. |
| nonce között | Igen | A kérelemben szereplő (az alkalmazás által létrehozott) érték, amely jogcímként szerepel az eredményül kapott azonosítójogkivonatban. Az alkalmazás ezután ellenőrizheti ezt az értéket a token-visszajátszási támadások csökkentése érdekében. Az érték általában egy randomizált, egyedi karakterlánc, amely a kérelem eredetének azonosítására használható. |
| Gyors | Nem | A szükséges felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes `login`érték . Ez a paraméter kényszeríti a felhasználót, hogy adja meg a hitelesítő adatait a kérelemhez. Az egyszeri bejelentkezés nem lép érvénybe. |

Ezen a ponton a rendszer felkéri a felhasználót, hogy fejezze be a szabályzat munkafolyamatát. Előfordulhat, hogy a felhasználónak meg kell adnia a felhasználónevét és jelszavát, be kell jelentkeznie egy közösségi identitással, regisztrálnia kell a címtárra, vagy bármilyen más lépést meg kell adnia. A felhasználói műveletek a felhasználói folyamat definiálásától függenek.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD `redirect_uri`a használt értékkel válaszol az alkalmazásra. A `response_mode` paraméterben megadott módszert használja. A válasz pontosan ugyanaz az egyes felhasználói művelet forgatókönyvek, függetlenül a felhasználói folyamat, amely végrehajtotta.

### <a name="successful-response"></a>Sikeres válasz
Sikeres válasz, `response_mode=fragment` amely `response_type=id_token+token` a következőképpen használja és az ahhoz hasonlóan néz ki, az olvashatóság érdekében sortörésekkel:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Paraméter | Leírás |
| --------- | ----------- |
| access_token | Az alkalmazás által kért hozzáférési jogkivonat. |
| token_type | A token típusának értéke. Az Azure AD által támogatott egyetlen típus a bemutatóra. |
| expires_in | A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| scope | Azok a hatókörök, amelyekre a jogkivonat érvényes. A további felhasználás érdekében a hatókörök segítségével is gyorsítótárazhatja a tokeneket. |
| id_token | Az alkalmazás által kért azonosító jogkivonat. Az azonosító jogkivonat segítségével ellenőrizheti a felhasználó identitását, és munkamenetet kezdhet a felhasználóval. Az azonosító jogkivonatokról és azok tartalmáról az [Azure AD B2C token hivatkozása](tokens-overview.md)című témakörben talál további információt. |
| state | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

### <a name="error-response"></a>Hibaválasz
A hibaválaszok is elküldhetők az átirányítási URI-ba, hogy az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A felmerülő hibatípusok osztályozására használt kód. |
| error_description | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| state | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e.|

## <a name="validate-the-id-token"></a>Az azonosítótoken ellenőrzése

Az azonosító jogkivonat fogadása nem elegendő a felhasználó hitelesítéséhez. Ellenőrizze az azonosító jogkivonat aláírását, és ellenőrizze a jogcímeket a jogkivonatban az alkalmazás követelményeinek megfelelően. Az Azure AD B2C [JSON webtokeneket (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú kriptográfia segítségével írja alá a jogkivonatokat, és ellenőrizze, hogy azok érvényesek-e.

Számos nyílt forráskódú kódtár áll rendelkezésre a JWT-k érvényesítéséhez, a ttól függően, hogy milyen nyelvet szeretne használni. Fontolja meg a rendelkezésre álló nyílt forráskódú kódtárak feltárását a saját ellenőrzési logika megvalósítása helyett. A cikkben található információk segítségével megtudhatja, hogyan használhatja megfelelően ezeket a könyvtárakat.

Az Azure AD B2C rendelkezik egy OpenID Connect metaadat-végpont. Egy alkalmazás használhatja a végpontot az Azure AD B2C futásidejű információinak lekéréséhez. Ez az információ végpontokat, jogkivonat-tartalmat és jogkivonat-aláíró kulcsokat tartalmaz. Az Azure AD B2C-bérlőminden egyes felhasználói folyamathoz van egy JSON-metaadat-dokumentum. Például a b2c_1_sign_in felhasználói folyamat metaadat-dokumentuma a fabrikamb2c.onmicrosoft.com bérlőben a következő helyen található:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

A konfigurációs dokumentum egyik tulajdonsága a. `jwks_uri` Ugyanannak a felhasználói folyamatnak az értéke a következő:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Annak meghatározásához, hogy melyik felhasználói folyamatot használták egy azonosító jogkivonat aláírásához (és honnan kell beolvasni a metaadatokat), két lehetősége van. Először is, a felhasználói `acr` folyamat `id_token`neve szerepel a jogcím ben. Az azonosító jogkivonat jogcímének elemzéséről az Azure [AD B2C token hivatkozása](tokens-overview.md)című témakörben talál további információt. A másik lehetőség az, hogy a felhasználói `state` folyamatot a paraméter értékében kódolja a kérelem kiadásakor. Ezután dekódolja a `state` paramétert, hogy meghatározza, melyik felhasználói folyamatot használta. Mindkét módszer érvényes.

Miután beszerezte a metaadat-dokumentumot az OpenID Connect metaadat-végpontról, az RSA-256 nyilvános kulcsok (ezen végponton található) segítségével ellenőrizheti az azonosítójogkivonat aláírását. Előfordulhat, hogy egy adott időpontban több kulcs is szerepel `kid`a végponton, amelyeket egy . A fejléc `id_token` is `kid` tartalmaz egy jogcímet. Azt jelzi, hogy ezek közül a kulcsok közül melyiket használták az azonosító jogkivonat aláírásához. További információért, beleértve a [jogkivonatok érvényesítésének megismerését,](tokens-overview.md)tekintse meg az [Azure AD B2C token hivatkozását.](tokens-overview.md)
<!--TODO: Improve the information on this-->

Az azonosító jogkivonat aláírásának ellenőrzése után számos jogcím hitelesítést igényel. Példa:

* Ellenőrizze `nonce` a jogcímet a token-visszajátszásos támadások megelőzése érdekében. Az érték nek meg kell adnia a bejelentkezési kérelemben megadott értéket.
* Ellenőrizze `aud` a jogcímet annak érdekében, hogy az azonosító jogkivonat ot az alkalmazáshoz adta-e ki. Az alkalmazás értékének az alkalmazás azonosítójának kell lennie.
* Ellenőrizze `iat` a `exp` és a jogcímeket annak érdekében, hogy az azonosító jogkivonat nem járt le.

Az [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html)részletesen ismerteti azokat a további érvényesítéseket, amelyeket végre kell hajtania. Előfordulhat, hogy további jogcímeket is szeretne érvényesíteni, a forgatókönyvtől függően. Néhány gyakori érvényesítés:

* Annak biztosítása, hogy a felhasználó vagy a szervezet feliratkozott az alkalmazásra.
* Annak biztosítása, hogy a felhasználó megfelelő jogosultságokkal és jogosultságokkal rendelkezzen.
* Annak biztosítása, hogy a hitelesítés bizonyos erőssége megtörtént, például az Azure többtényezős hitelesítés használatával.

Az azonosító jogkivonatban szereplő jogcímekről az [Azure AD B2C token hivatkozása](tokens-overview.md)című témakörben talál további információt.

Miután érvényesítette az azonosító jogkivonatot, megkezdheti a munkamenetet a felhasználóval. Az alkalmazásban használja a jogcímeket az azonosító jogkivonatban a felhasználóval kapcsolatos információk megszerzéséhez. Ez az információ használható megjelenítésre, rekordokra, engedélyezésre és így tovább.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beszereznie
Ha a webalkalmazásoknak csak felhasználói folyamatokat kell végrehajtaniuk, kihagyhatja a következő néhány szakaszt. A következő szakaszokban szereplő információk csak azokra a webalkalmazásokra vonatkoznak, amelyeknek hitelesített hívásokat kell kezdeményezniük egy webes API-hoz, és amelyeket az Azure AD B2C véd.

Most, hogy aláírta a felhasználót az egyoldalas alkalmazásba, hozzáférési jogkivonatokat kaphat az Azure AD által védett webes API-k hívásához. Még akkor is, ha már `token` kapott egy jogkivonatot a válasz típusával, ezzel a módszerrel további erőforrások jogkivonatait szerezheti be anélkül, hogy a felhasználót újra átirányítané a bejelentkezéshez.

Egy tipikus webalkalmazás-folyamat, azt szeretné, `/token` hogy a végpont. Azonban a végpont nem támogatja a CORS-kérelmeket, így a frissítési jogkivonat lekéréséhez az AJAX-hívások nem lehetséges. Ehelyett használhatja az implicit folyamat egy rejtett HTML iframe elem, hogy új tokeneket más webes API-k. Íme egy példa, a sortörések az olvashatóság:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|{bérlő}| Kötelező | Az Azure AD B2C-bérlő neve|
{házirend}| Kötelező| A futtatandó felhasználói folyamat. Adja meg az Azure AD B2C-bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`vagy . |
| client_id |Kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító az [Azure Portalon.](https://portal.azure.com) |
| response_type |Kötelező |`id_token` Tartalmaznia kell az OpenID Connect bejelentkezéshez.  A válasz típusát `token`is tartalmazhatja. Ha itt `token` használja, az alkalmazás azonnal kaphat egy hozzáférési jogkivonatot az engedélyezési végponttól, anélkül, hogy egy második kérést az engedélyező végpont. Ha a `token` választípust `scope` használja, a paraméternek tartalmaznia kell egy hatókört, amely jelzi, hogy melyik erőforrásnak adja ki a jogkivonatot. |
| redirect_uri |Ajánlott |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás hitelesítési válaszokat küldhet és fogadhat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy URL-kódolásúnak kell lennie. |
| scope |Kötelező |A hatókörök térben elválasztott listája.  Jogkivonatok beszerzése, tartalmazza az összes hatókört, hogy szükség van a tervezett erőforrás. |
| response_mode |Ajánlott |Megadja azt a módszert, amely az eredményül kapott jogkivonat visszaküldéséhez szolgál az alkalmazásnak. Az implicit folyamathoz használja a használatát. `fragment` Két másik mód is megadható, `query` és `form_post`a, de nem működnek az implicit folyamatban. |
| state |Ajánlott |A kérelemben szereplő érték, amely a jogkivonat-válaszban kerül vissza.  Bármilyen használni kívánt tartalom karakterlánca lehet.  Általában egy véletlenszerűen generált, egyedi értéket használnak, hogy megakadályozzák a helyek közötti kérelem hamisítási támadások.  Az állapot is a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt. Például az az oldal vagy nézet, amelyen a felhasználó volt. |
| nonce között |Kötelező |Az alkalmazás által létrehozott, a kérelemben szereplő érték, amely jogcímként szerepel az eredményül kapott azonosítójogkivonatban.  Az alkalmazás ezután ellenőrizheti ezt az értéket a token-visszajátszási támadások csökkentése érdekében. Az érték általában egy randomizált, egyedi karakterlánc, amely azonosítja a kérelem eredetét. |
| Gyors |Kötelező |A frissítéshez és a tokenek betöltéséhez egy rejtett iframe,használja `prompt=none` annak biztosítására, hogy az iframe nem elakad a bejelentkezési oldalon, és azonnal visszatér. |
| login_hint |Kötelező |A frissítéshez és a tokenek betöltéséhez egy rejtett iframe-ben, adja meg a felhasználó felhasználónevét ebben a tippben, hogy különbséget tegyen a felhasználó egy adott időpontban több munkamenetközött. A felhasználónevet kinyerheti egy korábbi bejelentkezési `preferred_username` jogcím `profile` használatával (a hatókör szükséges `preferred_username` a jogcím fogadásához). |
| domain_hint |Kötelező |A következők egyike lehet: `consumers` vagy `organizations`.  A frissítés és a tokenek beszerzése `domain_hint` egy rejtett iframe,adja meg az értéket a kérelemben.  A `tid` jogcím kivonata egy korábbi bejelentkezési azonosító tokent, hogy `profile` meghatározza, melyik értéket kell `tid` használni (a hatókör szükséges a jogcím fogadásához). Ha `tid` a jogcím `9188040d-6c67-4c5b-b112-36a304b66dad` `domain_hint=consumers`értéke, használja a használatát.  Ellenkező esetben `domain_hint=organizations`használja a használatát. |

A paraméter `prompt=none` beállításával ez a kérés vagy sikeres, vagy azonnal sikertelen lesz, és visszatér az alkalmazáshoz.  A sikeres választ az alkalmazás a jelzett átirányítási URI,a `response_mode` paraméterben megadott módszerrel.

### <a name="successful-response"></a>Sikeres válasz
A sikeres válasz `response_mode=fragment` használatával néz ki, mint ez a példa:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Paraméter | Leírás |
| --- | --- |
| access_token |Az alkalmazás által kért jogkivonat. |
| token_type |A token típusa mindig bearer lesz. |
| state |Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |
| expires_in |Mennyi ideig érvényes a hozzáférési jogkivonat (másodpercben). |
| scope |Azok a hatókörök, amelyekre a hozzáférési jogkivonat érvényes. |

### <a name="error-response"></a>Hibaválasz
Hibaválaszok is elküldhetők az átirányítási URI-ba, hogy az alkalmazás megfelelően tudja kezelni őket.  A `prompt=none`esetén a várt hiba a következő példához hasonlít:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibatípusok osztályozására használható. A karakterlánc segítségével is reagálhat a hibákra. |
| error_description |Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibaüzenetet kapja az iframe-kérelemben, a felhasználónak újra be kell jelentkeznie az új jogkivonat lekéréséhez.

## <a name="refresh-tokens"></a>Tokenek frissítése
Az azonosító jogkivonatok és a hozzáférési jogkivonatok egy rövid idő elteltével lejárnak. Az alkalmazásnak készen kell állnia a tokenek rendszeres frissítésére.  A token ek bármelyikének frissítéséhez hajtsa végre ugyanazt a rejtett `prompt=none` iframe-kérelmet, amelyet egy korábbi példában használtunk, a paraméter használatával az Azure AD-lépések vezérléséhez.  Új `id_token` érték fogadásához használja a `response_type=id_token` `scope=openid`és a `nonce` és a paramétert.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése
Ha ki szeretné jelentkezni a felhasználót az alkalmazásból, irányítsa át a felhasználót az Azure AD-be a kijelentkezéshez. Ha nem irányítja át a felhasználót, előfordulhat, hogy újra hitelesítheti magát az alkalmazásba anélkül, hogy újra meg kellene adnia a hitelesítő adatait, mert érvényes egyszeri bejelentkezési munkamenettel rendelkeznek az Azure AD-vel.

Egyszerűen átirányíthatja a felhasználót arra, `end_session_endpoint` amely ugyanabban az OpenID Connect metaadat-dokumentumban szerepel, amelyet az Azonosító token [érvényesítése](#validate-the-id-token)című részben ismertetett. Példa:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| {bérlő} | Igen | Az Azure AD B2C-bérlő neve |
| {házirend} | Igen | A felhasználó által az alkalmazásból való kijelentkeztetéséhez használni kívánt felhasználói folyamat. |
| post_logout_redirect_uri | Nem | Az URL-címet, amelya felhasználót a sikeres kijelentkezés után át kell irányítani. Ha nem tartalmazza, az Azure AD B2C egy általános üzenetet jelenít meg a felhasználónak. |
| state | Nem | Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |


> [!NOTE]
> Irányítja a `end_session_endpoint` felhasználót, hogy a törli a felhasználó egyszeri bejelentkezési állapotát az Azure AD B2C. Azonban nem írja alá a felhasználót a felhasználó közösségi identitásszolgáltatómunkamenetéből. Ha a felhasználó ugyanazt az identitásszolgáltatót választja egy későbbi bejelentkezés során, a felhasználó újra hitelesíti magát, hitelesítő adatai megadása nélkül. Ha egy felhasználó ki akar jelentkezni az Azure AD B2C alkalmazásból, az nem feltétlenül jelenti azt, hogy például teljesen ki szeretne jelentkezni a Facebook-fiókjából. A helyi fiókok esetében azonban a felhasználó munkamenete megfelelően véget ér.
>

## <a name="next-steps"></a>További lépések

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Kódminta: Azure AD B2C a Microsoft Authentication Library for JavaScript-kódrendszerrel

[Egyoldalas alkalmazás készült msal.js az Azure AD B2C][github-msal-js-example] (GitHub)

Ez a minta a GitHubon célja, hogy segítsen az Azure AD B2C-hez való első lépésekhez egy egyszerű webalkalmazásban, amely [msal.js-sel][github-msal-js] és előugró stílusú hitelesítéssel készült.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
