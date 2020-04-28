---
title: Webes bejelentkezés OpenID Connecttel – Azure Active Directory B2C
description: Hozzon létre webalkalmazásokat az OpenID Connect hitelesítési protokoll használatával Azure Active Directory B2Cban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264387"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webes bejelentkezés OpenID-kapcsolattal Azure Active Directory B2C

Az OpenID Connect egy OAuth 2,0-re épülő hitelesítési protokoll, amely a felhasználók biztonságos aláírására használható a webes alkalmazásokban. Az OpenID Connect Azure Active Directory B2C (Azure AD B2C) implementációjának használatával kioszthatja a regisztrációt, a bejelentkezést és más Identitáskezelés-kezelési tapasztalatokat a webalkalmazásokban a Azure Active Directory (Azure AD) szolgáltatásba. Ebből az útmutatóból megtudhatja, hogyan teheti meg ezt a nyelvet egymástól független módon. Ismerteti, hogyan küldhet és fogadhat HTTP-üzeneteket a nyílt forráskódú kódtárak használata nélkül.

Az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) kiterjeszti a OAuth 2,0 *engedélyezési* protokollt *hitelesítési* protokollként való használatra. Ez a hitelesítési protokoll lehetővé teszi az egyszeri bejelentkezés végrehajtását. Bevezeti az *azonosító jogkivonat*fogalmát, amely lehetővé teszi az ügyfél számára, hogy ellenőrizze a felhasználó identitását, és beszerezze a felhasználó alapszintű profiljának adatait.

