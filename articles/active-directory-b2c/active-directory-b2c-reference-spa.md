---
title: Egyoldalas bejelentkezés implicit folyamat használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá egyoldalas bejelentkezést a OAuth 2,0 implicit folyamattal Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7d6a0a4e341158b37de73a74390d87a135d65f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947981"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Egyoldalas bejelentkezés a OAuth 2,0 implicit flow használatával Azure Active Directory B2C

Számos modern alkalmazás rendelkezik egy egyoldalas alkalmazás előtérrel, amely elsősorban a JavaScriptben van írva. Az alkalmazás gyakran egy olyan keretrendszer használatával íródott, mint a reakciós, a szögletes vagy a Vue. js. Az egylapos alkalmazások és más, elsősorban böngészőben futtatott JavaScript-alkalmazások további kihívásokkal rendelkeznek a hitelesítéshez:

- Ezeknek az alkalmazásoknak a biztonsági jellemzői eltérnek a hagyományos, kiszolgáló-alapú webalkalmazásokkal.
- Számos engedélyezési kiszolgáló és Identitáskezelő nem támogatja a különböző eredetű erőforrás-megosztási (CORS) kérelmeket.
- A teljes képernyős böngésző átirányítja az alkalmazásból a felhasználói élményhez.

Ezeknek az alkalmazásoknak a támogatásához a Azure Active Directory B2C (Azure AD B2C) a OAuth 2,0 implicit folyamatot használja. Az OAuth 2,0-hitelesítés implicit engedélyezési folyamatát a [OAuth 2,0 specifikáció 4,2. szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. Az implicit folyamat során az alkalmazás közvetlenül a Azure Active Directory (Azure AD) engedélyezi a végpontot, a kiszolgáló és a kiszolgáló közötti Exchange nélkül. Az összes hitelesítési logika és munkamenet-kezelés teljes egészében a JavaScript-ügyfélen történik, vagy egy oldal átirányítása vagy egy előugró ablak jelenik meg.

Azure AD B2C kiterjeszti a standard OAuth 2,0 implicit folyamatot több mint egyszerű hitelesítésre és engedélyezésre. Azure AD B2C bevezeti a [házirend paramétert](active-directory-b2c-reference-policies.md). A Policy paraméterrel a OAuth 2,0 használatával szabályzatokat adhat hozzá az alkalmazáshoz, például a regisztráláshoz, a bejelentkezéshez és a profilokhoz tartozó felhasználói folyamatokhoz. A jelen cikkben szereplő HTTP-kérelmekben a **{bérlő}. onmicrosoft. com** példaként van használatban. Cserélje le a `{tenant}`t a bérlő nevére, ha van ilyen, és létrehozta a felhasználói folyamatot is.

Az implicit bejelentkezési folyamat az alábbi ábrához hasonlóan néz ki. Az egyes lépéseket a cikk későbbi részében részletesen ismertetjük.

![Sáv stílusú diagram, amely az OpenID Connect implicit folyamatát mutatja](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Hitelesítési kérelmek küldése

Ha a webalkalmazásnak hitelesítenie kell a felhasználót, és futtatnia kell egy felhasználói folyamatot, a felhasználó átirányíthatja a felhasználót az `/authorize` végpontra. A felhasználó a felhasználói folyamattól függően végrehajtja a műveletet.

Ebben a kérelemben az ügyfél a `scope` paraméterben és a felhasználói folyamat futtatásához szükséges engedélyeket jelzi. Ha úgy érzi, hogy a kérés hogyan működik, próbálja meg beilleszteni a kérést egy böngészőben, és futtassa azt. Cserélje le a `{tenant}`t a Azure AD B2C bérlő nevére. Cserélje le a `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`t annak az alkalmazásnak az azonosítójával, amelyet korábban regisztrált a bérlőben. Cserélje le a `{policy}`t a bérlőben létrehozott szabályzat nevére, például `b2c_1_sign_in`.

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

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
|Bérlő| Igen | A Azure AD B2C bérlő neve|
|politika| Igen| A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in`, `b2c_1_sign_up`vagy `b2c_1_edit_profile`. |
| client_id | Igen | Az alkalmazáshoz hozzárendelt [Azure Portal](https://portal.azure.com/) alkalmazás azonosítója. |
| response_type | Igen | Tartalmaznia kell `id_token` az OpenID Connect bejelentkezéshez. A válasz típusa `token`is lehet. Ha `token`használ, az alkalmazás azonnal kap egy hozzáférési jogkivonatot az engedélyezési végponttól anélkül, hogy egy második kérést kellene benyújtani az engedélyezési végpontnak.  Ha a `token` válasz típusát használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely megadja, hogy melyik erőforrást kell kibocsátani a jogkivonat számára. |
| redirect_uri | Nem | Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolásnak kell lennie. |
| response_mode | Nem | Meghatározza az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez használandó módszert.  Az implicit folyamatok esetében használja a `fragment`. |
| scope | Igen | A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. A `openid` hatókör a felhasználónak való bejelentkezéshez és az azonosító tokenek formájában a felhasználó adatainak lekéréséhez szükséges engedélyt jelöli. A `offline_access` hatóköre nem kötelező a Web Apps esetében. Azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez. |
| state | Nem | A kérelemben szereplő, a jogkivonat-válaszban is visszaadott érték. A használni kívánt tartalom karakterlánca lehet. A rendszer általában véletlenszerűen generált, egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisítás elleni támadásait. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például a laphoz. |
| egyszeri | Igen | A kérelemben szereplő, az eredményül kapott azonosító jogkivonatban található (az alkalmazás által generált) érték. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. |
| gyors | Nem | A kötelező felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes érték `login`. Ez a paraméter arra kényszeríti a felhasználót, hogy adja meg a kéréshez tartozó hitelesítő adatait. Az egyszeri bejelentkezés nem lép érvénybe. |

Ekkor a rendszer megkéri a felhasználót, hogy fejezze be a szabályzat munkafolyamatát. Előfordulhat, hogy a felhasználónak meg kell adnia felhasználónevét és jelszavát, be kell jelentkeznie egy közösségi identitással, regisztrálnia kell a címtárban, vagy bármely más lépéssel. A felhasználói műveletek attól függnek, hogy a felhasználói folyamat hogyan van definiálva.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD egy választ küld az alkalmazásnak a `redirect_uri`használt értékre. A `response_mode` paraméterben megadott metódust használja. A válasz pontosan ugyanaz, mint a felhasználói műveletek egyes forgatókönyvei esetében, a végrehajtott felhasználói folyamattól függetlenül.

### <a name="successful-response"></a>Sikeres válasz
`response_mode=fragment` és `response_type=id_token+token`t használó sikeres válasz a következőhöz hasonlóan néz ki, és az olvashatóság érdekében sortöréseket használ:

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
| token_type | A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| expires_in | A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| scope | Azok a hatókörök, amelyekhez a jogkivonat érvényes. Hatóköröket is használhat a tokenek későbbi használatra való gyorsítótárazásához. |
| id_token | Az alkalmazás által kért azonosító jogkivonat. Az azonosító token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. Az azonosító jogkivonatokkal és azok tartalmával kapcsolatos további információkért tekintse meg az [Azure ad B2C jogkivonat-referenciát](active-directory-b2c-reference-tokens.md). |
| state | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e. |

### <a name="error-response"></a>Hiba válasza
A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --------- | ----------- |
| error | A felmerülő hibák típusának besorolására szolgáló kód. |
| error_description | Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |
| state | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e.|

## <a name="validate-the-id-token"></a>AZONOSÍTÓ jogkivonat ellenőrzése

Az azonosító jogkivonat fogadása nem elegendő a felhasználó hitelesítéséhez. Érvényesítse az azonosító jogkivonat aláírását, és ellenőrizze a jogcímek jogcímeit az alkalmazás követelményei szerint. Azure AD B2C [JSON webes tokeneket (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú titkosítást használ a tokenek aláírásához, és ellenőrizze, hogy érvényesek-e.

Számos nyílt forráskódú kódtár elérhető a használni kívánt nyelvtől függően a JWTs érvényesítéséhez. A saját érvényesítési logikájának megvalósítása helyett érdemes lehet kideríteni a rendelkezésre álló nyílt forráskódú kódtárakat. A cikkben található információk segítségével megismerheti, hogyan használhatja ezeket a kódtárakat.

Azure AD B2C OpenID Connect metaadat-végponttal rendelkezik. Egy alkalmazás a végpont használatával adatokat tud beolvasni a Azure AD B2Cról. Ezek az adatok a végpontokat, a jogkivonat tartalmát és a jogkivonat-aláíró kulcsokat tartalmazzák. A Azure AD B2C-bérlő minden egyes felhasználói folyamatához JSON-metaadatokat tartalmazó dokumentum található. A fabrikamb2c.onmicrosoft.com-bérlő b2c_1_sign_in felhasználói folyamatához tartozó metaadat-dokumentum például a következő helyen található:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

A konfigurációs dokumentum egyik tulajdonsága a `jwks_uri`. Az azonos felhasználói folyamat értéke a következő lesz:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Ha meg szeretné határozni, hogy melyik felhasználói folyamatot használták egy azonosító jogkivonat aláírására (és honnan szeretné beolvasni a metaadatokat), két lehetőség közül választhat. Először is a felhasználói folyamat neve szerepel a `id_token``acr` jogcímben. További információ a jogcímek azonosító jogkivonat alapján történő elemzéséről: [Azure ad B2C jogkivonat-hivatkozás](active-directory-b2c-reference-tokens.md). A másik lehetőség a felhasználói folyamat kódolása a `state` paraméter értékében a kérelem kiadásakor. Ezután dekódolja a `state` paramétert annak meghatározásához, hogy melyik felhasználói folyamatot használták. Bármelyik metódus érvényes.

Miután megszerezte a metaadat-dokumentumot az OpenID Connect metaadat-végpontból, használhatja az RSA-256 nyilvános kulcsokat (ebben a végpontban) az azonosító jogkivonat aláírásának ellenőrzéséhez. Előfordulhat, hogy a végponton több kulcs van felsorolva, amelyeket a `kid`azonosít. A `id_token` fejléce `kid` jogcímet is tartalmaz. Azt jelzi, hogy a kulcsok közül melyeket használták az azonosító token aláírására. További információért, beleértve a [jogkivonatok érvényesítésével](active-directory-b2c-reference-tokens.md)kapcsolatos ismereteket, tekintse meg a [Azure ad B2C jogkivonat-referenciát](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Az azonosító jogkivonat aláírásának ellenőrzése után számos jogcím ellenőrzésre szorul. Példa:

* Érvényesítse a `nonce` jogcímet a jogkivonat-Visszajátszási támadások megelőzése érdekében. A bejelentkezési kérelemben megadott értéknek kell lennie.
* Érvényesítse a `aud` jogcímet, és győződjön meg arról, hogy az azonosító jogkivonat ki lett állítva az alkalmazáshoz. Az értéknek az alkalmazás alkalmazásspecifikus AZONOSÍTÓjának kell lennie.
* Ellenőrizze a `iat` és `exp` jogcímeket, hogy az azonosító jogkivonat nem járt le.

Az [OpenID Connect Core specifikáció](https://openid.net/specs/openid-connect-core-1_0.html)részletesen ismerteti a végrehajtandó több érvényesítést. A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Győződjön meg arról, hogy a felhasználó vagy szervezet regisztrált az alkalmazásra.
* Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel és jogosultságokkal.
* A hitelesítés bizonyos erősségének biztosítása, például az Azure Multi-Factor Authentication használatával.

Az azonosító jogkivonatok jogcímeivel kapcsolatos további információkért tekintse meg az [Azure ad B2C jogkivonat-referenciát](active-directory-b2c-reference-tokens.md).

Az azonosító jogkivonat ellenőrzése után megkezdheti a munkamenetet a felhasználóval. Az alkalmazásban az azonosító jogkivonatban található jogcímek segítségével szerezzen be információkat a felhasználóról. Ezek az információk megjeleníthetők, rekordok, engedélyezés és így tovább használhatók.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beolvasása
Ha az egyetlen dolog, amit a webalkalmazásoknak végre kell hajtania, akkor kihagyhatja a következő néhány szakaszt. A következő részekben található információk csak azokra a webalkalmazásokra érvényesek, amelyeknek hitelesített hívásokat kell végezniük a webes API-k számára, és amelyeket a Azure AD B2C véd.

Most, hogy aláírta a felhasználót egy egyoldalas alkalmazásba, hozzáférési jogkivonatokat kérhet le az Azure AD által védett webes API-k meghívásához. Akkor is használhatja ezt a módszert, ha a `token` válasz típusának használatával már kapott jogkivonatot, és nem irányítja át a felhasználót az ismételt bejelentkezéshez.

Egy tipikus webalkalmazás-folyamat esetében kérelmet kell benyújtani a `/token` végpontnak. A végpont azonban nem támogatja a CORS-kérelmeket, így az AJAX-hívások frissítési token beszerzése nem lehetséges. Ehelyett a rejtett HTML iframe-elemek implicit folyamatával új jogkivonatokat kérhet le más webes API-khoz. Íme egy példa, amely sortöréseket mutat be az olvashatóság érdekében:

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
|Bérlő| Szükséges | A Azure AD B2C bérlő neve|
politika| Szükséges| A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in`, `b2c_1_sign_up`vagy `b2c_1_edit_profile`. |
| client_id |Szükséges |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| response_type |Szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezéshez.  A válasz típusa `token`is lehet. Ha itt `token` használ, az alkalmazás azonnal kap egy hozzáférési jogkivonatot az engedélyezési végponttól anélkül, hogy egy második kérést kellene benyújtani az engedélyezési végpontnak. Ha a `token` válasz típusát használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely megadja, hogy melyik erőforrást kell kibocsátani a jogkivonat számára. |
| redirect_uri |Ajánlott |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan egyeznie kell a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolású. |
| scope |Szükséges |A hatókörök szóközzel tagolt listája.  A jogkivonatok beolvasásához adja meg a kívánt erőforráshoz szükséges összes hatókört. |
| response_mode |Ajánlott |Meghatározza az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez használt módszert. Az implicit folyamathoz használja a `fragment`. Két másik mód is megadható, `query` és `form_post`, de az implicit folyamat nem működik. |
| state |Ajánlott |A jogkivonat-válaszban visszaadott kérelemben szereplő érték.  A használni kívánt tartalom karakterlánca lehet.  A rendszer általában véletlenszerűen generált, egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisítás elleni támadásait.  Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna. Például a lap vagy a felhasználó megtekinthető. |
| egyszeri |Szükséges |Az alkalmazás által generált kérelemben szereplő érték, amely az eredményül kapott azonosító jogkivonat jogcímként szerepel.  Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely azonosítja a kérelem forrását. |
| gyors |Szükséges |A rejtett iframe-ben lévő jogkivonatok frissítéséhez és lekéréséhez használja a `prompt=none` annak biztosítására, hogy az IFRAME ne legyen beragadva a bejelentkezési oldalon, és azonnal visszaadja. |
| login_hint |Szükséges |A rejtett iframe-ben lévő tokenek frissítéséhez és lekéréséhez adja meg a felhasználó felhasználónevét, hogy megkülönböztesse az adott időpontban a felhasználó által esetlegesen használt munkameneteket. A felhasználónevet kinyerheti egy korábbi bejelentkezésből az `preferred_username` jogcím használatával (az `profile` hatókör szükséges az `preferred_username` jogcím megszerzéséhez). |
| domain_hint |Szükséges |A következők egyike lehet: `consumers` vagy `organizations`.  A rejtett iframe-ben lévő tokenek frissítéséhez és lekéréséhez adja meg a `domain_hint` értéket a kérelemben.  Kinyeri a `tid` jogcímet egy korábbi bejelentkezés azonosító jogkivonatával annak meghatározásához, hogy melyik értéket kell használni (az `profile` hatókör szükséges az `tid` jogcím fogadásához). Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használja a `domain_hint=consumers`.  Ellenkező esetben használja a `domain_hint=organizations`. |

Ha a `prompt=none` paramétert állítja be, akkor ez a kérelem sikeres vagy sikertelen lesz, és visszatér az alkalmazáshoz.  A rendszer sikeres választ küldött az alkalmazásnak a jelzett átirányítási URI-n, a `response_mode` paraméterben megadott metódus használatával.

### <a name="successful-response"></a>Sikeres válasz
A `response_mode=fragment` használatával történő sikeres válasz a következő példához hasonlóan néz ki:

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
| token_type |A jogkivonat típusa mindig a tulajdonos lesz. |
| state |Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e. |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| scope |Azok a hatókörök, amelyekre érvényes a hozzáférési jogkivonat. |

### <a name="error-response"></a>Hiba válasza
A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket.  `prompt=none`esetén a várt hiba a következő példához hasonlít:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibát az IFRAME-kérelemben kapja, a felhasználónak interaktív módon be kell jelentkeznie egy új jogkivonat lekéréséhez.

## <a name="refresh-tokens"></a>Tokenek frissítése
Az azonosító tokenek és a hozzáférési tokenek rövid idő után lejárnak. Az alkalmazásnak fel kell készülnie, hogy rendszeresen frissítse ezeket a jogkivonatokat.  A jogkivonat bármelyik típusának frissítéséhez végezze el ugyanazt a rejtett iframe-kérelmet, amelyet egy korábbi példában használunk a `prompt=none` paraméterrel az Azure AD-lépések vezérléséhez.  Ha új `id_token` értéket szeretne kapni, ügyeljen arra, hogy `response_type=id_token` és `scope=openid`, valamint egy `nonce` paramétert használjon.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése
Ha alá szeretné írni a felhasználót az alkalmazásból, a kijelentkezéshez irányítsa át a felhasználót az Azure AD-ba. Ha nem irányítja át a felhasználót, előfordulhat, hogy újra kell hitelesítenie az alkalmazást anélkül, hogy újra be kellene írnia a hitelesítő adatait, mert az Azure AD-vel érvényes egyszeri bejelentkezéses munkamenet van.

Egyszerűen átirányíthatja a felhasználót a `end_session_endpoint`, amely ugyanabban az OpenID Connect metaadat-dokumentumban szerepel, amely az [azonosító jogkivonat ellenőrzése](#validate-the-id-token)című cikkben található. Példa:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Bérlő | Igen | A Azure AD B2C bérlő neve |
| politika | Igen | Az alkalmazásból a felhasználó aláírásához használni kívánt felhasználói folyamat. |
| post_logout_redirect_uri | Nem | Az URL-cím, amelyet a felhasználónak át kell irányítani a sikeres kijelentkezés után. Ha nem tartalmazza, a Azure AD B2C általános üzenetet jelenít meg a felhasználó számára. |
| state | Nem | Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e. |


> [!NOTE]
> A felhasználó átirányítása a `end_session_endpoint` törli az egyes felhasználók egyszeri bejelentkezési állapotát Azure AD B2C használatával. Azonban nem írja alá a felhasználót a felhasználó közösségi identitás-szolgáltatói munkamenetében. Ha a felhasználó ugyanazt az identitás-szolgáltatót választja egy későbbi bejelentkezéskor, a rendszer újra hitelesíti a felhasználót a hitelesítő adatok megadása nélkül. Ha a felhasználó ki szeretne jelentkezni a Azure AD B2C alkalmazásból, nem feltétlenül jelenti azt, hogy teljesen ki szeretné jelentkezni a Facebook-fiókjából, például:. Helyi fiókok esetében azonban a felhasználó munkamenete megfelelően fog megjelenni.
>

## <a name="next-steps"></a>Következő lépések

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Mintakód: Hello. js Azure AD B2C

[Egy egyoldalas alkalmazás, amely a Hello. js-re épül Azure ad B2C][github-hello-js-example] (GitHub)

A GitHubon ez a példa arra szolgál, hogy segítséget nyújtson Azure AD B2C a [Hello. js][github-hello-js] -re épülő egyszerű webalkalmazásban és az előugró stílusú hitelesítésben.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
