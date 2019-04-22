---
title: Webes bejelentkezés OpenID Connect – Azure Active Directory B2C-|} A Microsoft Docs
description: Az OpenID Connect hitelesítési protokoll használatával az Azure Active Directory B2C webes alkalmazásokat hozhat létre.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6285a90a9dca305f3a9cd909af6c084c747daf99
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679057"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webalkalmazás-bejelentkezés OpenID-kapcsolattal az Azure Active Directory B2C-vel

OpenID Connect hitelesítést egy olyan hitelesítési protokoll, OAuth 2.0, biztonságosan bejelentkezhetnek a felhasználók webes alkalmazásokhoz használt illesztőprogramra épül. Az Azure Active Directory B2C használatával OpenID Connect (Azure AD B2C-vel) megvalósítását, is kiszervezik a regisztrációt, bejelentkezést és egyéb identitáskezelési funkciókat használhat a a webalkalmazások Azure Active Directory (Azure AD). Ez az útmutató bemutatja, hogyan ehhez egy nyelvtől független módon. Azt ismerteti, hogyan küldhetők és fogadhatók HTTP-üzenetek a nyílt forráskódú könyvtáraink bármelyikének használata nélkül.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kibővíti az OAuth 2.0 *engedélyezési* protokoll használható egy *hitelesítési* protokollt. A hitelesítési protokoll lehetővé teszi az egyszeri bejelentkezés. Ez bemutatja a egy *azonosító jogkivonat*, amely lehetővé teszi, hogy az ügyfél ellenőrizze a felhasználó identitását, és szerezze be a felhasználóval kapcsolatos főbb profiladatait.