Mivel a kiterjeszti a OAuth 2,0-es alkalmazást, lehetővé teszi az alkalmazások számára a *hozzáférési tokenek*biztonságos beszerzését is. Hozzáférési jogkivonatokkal férhet hozzá az [engedélyezési kiszolgáló](protocols-overview.md)által védett erőforrásokhoz. Az OpenID Connect használata akkor ajánlott, ha olyan webalkalmazást hoz létre, amely egy kiszolgálón fut, és böngészőn keresztül érhető el. A jogkivonatokkal kapcsolatos további információkért tekintse [meg a jogkivonatok áttekintését Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C kiterjeszti a szabványos OpenID Connect protokollt, hogy több, mint egyszerű hitelesítést és engedélyezést végezzen. Bevezeti a [felhasználói folyamat paramétert](user-flow-overview.md), amely lehetővé teszi, hogy az OpenID Connect használatával felhasználói élményeket vegyen fel az alkalmazásba, például a regisztrálást, a bejelentkezést és a profilok kezelését.

## <a name="send-authentication-requests"></a>Hitelesítési kérelmek küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, és futtatnia kell egy felhasználói folyamatot, akkor a felhasználó a `/authorize` végpontra irányíthatja a felhasználót. A felhasználó a felhasználói folyamattól függően végrehajtja a műveletet.

Ebben a kérelemben az ügyfél a `scope` paraméterben szereplő felhasználótól beszerzett engedélyeket jelzi, és megadja a futtatandó felhasználói folyamatot. Ha úgy érzi, hogy a kérés hogyan működik, próbálja meg beilleszteni a kérést egy böngészőben, és futtassa azt. Cserélje `{tenant}` le a helyére a bérlő nevét. A `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` helyére írja be annak az alkalmazásnak az azonosítóját, amelyet korábban regisztrált a bérlőben. Módosítsa a szabályzat nevét ()`{policy}`a bérlőben található szabályzat nevére (például `b2c_1_sign_in`:).

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
| Bérlő | Igen | A Azure AD B2C bérlő neve |
| politika | Igen | A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in`, `b2c_1_sign_up`, vagy. `b2c_1_edit_profile` |
| client_id | Igen | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója. |
| egyszeri | Igen | A kérelemben szereplő, az eredményül kapott azonosító jogkivonatban található (az alkalmazás által generált) érték. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy randomizált egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
| response_type | Igen | Tartalmaznia kell egy azonosító jogkivonatot az OpenID Connecthez. Ha a webalkalmazásnak szüksége van a webes API-k meghívásához szükséges jogkivonatokra is, használhatja `code+id_token`a következőt:. |
| scope | Igen | A hatókörök szóközzel tagolt listája. A `openid` hatókör a felhasználónak való bejelentkezéshez és az azonosító tokenek formájában kapott információk beszerzéséhez szükséges engedélyt jelöli. A `offline_access` hatókör nem kötelező a webalkalmazásokhoz. Azt jelzi, hogy az alkalmazásnak *frissítési jogkivonatra* lesz szüksége az erőforrásokhoz való kiterjesztett hozzáféréshez. |
| gyors | Nem | A kötelező felhasználói beavatkozás típusa. Ebben az esetben az egyetlen érvényes érték `login`, amely arra kényszeríti a felhasználót, hogy adja meg a kéréshez tartozó hitelesítő adatait. |
| redirect_uri | Nem | Az `redirect_uri` alkalmazás paraméterét, ahol a hitelesítési válaszokat el lehet juttatni és fogadni az alkalmazás. Pontosan meg kell egyeznie a Azure Portalban regisztrált `redirect_uri` paraméterek egyikével, azzal a különbséggel, hogy az URL-címet kódolni kell. |
| response_mode | Nem | Az eredményül kapott engedélyezési kód az alkalmazásba való visszaküldéséhez használt módszer. A következők egyike `query`lehet `form_post`:, `fragment`, vagy.  A `form_post` válasz mód ajánlott a legjobb biztonság érdekében. |
| state | Nem | A kérelemben szereplő érték, amelyet a jogkivonat válaszában is visszaadott. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a helyek közötti kérelmek hamisításának megelőzésére szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használható a hitelesítési kérelem végrehajtása előtt, például a lapon. |

Ekkor a rendszer megkéri a felhasználót, hogy fejezze be a munkafolyamatot. Előfordulhat, hogy a felhasználónak meg kell adnia a felhasználónevét és jelszavát, be kell jelentkeznie egy közösségi identitásba, vagy regisztrálnia kell a címtárat. A felhasználói folyamat meghatározásának módjától függően bármilyen más lépés is lehet.

Miután a felhasználó befejezte a felhasználói folyamatot, egy választ ad vissza az alkalmazásnak a jelzett `redirect_uri` paraméterben a `response_mode` paraméterben megadott metódus használatával. A válasz ugyanaz, mint az előző esetekben, a felhasználói folyamattól függetlenül.

A sikeres válasz az `response_mode=fragment` alábbi módon fog kinézni:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| id_token | Az alkalmazás által kért azonosító jogkivonat. Az azonosító token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. |
| code | Az alkalmazás által kért engedélyezési kód, ha Ön használta `response_type=code+id_token`. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési tokent a cél erőforráshoz. Az engedélyezési kódok általában körülbelül 10 perc elteltével lejárnak. |
| state | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, `state` hogy a kérelemben és a válaszban szereplő értékek azonosak-e. |

A rendszer hibaüzeneteket is küldhet a `redirect_uri` paraméternek, hogy az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | Kód, amely a felmerülő hibák típusának besorolására használható. |
| error_description | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |
| state | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, `state` hogy a kérelemben és a válaszban szereplő értékek azonosak-e. |

## <a name="validate-the-id-token"></a>AZONOSÍTÓ jogkivonat ellenőrzése

Az azonosító jogkivonat fogadása nem elegendő a felhasználó hitelesítéséhez. Érvényesítse az azonosító jogkivonat aláírását, és ellenőrizze az alkalmazás követelményei szerint a jogkivonatban lévő jogcímeket. Azure AD B2C [JSON webes tokeneket (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú titkosítást használ a tokenek aláírásához, és ellenőrizze, hogy érvényesek-e. Számos olyan nyílt forráskódú kódtár van, amely a JWTs ellenőrzéséhez használható, a kívánt nyelvtől függően. Javasoljuk, hogy a saját érvényesítési logikájának megvalósítása helyett ezeket a lehetőségeket vizsgálja.

Azure AD B2C rendelkezik egy OpenID Connect metaadat-végponttal, amely lehetővé teszi, hogy az alkalmazások a futtatókörnyezetről Azure AD B2C információkat kapjanak. Ezek az adatok a végpontokat, a jogkivonat tartalmát és a jogkivonat-aláíró kulcsokat tartalmazzák. A B2C-bérlőben minden felhasználói folyamathoz van egy JSON-metaadatokat tartalmazó dokumentum. A `b2c_1_sign_in` felhasználói folyamat metaadat- `fabrikamb2c.onmicrosoft.com` dokumentuma például a következő helyen található:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

A konfigurációs dokumentum egyik tulajdonsága az `jwks_uri`, hogy az azonos felhasználói folyamat értéke:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Ha meg szeretné határozni, hogy melyik felhasználói folyamatot használták egy azonosító jogkivonat aláírása (és a metaadatok lekérésének helyétől kezdve), két lehetőség közül választhat. Először is a felhasználói folyamat neve szerepel a `acr` jogcímben az azonosító jogkivonatban. A másik lehetőség a felhasználói folyamat kódolása a `state` paraméter értékében a kérelem kiadásakor, majd a kód dekódolásával meghatározhatja, hogy melyik felhasználói folyamatot használta. Bármelyik metódus érvényes.

Miután megszerezte a metaadat-dokumentumot az OpenID Connect metaadat-végpontból, az RSA 256 nyilvános kulcsokkal ellenőrizheti az azonosító token aláírását. A végponton több kulcs is szerepelhet, amelyek mindegyike egy `kid` jogcím alapján azonosítható. Az azonosító jogkivonat fejléce tartalmaz egy `kid` jogcímet is, amely azt jelzi, hogy a kulcsok közül melyeket használták az azonosító jogkivonat aláírására.

A tokenek Azure AD B2Cból való ellenőrzéséhez a kitevő (e) és a modulus (n) használatával kell létrehoznia a nyilvános kulcsot. Meg kell határoznia, hogy ennek megfelelően miként hajthatja végre ezt a megfelelő programozási nyelven. Az RSA protokollal a nyilvános kulcsok létrehozásáról szóló hivatalos dokumentáció itt található:https://tools.ietf.org/html/rfc3447#section-3.1

Az azonosító jogkivonat aláírásának ellenőrzése után több jogcím is van, amelyeket ellenőrizni kell. Ilyenek például a következők:

- Érvényesítse `nonce` a jogcímet a jogkivonat-újrapróbálkozások elleni támadások megelőzése érdekében. A bejelentkezési kérelemben megadott értéknek kell lennie.
- Érvényesítse `aud` a jogcímet, és győződjön meg arról, hogy az azonosító jogkivonat ki lett állítva az alkalmazáshoz. Az értéknek az alkalmazás alkalmazás-AZONOSÍTÓjának kell lennie.
- A `iat` és `exp` a jogcímek érvényesítésével győződjön meg arról, hogy az azonosító jogkivonat nem járt le.

Emellett még több érvényesítést is végre kell hajtania. Az érvényesítések részletes leírása az [OpenID Connect Core specifikációban](https://openid.net/specs/openid-connect-core-1_0.html)található. A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

- Annak biztosítása, hogy a felhasználó/szervezet regisztrálva legyen az alkalmazásra.
- Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel/jogosultságokkal.
- A hitelesítés bizonyos erősségének biztosítása, például az Azure Multi-Factor Authentication.

Az azonosító jogkivonat ellenőrzése után elindíthat egy munkamenetet a felhasználóval. Az azonosító jogkivonatban található jogcímek használatával adatokat szerezhet be az alkalmazásban lévő felhasználóról. Ehhez az információhoz a következők tartoznak: Display, Records és Authorization.

## <a name="get-a-token"></a>Jogkivonat beszerzése

Ha szüksége van a webalkalmazásra, hogy csak a felhasználói folyamatokat futtassa, kihagyhatja a következő néhány szakaszt. Ezek a csoportok csak olyan webalkalmazásokra vonatkoznak, amelyeknek hitelesített hívásokat kell végezniük a webes API-k számára, és a Azure AD B2C is védi őket.

Megadhatja a tokenhez a kívánt erőforráshoz beszerzett `response_type=code+id_token`engedélyezési kódot (a használatával), ha egy `POST` kérést küld a `/token` végpontnak. Azure AD B2C a szokásos módon [kérhet hozzáférési jogkivonatokat más API](access-tokens.md#request-a-token) -khoz a kérelemben szereplő hatókör (ek) megadásával.

Az alkalmazás saját háttérbeli webes API-hoz is kérhet hozzáférési tokent, ha az alkalmazás ügyfél-AZONOSÍTÓját használja a kért hatókörként (amely az ügyfél-AZONOSÍTÓhoz tartozó hozzáférési jogkivonatot eredményezi a "célközönség"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Bérlő | Igen | A Azure AD B2C bérlő neve |
| politika | Igen | Az engedélyezési kód beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. Adja hozzá ezt a paramétert a lekérdezési karakterlánchoz, nem pedig a POST szövegtörzshöz. |
| client_id | Igen | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója. |
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazással (nyilvános ügyfél) kapcsolatos forgatókönyvek esetén az ügyfél titkos kulcsai nem tárolhatók biztonságosan, a folyamaton nem használhatók threfore. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| code | Igen | A felhasználói folyamat elején beszerzett engedélyezési kód. |
| grant_type | Igen | A támogatás típusa, amelynek az engedélyezési kód `authorization_code` folyamatához kell tartoznia. |
| redirect_uri | Igen | Annak `redirect_uri` az alkalmazásnak a paramétere, amelyen az engedélyezési kódot megkapta. |
| scope | Nem | A hatókörök szóközzel tagolt listája. A `openid` hatókör jelzi a felhasználónak való bejelentkezéshez szükséges engedélyt, és id_token paraméterek formájában kéri le a felhasználó adatait. Használható a jogkivonatok az alkalmazás saját háttérbeli webes API-hoz való lekérésére, amelyet az ügyfél ugyanazzal az alkalmazás-AZONOSÍTÓval képvisel. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való kiterjesztett hozzáféréshez. |

A jogkivonat sikeres válasza A következőre hasonlít:

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
| not_before | Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type | A jogkivonat típusának értéke. `Bearer`az egyetlen támogatott típus. |
| access_token | A kért aláírt JWT-jogkivonat. |
| scope | Azok a hatókörök, amelyekhez a jogkivonat érvényes. |
| expires_in | A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| refresh_token | Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek segítségével hosszabb ideig is megtarthatja az erőforrásokhoz való hozzáférést. A hatókört `offline_access` az engedélyezési és a jogkivonat-kérelmekben is használni kell a frissítési jogkivonat fogadásához. |

A hibaüzenetek így néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | Kód, amely a bekövetkezett hibák típusának besorolására használható. |
| error_description | Egy üzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="use-the-token"></a>A jogkivonat használata

Most, hogy sikeresen beszerzett egy hozzáférési jogkivonatot, a jogkivonatot a háttérbeli webes API-khoz a kérésekben a `Authorization` fejlécbe belefoglalva is használhatja:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>A jogkivonat frissítése

Az azonosító tokenek rövid időn belül lejárnak. Az erőforrásokhoz való hozzáférés folytatásához frissítse a jogkivonatokat, miután lejárnak. A jogkivonatok frissítéséhez küldje el egy `POST` másik kérést `/token` a végpontnak. Ezúttal adja meg a `refresh_token` paramétert a `code` paraméter helyett:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Bérlő | Igen | A Azure AD B2C bérlő neve |
| politika | Igen | Az eredeti frissítési jogkivonat beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. Adja hozzá ezt a paramétert a lekérdezési karakterlánchoz, nem pedig a POST szövegtörzshöz. |
| client_id | Igen | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója. |
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazás (nyilvános ügyfél) forgatókönyvei esetében az ügyfél titkos kulcsai nem tárolhatók biztonságosan, a threfore nem használhatók. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| grant_type | Igen | A támogatás típusa, amelynek az engedélyezési kód ezen részéhez tartozó frissítési tokennek kell lennie. |
| refresh_token | Igen | A folyamat második részében beszerzett eredeti frissítési jogkivonat. A `offline_access` frissítési jogkivonat fogadásához a hatókört mind az engedélyezési, mind a jogkivonat-kérelemben kell használni. |
| redirect_uri | Nem | Annak `redirect_uri` az alkalmazásnak a paramétere, amelyen az engedélyezési kódot megkapta. |
| scope | Nem | A hatókörök szóközzel tagolt listája. A `openid` hatókör a felhasználónak való bejelentkezéshez és az azonosító tokenek formájában kapott információk beszerzéséhez szükséges engedélyt jelöli. A használatával jogkivonatokat küldhet az alkalmazás saját háttérbeli webes API-ra, amelyet a-ügyféllel megegyező alkalmazás-azonosító képvisel. A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való kiterjesztett hozzáféréshez. |

A jogkivonat sikeres válasza A következőre hasonlít:

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
| not_before | Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type | A jogkivonat típusának értéke. `Bearer`az egyetlen támogatott típus. |
| access_token | A kért aláírt JWT-jogkivonat. |
| scope | Az a hatókör, amelynek a jogkivonata érvényes. |
| expires_in | A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| refresh_token | Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek segítségével hosszabb ideig is megtarthatja az erőforrásokhoz való hozzáférést. |

A hibaüzenetek így néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | Kód, amely a bekövetkezett hibák típusának besorolására használható. |
| error_description | Egy üzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Ha ki szeretné írni a felhasználót az alkalmazásból, nem elég az alkalmazás cookie-jait törölni, vagy más módon leállítani a munkamenetet a felhasználóval. Átirányítja a felhasználót, hogy Azure AD B2C a kijelentkezéshez. Ha ezt nem teszi meg, előfordulhat, hogy a felhasználó újra hitelesíteni tudja az alkalmazását anélkül, hogy újra be kellene írnia a hitelesítő adatait.

A felhasználó kijelentkezéséhez irányítsa át a felhasználót a `end_session` korábban ismertetett OpenID Connect metadata dokumentumban felsorolt végpontra:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Paraméter | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Bérlő | Igen | A Azure AD B2C bérlő neve |
| politika | Igen | Az alkalmazásból a felhasználó aláírásához használni kívánt felhasználói folyamat. |
| id_token_hint| Nem | Egy korábban kiállított azonosító token, amelyet a kijelentkezési végpontnak továbbít a végfelhasználó aktuálisan hitelesített munkamenetével kapcsolatban az ügyféllel. Az `id_token_hint` biztosítja, hogy `post_logout_redirect_uri` a a regisztrált válasz URL-címe legyen a Azure ad B2C alkalmazás beállításaiban. |
| client_id | Nem | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója.<br><br>\**Erre akkor van szükség, `Application` ha elkülönítéses SSO-konfigurációt használ, és a kijelentkezési kérelemben _azonosító jogkivonat szükséges_ a következő értékre: `No`.* |
| post_logout_redirect_uri | Nem | Az URL-cím, amelyet a felhasználónak át kell irányítani a sikeres kijelentkezés után. Ha nem tartalmazza, a Azure AD B2C általános üzenetet jelenít meg a felhasználó számára. Ha nem ad meg `id_token_hint`, akkor ne regisztrálja az URL-címet válasz URL-címként a Azure ad B2C alkalmazás beállításaiban. |
| state | Nem | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, `state` hogy a kérelemben és a válaszban szereplő értékek azonosak-e. |

### <a name="secure-your-logout-redirect"></a>A kijelentkezési átirányítás biztonságossá tétele

A kijelentkezés után a rendszer átirányítja a felhasználót a `post_logout_redirect_uri` paraméterben megadott URI-ra, az alkalmazáshoz megadott válasz URL-címektől függetlenül. Ha azonban érvényes `id_token_hint` értéket ad meg, Azure ad B2C ellenőrzi, hogy az érték `post_logout_redirect_uri` megegyezik-e az alkalmazás által konfigurált átirányítási URI-k egyikével az átirányítás végrehajtása előtt. Ha nincs beállítva egyező válasz URL-cím az alkalmazáshoz, a rendszer hibaüzenetet jelenít meg, és a felhasználót nem irányítja át.

### <a name="external-identity-provider-sign-out"></a>Külső identitás-szolgáltató kijelentkezése

A felhasználó a `end_session` végpontra történő átirányítása törli a felhasználó egyszeri bejelentkezési állapotát Azure ad B2C, de nem írja alá a felhasználót a közösségi identitásszolgáltató-munkamenetből. Ha a felhasználó ugyanazt a IDENTITÁSSZOLGÁLTATÓ választja egy későbbi bejelentkezés során, a rendszer a hitelesítő adatok megadása nélkül újra hitelesíti őket. Ha a felhasználó ki szeretne jelentkezni az alkalmazásból, nem feltétlenül jelenti azt, hogy ki szeretné jelentkezni a Facebook-fiókjából. Ha azonban helyi fiókokat használ, a felhasználó munkamenete megfelelően végződik.
