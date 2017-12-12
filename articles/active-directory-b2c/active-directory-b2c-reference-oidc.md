---
title: "Bejelentkezés az OpenID Connect - Azure AD B2C webes |} Microsoft Docs"
description: "Webalkalmazások készítéséhez az Azure Active Directory végrehajtása az OpenID Connect hitelesítési protokoll használatával"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 21d420c8-3c10-4319-b681-adf2e89e7ede
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: 0eb4194307d1d3953fa1cd88ac014ac7c2ba7311
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Az Azure Active Directory B2C: Webes bejelentkezés az OpenID Connect
OpenID Connect egy olyan hitelesítési protokoll, OAuth 2.0, a biztonságos bejelentkezéshez felhasználók webalkalmazásokhoz használható platformra épül. Az Azure Active Directory B2C segítségével OpenID Connect (Azure AD B2C-vel) végrehajtásával, is erőforrás kihelyezése a regisztráció, bejelentkezés és más Identitáskezelés észlel a webalkalmazások Azure Active Directory (Azure AD). Ez az útmutató bemutatja, hogyan nyelvtől független módon. Bemutatja, hogyan lehet üzeneteket küldjön és fogadjon HTTP a nyílt forráskódú kódtárai bármelyikét használata nélkül.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) kibővíti az OAuth 2.0 *engedélyezési* protokollt használni egy *hitelesítési* protokoll. Ez lehetővé teszi, hogy az egyszeri bejelentkezés használatával OAuth végez. Akkor jelenik meg az egy *azonosító token*, amelyen egy biztonsági jogkivonatot, amely lehetővé teszi a felhasználó identitásának ellenőrzéséhez és a felhasználó alapvető profiladataihoz információt szerezni az ügyfél.