Kibővíti az OAuth 2.0, mert azt is lehetővé teszi az alkalmazások biztonságos beszerezni *hozzáférési jogkivonatokat*. Hozzáférési jogkivonatok használatával által védett erőforrások eléréséhez egy [az engedélyezési kiszolgáló](active-directory-b2c-reference-protocols.md). OpenID Connect használata ajánlott. Ha rendelkezik a kiszolgálón futtatott és böngészőn keresztül elért webes alkalmazást fejleszt. Ha a Mobile Identitáskezelés hozzáadni kívánt, vagy használjon az Azure AD B2C asztali alkalmazásokat, [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ahelyett, hogy az OpenID Connect. Jogkivonatok kapcsolatos további információkért lásd: a [jogkivonatokat az Azure Active Directory B2C – áttekintés](active-directory-b2c-reference-tokens.md)

Az Azure AD B2C ehhez több, mint az egyszerű hitelesítés és engedélyezés a szabványos OpenID Connect protokollt bővíti. Azt mutatja be a [felhasználói folyamat paraméter](active-directory-b2c-reference-policies.md), amely lehetővé teszi, hogy a felhasználó hozzáadása az OpenID Connect használatával találkozik az alkalmazását, például a regisztráció, bejelentkezést és profilkezelést.

## <a name="send-authentication-requests"></a>Hitelesítési kérelem küldése

Ha a webalkalmazás kell hitelesíteni a felhasználót, és a egy felhasználói folyamat futtatása, azt a felhasználót, hogy irányíthatók a `/authorize` végpont. A felhasználó által készített függően a felhasználói folyamatot.

A kéréshez, az ügyfél azt jelzi, hogy a beszerezni a felhasználótól a szükséges engedélyekkel a `scope` paraméter és a felhasználói folyamat futtatását a `p` paraméter. Három Példák különböző felhasználói folyamat használatával (a sortörések az olvashatóság érdekében), a következő szakaszokban találhatók. Betekintést nyerhet az egyes kérések működését, próbálja meg beilleszteni a kérelem egy böngészőbe, és azt futtatja. Lecserélheti `fabrikamb2c` Ha rendelkezik ilyennel, és a egy felhasználói folyamat létrehozása a bérlő nevével.

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

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz hozzárendelt. |
| response_type | Igen | Tartalmaznia kell egy azonosító jogkivonat az OpenID Connect. Ha a webalkalmazás jogkivonatokat a webes API-k meghívására szolgáló is van szüksége, használhatja `code+id_token`. |
| redirect_uri | Nem | A `redirect_uri` paraméter az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell a `redirect_uri` paraméterek regisztrált az Azure Portalon, azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| scope | Igen | Hatókörök szóközzel elválasztott listáját. A `openid` hatókör azt jelzi, hogy jelentkezzen be a felhasználó és azonosító-jogkivonatokat formájában a felhasználóval kapcsolatos adatok beolvasása az engedélyt. A `offline_access` hatókör megadása nem kötelező a webes alkalmazásokhoz. Azt jelzi, hogy az alkalmazásnak szüksége lesz egy *frissítési jogkivonat* az erőforrásokhoz való kiterjesztett hozzáférést. |
| response_mode | Nem | A módszer, amellyel az eredményül kapott engedélyezési kód küldése az alkalmazásnak. Ez lehet `query`, `form_post`, vagy `fragment`.  A `form_post` Válaszmód ajánlott legjobb biztonsági. |
| state | Nem | A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat, amelyeket szeretne karakterlánc lehet. Egy véletlenszerűen generált egyedi érték webhelyközi kérések hamisításának megakadályozása támadások általában szolgál. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például a voltak a lap is szolgál. |
| egyszeri | Igen | A kérésben (az alkalmazás által létrehozott), amely az eredményül kapott azonosító jogkivonat jogcímként szerepel érték. Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Értéke általában egy véletlenszerű egyedi karakterlánc, amely a kérés eredetének azonosítására használhatók. |
| p | Igen | A felhasználói folyamatot, amely futtatásakor. Felhasználói folyamat, amely jön létre az Azure AD B2C-bérlő neve. A felhasználói folyamat nevét kell kezdődnie `b2c\_1\_`. |
| parancssor | Nem | A felhasználói beavatkozás szükséges típusa. Jelenleg az egyetlen érvényes érték `login`, amely arra kényszeríti a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem. |

Ezen a ponton a felhasználó kéri a munkafolyamat befejezéséhez. A felhasználó adja meg a felhasználónevüket és jelszavukat, és regisztráljon a közösségi, vagy jelentkezzen be a címtár előfordulhat. Attól függően, hogyan van definiálva a felhasználói folyamat lépéseket minden más szám lehet.

A felhasználó a felhasználói folyamat befejezése után a válasz az alkalmazáshoz, a kijelzett `redirect_uri` paramétert, a megadott módszer használatával a `response_mode` paraméter. A válasz megegyezik az egyes független a felhasználói folyamat az előző esetben.

A sikeres válasz használatával `response_mode=fragment` ehhez hasonlóan néz ki:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| id_token | Az azonosító jogkivonat, amely az alkalmazás kéri. Az azonosító jogkivonat segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. |
| Kód | Az engedélyezési kódot, amely az alkalmazás kéri, ha `response_type=code+id_token`. Az alkalmazás az engedélyezési kód használatával egy cél-erőforrásra vonatkozó hozzáférési jogkivonat kérése. Hitelesítési kódok általában körülbelül 10 perc múlva lejár. |
| state | Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

Hibaválaszok is lehet küldeni a `redirect_uri` paramétert, hogy az alkalmazás kezelni tudja azokat megfelelően:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A kód, amely a bekövetkező hibák típusú besorolására használható. |
| error_description | Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításához. |
| state | Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

## <a name="validate-the-id-token"></a>Az azonosító jogkivonat érvényesítése

Egy azonosító jogkivonat érkező már nem elég a felhasználó hitelesítéséhez. Az azonosító jogkivonat-aláírás ellenőrzése, és ellenőrizze a jogcímeket az alkalmazás igényeknek. Használja az Azure AD B2C [JSON webes jogkivonatainak (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú hitelesítésen jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek. Nincsenek elérhető JWTs, ellenőrzése preferencia a nyelvtől függően számos nyílt forráskódú-kódtár. Javasoljuk, hogy ezek a lehetőségek felfedezése a saját ellenőrzési logika megvalósítása helyett. 

Az Azure AD B2C az OpenID Connect metaadatok végpontja, amely lehetővé teszi egy alkalmazás futásidőben az Azure AD B2C-vel kapcsolatos információkat tartalmaz. Ezen információk közé tartozik a végpontokat, a jogkivonat tartalma és a jogkivonat-aláíró kulcsok. A B2C-bérlő minden egyes felhasználói folyamat JSON-metaadatok dokumentumok van. Például a metaadat-dokumentum számára a `b2c_1_sign_in` a felhasználói folyamat `fabrikamb2c.onmicrosoft.com` helyen található:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

A konfigurációs dokumentum tulajdonságainak egyike `jwks_uri`, amelynek értéke lenne, a felhasználói folyamatot:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Meghatározni, melyik felhasználói folyamatot azonosító regisztráló használt token (és, ahonnan a metaadatok lekérése), két lehetősége van. Először a felhasználói interakciósorozat neve szerepel a `acr` jogcím az azonosító jogkivonat. A másik lehetőség, hogy az értékét a felhasználói folyamat kódolása a `state` paraméter, amikor a kérelmet, és ezután dekódolni a meghatározásához, hogy mely felhasználói folyamat lett megadva. Mindkét módszer esetén érvényes.

Miután a metaadat-dokumentum, az OpenID Connect metaadat-végpontról, melyeket beszerezett, használhatja a RSA 256 nyilvános kulcsok érvényesíteni az aláírást a azonosító jogkivonat. Előfordulhat, hogy a végpont-több kulcsok, minden egyes által azonosított egy `kid` jogcím. Az azonosító jogkivonat feje is tartalmazza a `kid` igényelni, ami azt jelenti, amelyhez ezeket a kulcsokat a azonosító jogkivonat aláírásához használt.

Az azonosító jogkivonat aláírása érvényesítése után nincsenek ellenőriznie kell néhány jogcímeket. Például:

- Ellenőrizze a `nonce` jogcím ismétlésének támadások megelőzése érdekében. Az értéknek kell lennie, a bejelentkezési kérésben megadott.
- Ellenőrizze a `aud` győződjön meg arról, hogy az azonosító jogkivonat az alkalmazáshoz kiállított jogcímet. Az értéknek kell lennie az alkalmazás az Alkalmazásazonosítót.
- Ellenőrizze a `iat` és `exp` jogcímeket kell győződjön meg arról, hogy az azonosító jogkivonat lejárt még nem.

Nincsenek is végre kell hajtania néhány további ellenőrzések. A részletes leírását az ellenőrzés a [OpenID Connect Core specifikációja](https://openid.net/specs/openid-connect-core-1_0.html). Érdemes azt is, további jogcímek a forgatókönyvtől függően ellenőrzése. Néhány gyakori ellenőrzések a következők:

- Annak ellenőrzése, hogy a felhasználó és szervezet regisztrált az alkalmazáshoz.
- Annak ellenőrzése, hogy a felhasználó rendelkezik-e a megfelelő engedélyezési vagy jogosultságokkal.
- Annak ellenőrzése, hogy egy bizonyos hitelesítés erőssége történt, például az Azure multi-factor Authentication hitelesítéshez.

Után ellenőrizze, hogy az azonosító jogkivonat, munkamenet megkezdheti a felhasználóval. A jogcímek az azonosító jogkivonat segítségével a felhasználó adatainak beszerzése az alkalmazásban. Ezek az információk felhasználása megjelenítendő rekordok és engedélyezési tartalmazza.

## <a name="get-a-token"></a>Egy token beszerzése

Ha a webes alkalmazás csak a felhasználói folyamatok futtatását, kihagyhatja a következő néhány szakaszban. Ezek a szakaszok csak az alkalmazásokat, amelyek el kell végezni egy webes API-hívások hitelesített, és emellett az Azure AD B2C által védett webes érvényesek.

Az engedélyezési kód beszerzett beválthatja (használatával `response_type=code+id_token`) a kívánt erőforrást küldésével jogkivonat egy `POST` kérelmet a `/token` végpont. Az egyetlen erőforrás, amely egy jogkivonatot kérhet jelenleg az alkalmazás saját webes API-t. Az egyezmény saját magának egy jogkivonatot kér az, hogy használja az alkalmazás ügyfél-azonosító hatóköre:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
| p | Igen | A felhasználói folyamat, amellyel az engedélyezési kód beszerzése. A kérelem nem használhat egy másik felhasználói folyamatot. Adja hozzá ezt a paramétert, a lekérdezési karakterláncban, nem pedig a bejegyzés törzse. |
| client_id | Igen | Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz hozzárendelt. |
| grant_type | Igen | Engedély megadása, amelynek kell típusú `authorization_code` az engedélyezési kód folyamata. |
| scope | Nem | Hatókörök szóközzel elválasztott listáját. A `openid` hatókör azt jelzi, hogy a felhasználók bejelentkeztetése és id_token paraméterek formájában a felhasználóval kapcsolatos adatok beolvasása engedélyt. Az alkalmazás saját webes API-t, amely alkalmazás azonosítója megegyezik az ügyfél által jelölt-jogkivonat beolvasásához használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak kell-e a frissítési jogkivonatok az erőforrásokhoz való kiterjesztett hozzáférést. |
| Kód | Igen | A felhasználói folyamat elején beszerzett engedélyezési kód. |
| redirect_uri | Igen | A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kód. |
| client_secret | Igen | Az alkalmazás titkos létrehozott a [az Azure portal](https://portal.azure.com/). Ez az alkalmazástitkok egy fontos biztonsági összetevője. Meg kell tárolja biztonságos helyen a kiszolgálón. Módosítsa a titkos Ügyfélkód rendszeres időközönként. |

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
| --------- | ----------- |
| not_before | Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type | A token típusú értékké. `Bearer` jelenleg az egyetlen támogatott. |
| access_token | Az aláírt JWT-jogkivonat, amely a kért. |
| scope | A hatókörök, amelynek a token érvényességét. |
| expires_in | Az, hogy mennyi idő a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token | Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után. Frissítési jogkivonatok segítségével hosszabb ideig megőrizni erőforrásokhoz való hozzáférést. A hatókör `offline_access` kell használtak az engedélyezési és a jogkivonat-kérelmeket annak érdekében, hogy a frissítési jogkivonatok fogadása. |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | Ez a kód típusú előforduló hibák besorolására használható. |
| error_description | Egy üzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításához. |

## <a name="use-the-token"></a>A jogkivonat használatával

Most, hogy a hozzáférési jogkivonat beszerzése sikerült, használhatja a kérések a jogkivonatot a háttér-webes API-k által, beleértve a `Authorization` fejléc:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>A jogkivonat frissítése

ID jogkivonatok lejárnak, egy rövid idő alatt. Frissítse a jogkivonatokat, után folytatja a nem tudnak hozzáférni az erőforrásokhoz. Ha elküldi egy másik jogkivonatot frissítheti `POST` kérelmet a `/token` végpont. Ebben az esetben adja meg a `refresh_token` paraméter helyett a `code` paramétert:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
| p | Igen | A felhasználói folyamatot, amely az eredeti frissítési jogkivonat beszerzéséhez használt. A kérelem nem használhat egy másik felhasználói folyamatot. Adja hozzá ezt a paramétert, a lekérdezési karakterláncban, nem pedig a bejegyzés törzse. |
| client_id | Igen | Az alkalmazás AZONOSÍTÓJÁT, amely a [az Azure portal](https://portal.azure.com/) az alkalmazáshoz hozzárendelt. |
| grant_type | Igen | Engedély megadása, amely a frissítési jogkivonatok az engedélyezési kód folyamat ezen részének kell lennie a típusa. |
| scope | Nem | Hatókörök szóközzel elválasztott listáját. A `openid` hatókör azt jelzi, hogy jelentkezzen be a felhasználó és azonosító-jogkivonatokat formájában a felhasználóval kapcsolatos adatok beolvasása az engedélyt. Az alkalmazás saját webes API-t, amely alkalmazás azonosítója megegyezik az ügyfél által jelölt jogkivonatok küldéséhez használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak kell-e a frissítési jogkivonatok az erőforrásokhoz való kiterjesztett hozzáférést. |
| redirect_uri | Nem | A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kód. |
| refresh_token | Igen | Az eredeti frissítési jogkivonatot, amely a folyamat második részében szerezték be. A `offline_access` hatókört kell használni az engedélyezési és a jogkivonat-kérelmeket annak érdekében, hogy a frissítési jogkivonatok fogadása. |
| client_secret | Igen | Az alkalmazás titkos létrehozott a [az Azure portal](https://portal.azure.com/). Ez az alkalmazástitkok egy fontos biztonsági összetevője. Meg kell tárolja biztonságos helyen a kiszolgálón. Módosítsa a titkos Ügyfélkód rendszeres időközönként. |

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
| --------- | ----------- |
| not_before | Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type | A token típusú értékké. `Bearer` jelenleg az egyetlen támogatott. |
| access_token | Az aláírt JWT-jogkivonat, amely a kért. |
| scope | A hatókör, amelyhez a token érvényességét. |
| expires_in | Az, hogy mennyi idő a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token | Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után. Frissítési jogkivonatok segítségével hosszabb ideig megőrizni erőforrásokhoz való hozzáférést. |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | Ez a kód típusú előforduló hibák besorolására használható. |
| error_description | Egy üzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításához. |

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése

Ha azt szeretné, a felhasználó ki az alkalmazás aláírásához, nem elég az alkalmazás cookie-k törléséhez, vagy ellenkező esetben a felhasználóhoz, a munkamenet befejezéséhez. Jelentkezzen ki az Azure AD B2C átirányítja a felhasználót. Ha ezt nem, a felhasználó lehet az alkalmazás a hitelesítő adatok újbóli megadása nélkül hitelesítse magát újra.

A felhasználónak egyszerűen csak átirányíthatja a `end_session` végpontot, amely szerepel az OpenID Connect metaadat-dokumentum a korábban ismertetett:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
| p | Igen | A felhasználói folyamatot, amely a felhasználó ki az alkalmazás aláírásához használni kívánt. |
| post_logout_redirect_uri | Nem | Az URL-cím, amely a felhasználót át kell irányítani, sikeres bejelentkezés után. Ha nem tartalmaz, az Azure AD B2C-t jeleníti meg a felhasználó alábbi hibaüzenet jelenik meg. |

Irányítja a felhasználót, hogy a `end_session` végpont törlése a felhasználó egyszeri bejelentkezés állapotát az Azure AD B2C-vel némelyike, de azt nem jelentkezzen ki a közösségi identity provider (IDP)-munkamenetet a felhasználó. Ha a felhasználó az azonos Identitásszolgáltató későbbi bejelentkezés során, akkor rendszer hitelesíteni, a hitelesítő adatok megadása nélkül. Jelentkezzen ki az alkalmazást a felhasználó használni szeretne, ha ez nem feltétlenül jelenti azt szeretne jelentkezzen ki a saját Facebook-fiókban. Azonban ha helyi fiókot használ, a felhasználói munkamenet is lezárul megfelelően.

