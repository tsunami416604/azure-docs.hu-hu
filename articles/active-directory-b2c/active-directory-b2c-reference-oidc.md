---
title: Webes bejelentkezés OpenID-kapcsolattal az Azure Active Directory B2C |} A Microsoft Docs
description: Webalkalmazások létrehozása az Azure Active Directory megvalósítása az OpenID Connect hitelesítési protokoll használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 39a3164c27fa30250fe08e864db889eac844f646
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173003"
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: Webes bejelentkezés OpenID-kapcsolattal
OpenID Connect hitelesítést egy olyan hitelesítési protokoll, OAuth 2.0, biztonságosan bejelentkezhetnek a felhasználók webes alkalmazásokhoz használt illesztőprogramra épül. Az Azure Active Directory B2C használatával OpenID Connect (Azure AD B2C-vel) megvalósítását, is kiszervezik a regisztrációt, bejelentkezést és egyéb identitáskezelési funkciókat használhat a a webalkalmazások Azure Active Directory (Azure AD). Ez az útmutató bemutatja, hogyan ehhez egy nyelvtől független módon. Azt ismerteti, hogyan küldhetők és fogadhatók HTTP-üzenetek a nyílt forráskódú könyvtáraink bármelyikének használata nélkül.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kibővíti az OAuth 2.0 *engedélyezési* protokoll használható egy *hitelesítési* protokollt. Ez lehetővé teszi az egyszeri bejelentkezés OAuth használatával végezhető el. Ez bemutatja a egy *azonosító jogkivonat*, amelynek egy biztonsági jogkivonatot, amely lehetővé teszi az ügyfél ellenőrizze a felhasználó identitását, és szerezze be a felhasználóval kapcsolatos főbb profiladatait.