OAuth 2.0 terjeszti ki, mert emellett lehetővé teszi az alkalmazások biztonságos megszerzésére *hozzáférési jogkivonatok*. Access_tokens által védett erőforrások eléréséhez használható egy [engedélyezési server](active-directory-b2c-reference-protocols.md#the-basics). Ajánlott OpenID Connect, ha a webes alkalmazás, amely a kiszolgálón futtatott és böngészőn keresztül elért most felépítése. Ha azt szeretné, az Identitáskezelés hozzáadása a hordozható vagy asztali alkalmazásokhoz az Azure AD B2C használatával, használjon [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ahelyett, hogy az OpenID Connect.

Az Azure AD B2C kibővíti az OpenID Connect protokoll szabványt nagyobb, mint az egyszerű hitelesítéshez és engedélyezéshez. Okozna a [házirend paraméter](active-directory-b2c-reference-policies.md), amely lehetővé teszi az OpenID Connect használatával adhatja hozzá a felhasználói élményt – például a regisztráció, bejelentkezés és a profilkezelés – az alkalmazáshoz. Itt azt mutatja be valósít meg minden olyan ezeket a szolgáltatásokat a webalkalmazások OpenID Connect és házirendek segítségével. Is mutat be, hogyan kérhet hozzáférési jogkivonatok webes API-k eléréséhez.

A példa HTTP-kérelmekre a következő szakaszban használja, a minta B2C-címtárban, fabrikamb2c.onmicrosoft.com, valamint a mintaalkalmazást, https://aadb2cplayground.azurewebsites.net és házirendek. Szabadon próbálja ki a kérelmek saját kezűleg a ezeket az értékeket, vagy lecserélheti őket a saját.
Megtudhatja, hogyan [saját B2C-bérlő, alkalmazások és házirendek](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Hitelesítési kérések küldése
Ha a webalkalmazás kell hitelesíteni a felhasználót, majd hajtsa végre egy házirendet, utasíthatja-e a felhasználót, hogy a `/authorize` végpont. Az adatfolyam interaktív része azt, ha a felhasználó végrehajtja a műveletet, attól függően, hogy a házirend.

A kérelem, az ügyfél azt jelzi, szerzi be a felhasználó a szükséges engedélyekkel a `scope` paraméter és a házirendet, a végrehajtást a `p` paraméter. Három Példák különböző házirend segítségével (sortöréssel olvashatóság érdekében), a következő szakaszban találhatók. Követve betekintést nyerhet abba az egyes kérelmek működése, próbálja meg beilleszteni a kérelem egy böngészőbe, és azt futtatja.

#### <a name="use-a-sign-in-policy"></a>A bejelentkezési házirend
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>A regisztrációs szabályzatban használata
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Egy házirend-profil szerkesztése
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [Azure-portálon](https://portal.azure.com/) az alkalmazáshoz hozzárendelni. |
| response_type |Szükséges |A válasz-típus, az OpenID Connect tartalmaznia kell egy azonosító jogkivonatot. Ha a webalkalmazásnak is kell jogkivonatok hívásakor webes API-k, `code+id_token`, ahogy azt itt régebben már kötöttek. |
| redirect_uri |Javasolt |A `redirect_uri` paraméter az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell a `redirect_uri` paramétereket, és regisztrálta a portálon, azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték az Azure AD mindkét kérnek engedélyeket. A `openid` hatókör azt jelzi, a felhasználói azonosító-jogkivonatokat (több, a cikk későbbi részében ezen lesz) formájában adatait, és jelentkezzen be a felhasználó engedélyt. A `offline_access` hatókör megadása nem kötelező web Apps. Azt jelzi, hogy az alkalmazás kell egy *frissítési jogkivonat* hosszú élettartamú erőforrások elérése érdekében. |
| response_mode |Javasolt |A módszer, amelynek használatával az eredményül kapott engedélyezési kód küldi vissza az alkalmazást. Ez lehet akár `query`, `form_post`, vagy `fragment`.  A `form_post` válasz mód használata ajánlott legjobb biztonsági. |
| state |Javasolt |A kérelem is a biztonságijogkivonat-válaszban visszaadott szerepel érték. Bármely, a kívánt tartalmat karakterlánc lehet. Egy véletlenszerűen generált egyedi érték webhelyközi kérések hamisításának megakadályozása támadások megelőzése általában szolgál. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például a lap, amilyenek korábban voltak a is használatos. |
| Nonce |Szükséges |A kérelem (az alkalmazás által generált), amely szerepelni fog az eredményül kapott azonosító jogkivonat jogcímként szerepel érték. Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Az érték egy véletlenszerű egyedi karakterlánc, amely segítségével azonosíthatja a kérelem forrása általában. |
| P |Szükséges |A házirendet, amely végrehajtja. Olyan házirendet, amely a B2C bérlő létrehozása nevét. A házirend nevének értékét kell kezdődnie `b2c\_1\_`. További információ a házirendek és a [bővíthető szabályzat-keretrendszernek](active-directory-b2c-reference-policies.md). |
| parancssor |Választható |A típus a felhasználói beavatkozás szükséges. Jelenleg az egyetlen érvényes érték `login`, amely előírja, hogy a felhasználó megadja hitelesítő adataikkal, hogy kérésre. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználónak kapcsolatba a házirend-munkafolyamat végrehajtásához. Ez a felhasználó, írja be a felhasználónevét és jelszavát, bejelentkezés egy közösségi identitás regisztrál a könyvtár, vagy minden más szám lépésből áll, attól függően, hogy hogyan van definiálva a házirend az lehet, hogy magában.

Miután a felhasználó teljesítette a szabályzat, az Azure AD adja vissza az alkalmazást a jelzett választ `redirect_uri` paraméter megadott módon a `response_mode` paraméter. A válasz megegyezik a független a házirendet, amely végrehajtja a rendszer a fenti esetek mindegyikét.

A sikeres válasz használatával `response_mode=fragment` alábbihoz hasonlóan fog kinézni:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| id_token |Az alkalmazás által kért azonosítója jogkivonat. Az azonosító jogkivonat segítségével ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. További részleteket a azonosító-jogkivonatokat és azok tartalmát szerepelnek a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri, ha használt `response_type=code+id_token`. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot cél erőforrás igényelhetnek. Hitelesítési kódok nagyon rövid élettartamú. Ezek általában körülbelül 10 perc múlva lejár. |
| state |Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |

Hibaválaszok is küldhető a `redirect_uri` paramétert, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Egy hibakód karakterlánc típusú hibák előforduló és hibáinak reagálni azokra, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |
| state |Lásd az ebben a szakaszban az első tábla teljes leírását. Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |

## <a name="validate-the-id-token"></a>Az azonosító token ellenőrzése
Csak fogadása az azonosító tokent elég nem lehet hitelesíteni a felhasználót. Kell a azonosító jogkivonat-aláírás ellenőrzése és ellenőrizze a jogcímek, az alkalmazás követelményeinek / jogkivonat. Használja az Azure AD B2C [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényes nyilvános kulccsal történő titkosítás.

Nincsenek számos nyílt forráskódú kódtárai elérhető JWTs, attól függően, hogy a nyelvi preferencia szerinti ellenőrzése. Javasoljuk, hogy ezek a lehetőségek felfedezése megvalósító egyéni ellenőrzési logika helyett. Itt az információk hasznosak bizonyulnak a tudja, hogyan megfelelően használhatja a tárak.

Az Azure AD B2C rendelkezik az OpenID Connect metaadat-végpontjához, amely lehetővé teszi egy alkalmazás futásidőben Azure AD B2C-vel kapcsolatos információk beolvasása. Ezen információk közé tartozik a végpontok, lexikális elem tartalmának és jogkivonat-aláíró kulcsok. Nincs a JSON metaadat-dokumentum-házirendet a B2C-bérlőben. Például a metaadat-dokumentum esetében a `b2c_1_sign_in` -szabályzat `fabrikamb2c.onmicrosoft.com` helyen található:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

A konfigurációs dokumentum tulajdonságainak egyik `jwks_uri`, amelynek értéke lenne ugyanabban a házirendben:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Annak meghatározásához, hogy mely házirendet használt azonosító aláíró jogkivonat (és a metaadatok beolvasása honnan), két lehetősége van. Először a házirend neve szerepel a `acr` jogcím a Azonosítót jogkivonatban. A jogcímek egy azonosító jogkivonat elemzése információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). A másik lehetőség egy kódolására a házirend az értékét a `state` paraméter küldje a kérelmet, és ezután dekódolni a meghatározásához, hogy mely házirendet lett megadva. Mindkét módszer esetén érvényes.

Után jut hozzá a metaadat-dokumentum, az OpenID Connect metaadat-végpontjához, használhatja az RSA 256 nyilvános kulcsot (amely találhatók ezen a végponton) érvényesítése azonosító jogkivonat aláírását. Előfordulhat, hogy több idő álljon a végponti szereplő kulcsok, minden egyes által azonosított egy `kid` jogcímek. A fejléc az azonosító jogkivonat is tartalmaz egy `kid` jogcím, ami azt jelenti, amely ezeket a kulcsokat a Azonosítót jogkivonatban aláírásához használt. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md) (szakaszban [jogkivonatok ellenőrzése](active-directory-b2c-reference-tokens.md#token-validation), különösen).
<!--TODO: Improve the information on this-->

A azonosító jogkivonat aláírása ellenőrzését követően nincsenek vissza kell igazolnia több jogcímeket. Például:

* Érdemes ellenőrizni a `nonce` jogcím-token ismétléses támadások megelőzése érdekében. Az értékét a bejelentkezési kérelemben megadott kell lennie.
* Érdemes ellenőrizni a `aud` jogcím győződjön meg arról, hogy a Azonosítót jogkivonatban ki az alkalmazást. Az értéknek kell lennie az alkalmazás Azonosítóját a az alkalmazás.
* Érdemes ellenőrizni a `iat` és `exp` győződjön meg arról, hogy az azonosító jogkivonat nem járt jogcímeket.

Nincsenek is végre kell hajtania néhány további érvényesítést. Ezek a részletes leírása a [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html).  Érdemes további ellenőrizhesse, a forgatókönyvtől függően is. Néhány gyakori érvényesítést a következők:

* Győződjön meg arról, hogy a felhasználó/szervezeti rendelkezik regisztrálta az alkalmazást.
* Győződjön meg arról, hogy a felhasználó rendelkezik-e a megfelelő engedélyezési vagy jogosultságokkal.
* Győződjön meg arról, hogy bizonyos erősségével hitelesítési történt, például az Azure multi-factor Authentication.

A jogcímek egy Azonosítót jogkivonatban további információkért tekintse meg a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

Az azonosító token ellenőrzése után megkezdheti a munkamenet a felhasználóval. A jogcímek az Azonosítót jogkivonatban segítségével a felhasználó információt szerezni az alkalmazásban. Ezt az információt használja megjelenített rekordok és engedélyezési tartalmazza.

## <a name="get-a-token"></a>A jogkivonat beolvasása
Ha csak végrehajtási házirendek webalkalmazás van szüksége, továbbléphet a következő néhány szakasz. Ezek a szakaszok csak webes alkalmazásokat, amelyek kell hitelesített hívásokat egy webes API-hoz, és is védi az Azure AD B2C vonatkoznak.

Az engedélyezési kód beszerzett is beválthatja (használatával `response_type=code+id_token`) a kívánt erőforrás elküldésével jogkivonat egy `POST` elküldeni a kérelmet a `/token` végpont. A jogkivonatot kérhet csak erőforrás jelenleg az alkalmazás saját webes API-t. Az a magához jogkivonat kérésével egyezmény, hogy az alkalmazás ügyfél-Azonosítóját használja a hatóköreként:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| P |Szükséges |A házirendet, amely szerepel az engedélyezési kód megszerzésére. A kérelem nem használhatja másik szabályt. Vegye figyelembe, hogy hozzá ezt a paramétert a lekérdezési karakterlánc nem arra a `POST` törzsében. |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [Azure-portálon](https://portal.azure.com/) az alkalmazáshoz hozzárendelni. |
| grant_type |Szükséges |Az engedély megadása, amelynek támogatnia kell a típusú `authorization_code` a a hitelesítésikód-folyamata. |
| Hatókör |Javasolt |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték az Azure AD mindkét kérnek engedélyeket. A `openid` hatókör id_token paraméterek formájában a felhasználó adatait, és jelentkezzen be a felhasználó engedélyt jelzi. Az alkalmazás saját webes API, amely alkalmazás azonosítója megegyezik az ügyfél által képviselt-jogkivonat eléréséhez használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat kell hosszú élettartamú erőforrások elérése érdekében. |
| Kód |Szükséges |A folyamat első szakasza beszerzett engedélyezési kódot. |
| redirect_uri |Szükséges |A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kódot. |
| client_secret |Szükséges |A létrehozott alkalmazás titkos kulcs a [Azure-portálon](https://portal.azure.com/). Ez az alkalmazástitkok fontos biztonsági összetevő. Meg kell tárolja biztonságos helyen a kiszolgálón. Az ügyfélkulcs rendszeres időközönként elforgatása is kell. |

A sikeres token válasz néz ki:

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
| not_before |Az az idő, amelynél a jogkivonat érvényes, időbeli kortartományon. |
| token_type |A jogkivonat típusa érték. Az egyetlen típus, amely az Azure AD által támogatott `Bearer`. |
| access_token |Az aláírt JWT jogkivonat az Ön által kért. |
| Hatókör |A hatókörök, amelynél a jogkivonat érvényes érték. Ezek használhatók a gyorsítótárazáshoz jogkivonatok későbbi használatra. |
| expires_in |Mennyi ideig, amely a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás a jogkivonat segítségével szerezzen be további jogkivonatok a jelenlegi jogkivonat lejárata után is. Frissítési jogkivonatok hosszú élettartamú, és erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használható. További részletekért tekintse meg a [B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). Megjegyzés: kell használt a hatókör `offline_access` az engedélyezési és egy frissítési jogkivonat fogadásához a jogkivonat-kérelmeket. |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Egy hibakód karakterlánc típusú hibák előforduló és hibáinak reagálni azokra, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

## <a name="use-the-token"></a>A jogkivonat
Most, hogy olyan hozzáférési jogkivonatot már sikeresen szerezte be, használhatja a token kérelmek számára a háttérben futó webes API-khoz azzal, hogy belefoglalja azt a a `Authorization` fejléc:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>A token frissítése
Azonosító-jogkivonatokat rövid élettartamú. Frissítenie kell őket után nem tudnak hozzáférni az erőforrásokhoz a folytatáshoz. Megteheti egy másik elküldése `POST` elküldeni a kérelmet a `/token` végpont. Most, adja meg a `refresh_token` paraméter ahelyett, hogy a `code` paraméter:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paraméter | Szükséges | Leírás |
| --- | --- | --- |
| P |Szükséges |A házirendet, amely az eredeti frissítési jogkivonat használatával. A kérelem nem használhatja másik szabályt. Vegye figyelembe, hogy ez a paraméter hozzáadása, a lekérdezési karakterláncot, nem a FELADÁS egy vagy több szervezet. |
| client_id |Szükséges |Az alkalmazás azonosítója, amely a [Azure-portálon](https://portal.azure.com/) az alkalmazáshoz hozzárendelni. |
| grant_type |Szükséges |Az engedély megadása, amelynek kell lennie a hitelesítésikód-folyamata ezen szakasza a frissítési jogkivonat típusa. |
| Hatókör |Javasolt |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték az Azure AD mindkét kérnek engedélyeket. A `openid` hatókör azt jelzi, a felhasználói azonosító-jogkivonatokat formájában adatait, és jelentkezzen be a felhasználó engedélyt. Az alkalmazás saját webes API, amely alkalmazás azonosítója megegyezik az ügyfél által képviselt-jogkivonat eléréséhez használható. A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat kell hosszú élettartamú erőforrások elérése érdekében. |
| redirect_uri |Javasolt |A `redirect_uri` paraméter az alkalmazás, ahol kapott engedélyezési kódot. |
| refresh_token |Szükséges |Az eredeti frissítési jogkivonat a folyamat második szakasza beszerzett. Megjegyzés: kell használt a hatókör `offline_access` az engedélyezési és egy frissítési jogkivonat fogadásához a jogkivonat-kérelmeket. |
| client_secret |Szükséges |A létrehozott alkalmazás titkos kulcs a [Azure-portálon](https://portal.azure.com/). Ez az alkalmazástitkok fontos biztonsági összetevő. Meg kell tárolja biztonságos helyen a kiszolgálón. Az ügyfélkulcs rendszeres időközönként elforgatása is kell. |

A sikeres token válasz néz ki:

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
| not_before |Az az idő, amelynél a jogkivonat érvényes, időbeli kortartományon. |
| token_type |A jogkivonat típusa érték. Az egyetlen típus, amely az Azure AD által támogatott `Bearer`. |
| access_token |Az aláírt JWT jogkivonat az Ön által kért. |
| Hatókör |A hatókör, amely a jogkivonat érvényes, a későbbi használatra jogkivonatok gyorsítótárazás használható. |
| expires_in |Mennyi ideig, amely a hozzáférési jogkivonat érvénytelen (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás a jogkivonat segítségével szerezzen be további jogkivonatok a jelenlegi jogkivonat lejárata után is.  Frissítési jogkivonatok hosszú élettartamú, és erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használható. További részletekért tekintse meg a [B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok a következőhöz hasonló:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Egy hibakód karakterlánc típusú hibák előforduló és hibáinak reagálni azokra, amely használható besorolására használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha meg szeretné beléptetni a felhasználót az alkalmazásból, nincs elég törölni kívánja a felhasználói munkamenet az alkalmazás cookie-k vagy más célból. Az Azure AD-kijelentkezés is kell átirányítja a felhasználót. Ha nem sikerül, a felhasználó lehet újból hitelesítésre az alkalmazásnak, hogy a hitelesítő adataik ismételt beírása nélkül. Ennek az az oka érvényes egyszeri bejelentkezés munkamenetet az Azure ad-val rendelkeznek.

A felhasználó egyszerűen átirányíthatja a `end_session` endpoint OpenID Connect metaadat-dokumentum szereplő korábban az a "Ellenőrizze a Azonosítót jogkivonatban" szakaszban leírt:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| P |Szükséges |A felhasználó kívül az alkalmazás aláírásához használt kívánt házirendet. |
| post_logout_redirect_uri |Javasolt |Az URL-címet, a felhasználó után a rendszer átirányítja a sikeres kijelentkezési. Ha nincs megadva, az Azure AD B2C a felhasználó egy általános üzenetet jelenít meg. |

> [!NOTE]
> Bár irányítja a felhasználót, hogy a `end_session` végpont törölni fogja a néhány, a felhasználó állapotának egyszeri bejelentkezés az Azure AD B2C, nem a kívül a közösségi identity provider (IDP) munkamenet a felhasználó alá fogja írni. Ha a felhasználó kijelöli a azonos IDP későbbi bejelentkezés során, akkor lesz hitelesíthető, a hitelesítő adatok megadása nélkül. Ha egy felhasználó kijelentkezik a B2C alkalmazás azt szeretné, hogy nem feltétlenül jelenti szeretne kijelentkezni a Facebook-fiókkal. Azonban helyi fiókok esetében a felhasználói munkamenet véget ér megfelelően.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>A saját B2C-bérlő használata
Ha meg szeretné próbálni az ilyen kérelmeket szolgáltatást, először hajtsa végre a fenti három lépést, és az cserélje a példaértékeket a saját korábban leírt:

1. [A B2C bérlő létrehozása](active-directory-b2c-get-started.md), és a bérlő nevét használja a kérelemben.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) beszerzése egy azonosítót. Vegye fel az alkalmazás egy webes vagy webes API-t. Szükség esetén hozzon létre egy alkalmazás titkos kulcs.
3. [A házirendek létrehozása](active-directory-b2c-reference-policies.md) beszerzése a házirend nevét.