Kibővíti az OAuth 2.0, mivel lehetővé teszi alkalmazások biztonságosan beszerezni *hozzáférési jogkivonatokat*. Access_tokens által védett erőforrások eléréséhez használható egy [az engedélyezési kiszolgáló](active-directory-b2c-reference-protocols.md#the-basics). Javasoljuk, hogy OpenID Connect, amely a kiszolgálón futtatott és böngészőn keresztül elért webalkalmazás létrehozásakor. Ha azt szeretné, Identitáskezelés adhat a mobil- vagy asztali alkalmazásokhoz az Azure AD B2C segítségével, használjon [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ahelyett, hogy az OpenID Connect.

Az Azure AD B2C ehhez több, mint az egyszerű hitelesítés és engedélyezés a szabványos OpenID Connect protokollt bővíti. Azt mutatja be a [felhasználói folyamat paraméter](active-directory-b2c-reference-policies.md), amely lehetővé teszi, hogy adja hozzá a felhasználói élményt – mint például az OpenID Connect segítségével regisztrációs, bejelentkezési és profilok kezelése – az alkalmazás. Itt hogy bemutatják, hogyan megvalósítása ezek a tapasztalatok mindegyike webalkalmazásait az OpenID Connect- és felhasználói folyamatokat használhat. Azt is megmutatjuk, hozzáférési tokenek beszerzése a webes API-k eléréséhez.

A példa HTTP-kérelmek, a következő szakaszban használja, a minta a B2C-címtárat, fabrikamb2c.onmicrosoft.com, valamint a mintaalkalmazás https://aadb2cplayground.azurewebsites.net, és a felhasználói forgalmat. Ön kipróbálni a kérelmek saját maga, használja ezeket az értékeket, vagy is le kell cserélni a saját.
Ismerje meg, hogyan [saját B2C-bérlő, az alkalmazás és a felhasználói folyamatok beolvasása](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Hitelesítési kérelem küldése
A webalkalmazás kell hitelesíteni a felhasználót, és hajtsa végre a felhasználói folyamat, ha azt a felhasználót, hogy irányíthatók a `/authorize` végpont. Ez az interaktív részének a folyamatot, ahol a felhasználó által készített művelet, attól függően, a felhasználói folyamatot.

A kéréshez, az ügyfél azt jelzi, hogy a beszerezni a felhasználótól a szükséges engedélyekkel a `scope` paraméter és a felhasználói folyamat végrehajtása a `p` paraméter. Három Példák különböző felhasználói folyamat használatával (a sortörések az olvashatóság érdekében), a következő szakaszokban találhatók. Betekintést nyerhet az egyes kérések működését, próbálja meg beilleszteni a kérelem egy böngészőbe, és azt futtatja.

#### <a name="use-a-sign-in-user-flow"></a>Felhasználói bejelentkezési folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Regisztráció felhasználói folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Egy profil szerkesztése felhasználói folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz rendelt. |
| response_type |Szükséges |Válasz típusa, amely az OpenID Connect tartalmaznia kell egy azonosító jogkivonat. Ha a webalkalmazás jogkivonatokat a webes API-k meghívására szolgáló is van szüksége, használhat `code+id_token`, ahogy használtuk. |
| redirect_uri |Ajánlott |A `redirect_uri` paraméter az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell a `redirect_uri` paraméterek regisztrált a portálon, azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| scope |Szükséges |Hatókörök szóközzel elválasztott listáját. Egy hatókör értéke azt jelzi, hogy az Azure AD mindkét engedélyeket kérnek. A `openid` hatókör azt jelzi, hogy jelentkezzen be a felhasználó és a felhasználói azonosító-jogkivonatokat (lásd a Ez a cikk későbbi részében lesz) formájában kapcsolatos adatok beolvasása engedélyt. A `offline_access` hatókör megadása nem kötelező web apps számára. Azt jelzi, hogy kell-e az alkalmazás egy *frissítési jogkivonat* hosszú élettartamú erőforrások eléréséhez. |
| response_mode |Ajánlott |A módszer, amellyel az eredményül kapott engedélyezési kód küldi vissza az alkalmazást. Ez lehet `query`, `form_post`, vagy `fragment`.  A `form_post` Válaszmód ajánlott legjobb biztonsági. |
| state |Ajánlott |A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Egy véletlenszerűen generált egyedi érték webhelyközi kérések hamisításának megakadályozása támadások általában szolgál. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például a voltak a lap is szolgál. |
| egyszeri |Szükséges |A kérésben (az alkalmazás által generált), amely az eredményül kapott azonosító jogkivonat jogcímként szerepeljenek érték. Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Értéke általában egy véletlenszerű egyedi karakterlánc, amely a kérés eredetének azonosítására használhatók. |
| p |Szükséges |A felhasználói folyamatot, amely lesz végrehajtva. A B2C-bérlőben létrehozott felhasználói folyamat neve. A felhasználói folyamat nevének értékét kell kezdődnie `b2c\_1\_`. További információ a házirendek és a [bővíthető felhasználói folyamat keretében](active-directory-b2c-reference-policies.md). |
| parancssor |Optional |A felhasználói beavatkozás szükséges típusa. Jelenleg az egyetlen érvényes érték `login`, amely arra kényszeríti a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználó kéri a felhasználói folyamat munkafolyamat végrehajtásához. Ez a felhasználó megadja a felhasználónevüket és jelszavukat, bejelentkezés egy közösségi identitás való regisztráláskor a címtárban, vagy bármely más több lépést, attól függően, hogyan van definiálva a felhasználói folyamatot is járhat.

A felhasználó a felhasználói folyamat befejezése után az Azure AD választ az alkalmazást a jelzett `redirect_uri` paramétert, a megadott módszer használatával a `response_mode` paraméter. A válasz megegyezik az egyes független a végrehajtott felhasználói folyamatot az előző esetben.

A sikeres válasz használatával `response_mode=fragment` ehhez hasonlóan néz ki:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az azonosító jogkivonat, amely az alkalmazás kéri. Az azonosító jogkivonat segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. Azonosító-jogkivonatokat és azok tartalmát kapcsolatos további információk szerepelnek a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |
| Kód |Az engedélyezési kódot, amely az alkalmazás kéri, ha használta `response_type=code+id_token`. Az alkalmazás az engedélyezési kód használatával egy cél-erőforrásra vonatkozó hozzáférési jogkivonat kérése. Engedélyezési kódokat is nagyon rövid ideig tartó. Ezek általában körülbelül 10 perc múlva lejár. |
| state |Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

Hibaválaszok is lehet küldeni a `redirect_uri` paramétert, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterlánc típusú hibák fordulnak elő, és reagálni azokra, hibák, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |
| state |Lásd az ebben a szakaszban az első táblázat a teljes leírását. Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

## <a name="validate-the-id-token"></a>Az azonosító jogkivonat érvényesítése
Egy azonosító jogkivonat érkező már nem elég a felhasználó hitelesítéséhez. Az azonosító jogkivonat-aláírás ellenőrzése kell, és ellenőrizze a jogcímeket az alkalmazáskövetelmények szerint. Használja az Azure AD B2C [JSON webes jogkivonatainak (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú hitelesítésen jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek.

Nincsenek elérhető JWTs, ellenőrzése preferencia a nyelvtől függően számos nyílt forráskódú-kódtár. Javasoljuk, hogy ezek a lehetőségek felfedezése a saját ellenőrzési logika megvalósítása helyett. Az adatok itt lesz hasznos foglalkozhatunk azzal, hogyan megfelelően használni az ezeket a kódtárakat.

Az Azure AD B2C-vel rendelkezik az OpenID Connect metaadatok végpontja, amely lehetővé teszi egy alkalmazás futásidőben az Azure AD B2C-vel kapcsolatos információkat lekérni. Ezen információk közé tartozik a végpontokat, a jogkivonat tartalma és a jogkivonat-aláíró kulcsok. A B2C-bérlő minden egyes felhasználói folyamat JSON-metaadatok dokumentumok van. Például a metaadat-dokumentum számára a `b2c_1_sign_in` a felhasználói folyamat `fabrikamb2c.onmicrosoft.com` helyen található:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

A konfigurációs dokumentum tulajdonságainak egyike `jwks_uri`, amelynek értéke lenne, a felhasználói folyamatot:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Meghatározni, melyik felhasználói folyamatot azonosító regisztráló használt token (és az, ahonnan a metaadatok beolvasása), két lehetősége van. Először a felhasználói interakciósorozat neve szerepel a `acr` jogcím az azonosító jogkivonat. A jogcímeket egy azonosító jogkivonat elemzése információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). A másik lehetőség, hogy az értékét a felhasználói folyamat kódolása a `state` paraméter, amikor a kérelmet, és ezután dekódolni a meghatározásához, hogy mely felhasználói folyamat lett megadva. Mindkét módszer esetén érvényes.

A metaadat-dokumentum, az OpenID Connect metaadat-végpontról, melyeket beszerezett, miután az RSA 256 nyilvános kulcsot (amely a következő helyen találhatók a végpont) is használhatja az azonosító jogkivonat aláírása ellenőrzése. A végpont adott időben felsorolt több kulcs van, minden egyes által azonosított egy `kid` jogcím. Az azonosító jogkivonat feje is tartalmazza a `kid` igényelni, ami azt jelenti, amelyhez ezeket a kulcsokat a azonosító jogkivonat aláírásához használt. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md) (szakaszát [jogkivonatok érvényességének](active-directory-b2c-reference-tokens.md#token-validation), különösen).
<!--TODO: Improve the information on this-->

Az azonosító jogkivonat aláírása érvényesítése után nincsenek ellenőriznie kell néhány jogcímeket. Például:

* Ellenőrizni kell a `nonce` jogcím ismétlésének támadások megelőzése érdekében. Az értéknek kell lennie, a bejelentkezési kérésben megadott.
* Ellenőrizni kell a `aud` győződjön meg arról, hogy az azonosító jogkivonat az alkalmazáshoz kiállított jogcímet. Az értéknek kell lennie a az alkalmazás azonosítója.
* Ellenőrizni kell a `iat` és `exp` jogcímeket kell győződjön meg arról, hogy az azonosító jogkivonat nem járt le.

Nincsenek is végre kell hajtania néhány további ellenőrzések. Ezek a részletes leírását a [OpenID Connect Core specifikációja](https://openid.net/specs/openid-connect-core-1_0.html).  Érdemes azt is, további jogcímek a forgatókönyvtől függően ellenőrzése. Néhány gyakori ellenőrzések a következők:

* Annak ellenőrzése, hogy a felhasználó és szervezet regisztrált az alkalmazáshoz.
* Annak ellenőrzése, hogy a felhasználó rendelkezik-e a megfelelő engedélyezési vagy jogosultságokkal.
* Annak ellenőrzése, hogy egy bizonyos hitelesítés erőssége történt, például az Azure multi-factor Authentication hitelesítéshez.

Egy azonosító jogkivonat jogcímeiben további információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

Miután ellenőrizte az azonosító jogkivonat, munkamenet megkezdheti a felhasználóval. A jogcímek az azonosító jogkivonat segítségével a felhasználó adatainak beszerzése az alkalmazásban. Ezek az információk felhasználása megjelenítendő rekordok és engedélyezési tartalmazza.

## <a name="get-a-token"></a>Egy token beszerzése
Ha csak a felhasználói folyamatok végrehajtása az webes alkalmazás van szüksége, folytassa a következő néhány szakaszban. Ezek a szakaszok csak webes alkalmazásokat, amelyeket el kell végezni a webes API-hívások hitelesített, és Azure AD B2C által védett is érvényesek.

Az engedélyezési kód beszerzett beválthatja (használatával `response_type=code+id_token`) a kívánt erőforrást küldésével jogkivonat egy `POST` kérelmet a `/token` végpont. Az egyetlen erőforrás, amely egy jogkivonatot kérhet jelenleg az alkalmazás saját webes API-t. Az egyezmény saját magának egy jogkivonatot kér az, hogy az alkalmazás ügyfél-Azonosítóját használja, a hatókör:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| p |Szükséges |A felhasználói folyamat, amellyel az engedélyezési kód beszerzése. A kérelem nem használhat egy másik felhasználói folyamatot. Vegye figyelembe, hogy hozzá ezt a paramétert a lekérdezési karakterlánc nem arra a `POST` törzse. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz rendelt. |
| grant_type |Szükséges |Engedély megadása, amelynek kell típusú `authorization_code` az engedélyezési kód folyamata. |
| scope |Ajánlott |Hatókörök szóközzel elválasztott listáját. Egy hatókör értéke azt jelzi, hogy az Azure AD mindkét engedélyeket kérnek. A `openid` hatókör azt jelzi, hogy a felhasználók bejelentkeztetése és id_token paraméterek formájában a felhasználóval kapcsolatos adatok beolvasása engedélyt. Az alkalmazás saját webes API-t, amely alkalmazás azonosítója megegyezik az ügyfél által jelölt-jogkivonat beolvasásához használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat szükséges hosszú élettartamú erőforrásokhoz való hozzáférés. |
| Kód |Szükséges |A folyamat első szakasza beszerzett engedélyezési kód. |
| redirect_uri |Szükséges |A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kód. |
| client_secret |Szükséges |A létrehozott alkalmazás titkos kulcsot a [az Azure portal](https://portal.azure.com/). Ez az alkalmazástitkok egy fontos biztonsági összetevője. Meg kell tárolja biztonságos helyen a kiszolgálón. A titkos Ügyfélkód rendszeres időközönként forgatása is kell. |

Token sikeres válasz hasonlóan néz ki:

```
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
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type |A token típusú értékké. Az egyetlen típus, amely az Azure AD által támogatott `Bearer`. |
| access_token |Az aláírt JWT-jogkivonat, amely a kért. |
| scope |A hatókörök, amelynek a token érvényességét. Ezek használhatók a jogkivonatok későbbi használatra gyorsítótárazáshoz. |
| expires_in |Az, hogy mennyi idő a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után. Frissítési jogkivonatok hosszú élettartamú és erőforrásokhoz való hozzáférés megőrzése a hosszabb ideig is használható. További részletekért tekintse meg a [B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). Vegye figyelembe, hogy kell használta a hatókör `offline_access` az engedélyezési és a jogkivonat-kérelmeket annak érdekében, hogy a frissítési jogkivonatok fogadása. |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterlánc típusú hibák fordulnak elő, és reagálni azokra, hibák, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

## <a name="use-the-token"></a>A jogkivonat használatával
Most, hogy a hozzáférési jogkivonat beszerzése sikerült, használhatja a kérések a jogkivonatot a háttér-webes API-k által, beleértve a `Authorization` fejléc:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>A jogkivonat frissítése
ID jogkivonatok olyan rövid élettartamú. Frissítenie kell azokat után folytatja a nem tudnak hozzáférni az erőforrásokhoz. Ha elküldi egy másik megteheti `POST` kérelmet a `/token` végpont. Ebben az esetben adja meg a `refresh_token` paraméter helyett a `code` paramétert:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paraméter | Szükséges | Leírás |
| --- | --- | --- |
| p |Szükséges |A felhasználói folyamatot, amely az eredeti frissítési jogkivonat beszerzéséhez használt. A kérelem nem használhat egy másik felhasználói folyamatot. Vegye figyelembe, hogy ez a paraméter hozzáadása a lekérdezési karakterláncban, nem pedig a bejegyzés törzse. |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz rendelt. |
| grant_type |Szükséges |Engedély megadása, amelynek támogatnia kell a frissítési jogkivonatok esetében ez a hitelesítési kódfolyamat alapját képező típusa. |
| scope |Ajánlott |Hatókörök szóközzel elválasztott listáját. Egy hatókör értéke azt jelzi, hogy az Azure AD mindkét engedélyeket kérnek. A `openid` hatókör azt jelzi, hogy jelentkezzen be a felhasználó és azonosító-jogkivonatokat formájában a felhasználóval kapcsolatos adatok beolvasása az engedélyt. Az alkalmazás saját webes API-t, amely alkalmazás azonosítója megegyezik az ügyfél által jelölt-jogkivonat beolvasásához használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat szükséges hosszú élettartamú erőforrásokhoz való hozzáférés. |
| redirect_uri |Ajánlott |A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kód. |
| refresh_token |Szükséges |Az eredeti frissítési token az a folyamat második alapját képező beszerzett. Vegye figyelembe, hogy kell használta a hatókör `offline_access` az engedélyezési és a jogkivonat-kérelmeket annak érdekében, hogy a frissítési jogkivonatok fogadása. |
| client_secret |Szükséges |A létrehozott alkalmazás titkos kulcsot a [az Azure portal](https://portal.azure.com/). Ez az alkalmazástitkok egy fontos biztonsági összetevője. Meg kell tárolja biztonságos helyen a kiszolgálón. A titkos Ügyfélkód rendszeres időközönként forgatása is kell. |

Token sikeres válasz hasonlóan néz ki:

```
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
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type |A token típusú értékké. Az egyetlen típus, amely az Azure AD által támogatott `Bearer`. |
| access_token |Az aláírt JWT-jogkivonat, amely a kért. |
| scope |A hatókör, amely a jogkivonat érvényes, amely későbbi használatra jogkivonatok gyorsítótárazáshoz használható. |
| expires_in |Az, hogy mennyi idő a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után.  Frissítési jogkivonatok hosszú élettartamú és erőforrásokhoz való hozzáférés megőrzése a hosszabb ideig is használható. További részletekért tekintse meg a [B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterlánc típusú hibák fordulnak elő, és reagálni azokra, hibák, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha szeretne bejelentkezni a felhasználó ki az alkalmazásból, akkor sem elég, törölje az alkalmazás a cookie-k vagy egyéb célból a munkamenet a felhasználó. Jelentkezzen ki az Azure ad-hez is kell átirányítja a felhasználót. Ha ezt nem, a felhasználó lehet az alkalmazásba a hitelesítő adatok újbóli megadása nélkül hitelesítse magát újra. Ennek az oka egy érvényes egyszeri bejelentkezés munkamenetet az Azure ad-vel rendelkeznek.

A felhasználónak egyszerűen csak átirányíthatja a `end_session` végpontot, amely szerepel az OpenID Connect metaadat-dokumentumban leírt korábban az a "érvényesítés az azonosító jogkivonat" szakaszban:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| p |Szükséges |A felhasználói folyamatot, amely a felhasználó ki az alkalmazás aláírásához használni kívánt. |
| post_logout_redirect_uri |Ajánlott |Az URL-cím, amely a felhasználó után a rendszer átirányítja a sikeres kijelentkezést. Ha a lehetőség nem része, Azure AD B2C-vel a felhasználó egy általános üzenetet jeleníti meg. |

> [!NOTE]
> Bár a irányítja a felhasználót, hogy a `end_session` végpont törli a felhasználó egyszeri bejelentkezés állapotát az Azure AD B2C-vel némelyike, nem a tartományon kívül közösségi identity provider (IDP) a munkamenet a felhasználó alá fogja írni. Ha a felhasználó az azonos Identitásszolgáltató későbbi bejelentkezés során, akkor lesz hitelesíthető, a hitelesítő adatok megadása nélkül. Ha egy felhasználó kijelentkezik a B2C-alkalmazást szeretné, ez nem feltétlenül jelenti szeretne jelentkezzen ki a saját Facebook-fiókban. Azonban helyi fiókok esetén a felhasználói munkamenet véget ér megfelelően.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>A saját B2C-bérlő használatához
Próbálja meg ezeket a kérelmeket a saját maga szeretné, ha először hajtsa végre az alábbi három lépést, és az ezután cserélje le a példában szereplő értékeket a saját fentebb leírt:

1. [B2C-bérlő létrehozása](active-directory-b2c-get-started.md), és a kérések a bérlő nevével.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) beszerzése az alkalmazás azonosítója. Egy webalkalmazás vagy webes API belefoglalása az alkalmazásban. Szükség esetén hozzon létre egy.
3. [A felhasználói folyamatok létrehozása](active-directory-b2c-reference-policies.md) beszerzése a felhasználói folyamat nevét.

