---
title: Implicit folyamat használata az Azure Active Directory B2C egyoldalas alkalmazások |} A Microsoft Docs
description: Megtudhatja, hogyan segítségével közvetlenül OAuth 2.0 implicit folyamat az Azure Active Directory B2C egyoldalas alkalmazások készítéséhez.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee341fd3b54d748849da34cd11db30e5ea758fb1
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445268"
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Az Azure AD B2C: Egyoldalas alkalmazás jelentkezzen be az OAuth 2.0 implicit folyamat

Számos modern alkalmazás egylapos alkalmazás előtérrendszerét elsősorban javascriptben írt rendelkezik. Gyakran előfordul az alkalmazás-keretrendszert, például az AngularJS, az Ember.js vagy a Durandal használatával írt. Egyoldalas alkalmazások és más elsősorban a böngészőben futó JavaScript-alapú alkalmazások rendelkeznek a hitelesítés további áttekinthet néhány problémát:

* Az ezeknek az alkalmazásoknak biztonsági jellemzőkkel jelentősen eltérnek a hagyományos server-alapú webes alkalmazások.
* Számos engedélyezési kiszolgálók és identitás-szolgáltatóktól nem támogatja az eltérő eredetű erőforrások megosztása (CORS) kéréseket.
* Lehet, hogy oldalas böngésző átirányítja a forrásadatok az alkalmazás jelentősen invazív, hogy a felhasználói élmény.

Ezek az alkalmazások Azure Active Directory B2C-t támogató (az Azure AD B2C-vel) az OAuth 2.0 implicit folyamat használja. Az OAuth 2.0 típusú implicit engedélyezés folyamat leírt [szakaszban, az OAuth 2.0 ismertetőjének 4.2](http://tools.ietf.org/html/rfc6749). Az implicit folyamatot, az alkalmazás fogad jogkivonatok közvetlenül az Azure Active Directory (Azure AD) authorize végponton, bármely az exchange server-kiszolgáló nélkül. Hitelesítési logikát, és a munkamenet idő szükséges kezelése teljes mértékben a JavaScript-ügyfelet, anélkül, hogy további oldal átirányítja az helyezze el.

Az Azure AD B2C kiterjeszti a szabványos OAuth 2.0 implicit folyamat több, mint az egyszerű hitelesítés és engedélyezés. Az Azure AD B2C bevezeti a [szabályzatparaméter](active-directory-b2c-reference-policies.md). A házirend-paraméterrel, az OAuth 2.0 használatával felhasználói élményeket adhat hozzá alkalmazásához, például a regisztrációs, bejelentkezési és profilok kezelése. Ebben a cikkben bemutatjuk, ezek a tapasztalatok mindegyike az egyoldalas alkalmazások megvalósításához az implicit folyamat és az Azure AD használatával. Segít megismerkedni, hogy vessen egy pillantást a [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) és [a Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) mintákat.

Az ebben a cikkben példa HTTP-kérések, használjuk a mintául szolgáló Azure AD B2C-címtár **fabrikamb2c.onmicrosoft.com**. Is használhatja a saját mintaalkalmazás és a szabályzatok. Próbálja meg a kérések saját maga az ezeket az értékeket, vagy is le kell cserélni a saját értékeire.
Ismerje meg, hogyan [első saját Azure AD B2C directory, az alkalmazás és a szabályzatok](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Protokoll diagramja

Az implicit bejelentkezési folyamat a következőhöz hasonló az alábbi ábra lesz. Az egyes lépések a cikk későbbi részében részletesebben ismertetjük.

![OpenID Connect sávok](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Hitelesítési kérelem küldése
Ha a webalkalmazás kell hitelesíteni a felhasználót, és hajtsa végre egy házirendet, irányítja a felhasználót, hogy a `/authorize` végpont. Ez az interaktív részének a folyamatot, ahol a felhasználó által készített művelet függően a szabályzatot. A felhasználó beolvasása az Azure AD-végpont jogkivonat-azonosító.

A kéréshez, az ügyfél azt jelzi, hogy az a `scope` paraméter szerzi be a felhasználó szükséges engedélyeket. Az a `p` paraméter azt jelzi, hajtsa végre a szabályzatot. Az alábbi három példák (a sortörések az olvashatóság érdekében) minden egyes egy másik szabályzat használja. Betekintést nyerhet az egyes kérések működését, próbálja meg beilleszteni a kérelem egy böngészőbe, és azt futtatja.

### <a name="use-a-sign-in-policy"></a>Bejelentkezés szabályzattal
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Előfizetési szabályzat
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Profil szerkesztése szabályzattal
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az Alkalmazásazonosítót az alkalmazáshoz a hozzárendelt a [az Azure portal](https://portal.azure.com). |
| response_type |Szükséges |Tartalmaznia kell `id_token` OpenID Connect bejelentkezhet. Válasz típusa is tartalmazhat `token`. Ha `token`, az alkalmazás azonnal fogadhatnak egy hozzáférési jogkivonatot a hitelesítési végpontra, anélkül, hogy a második kérés a hitelesítési végpontra.  Ha használja a `token` válasz típusát, a `scope` paraméternek tartalmaznia kell egy hatókör, amely azt jelzi, hogy melyik erőforrást kell kiállítani token. |
| redirect_uri |Ajánlott |Az átirányítási URI-ját az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| response_mode |Ajánlott |Meghatározza a küldése az alkalmazásnak vissza a létrejövő jogkivonat segítségével módszert.  Implicit folyamat használata `fragment`. |
| scope |Szükséges |Hatókörök szóközzel elválasztott listáját. Hatókör érték azt jelzi, az Azure AD mindkét engedélyeket, hogy a kérés érkezik. A `openid` hatókör azt jelzi, hogy jelentkezzen be a felhasználó és azonosító-jogkivonatokat formájában a felhasználóval kapcsolatos adatok beolvasása az engedélyt. (Tárgyaljuk Ez a cikk több későbbi részében.) A `offline_access` hatókör megadása nem kötelező web apps számára. Azt jelzi, hogy kell-e az alkalmazás egy frissítési jogkivonat hosszú élettartamú erőforrások eléréséhez. |
| state |Ajánlott |A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat, amelyet használni szeretne, karakterlánc lehet. Általában egy véletlenszerűen létrehozott, egyedi érték szolgál, webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében. Az állapot is szolgál a felhasználói állapot az alkalmazás információ kódolás előtt a hitelesítési kérelmet, a lap, amelyen korábban volt. |
| egyszeri |Szükséges |A kérésben (az alkalmazás által generált), amely az eredményül kapott azonosító jogkivonat jogcímként szerepel érték. Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Általában értéke véletlenszerű, egyedi karakterlánc, amely a kérés eredetének azonosítására használhatók. |
| p |Szükséges |A szabályzat végrehajtásához. Egy szabályzatot, amely jön létre az Azure AD B2C-bérlő nevét. A szabályzat neve értéket érdemes kezdenie **b2c\_1\_**. További információkért lásd: [beépített szabályzatok Azure AD B2C-vel](active-directory-b2c-reference-policies.md). |
| parancssor |Optional |A felhasználói beavatkozás szükséges típusa. Jelenleg az egyetlen érvényes érték el `login`. Ez kényszeríti a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználó kéri a házirend-munkafolyamat végrehajtásához. Ez a felhasználó megadja a felhasználónevüket és jelszavukat, bejelentkezés egy közösségi identitás való regisztráláskor a címtárban, vagy bármely más több lépést is járhat. Felhasználói műveletek attól függ, hogyan van definiálva a szabályzatot.

Miután a felhasználó elvégzi a szabályzatot, az Azure AD választ az értéket az alkalmazáshoz használt `redirect_uri`. A megadott metódust használ a `response_mode` paraméter. A válasz a pontosan megegyezik az egyes felhasználói művelet forgatókönyv, függetlenül a szabályzat, amely végre lett hajtva.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz által használt `response_mode=fragment` és `response_type=id_token+token` tűnik, a sortörések az olvashatóság érdekében a következő:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Paraméter | Leírás |
| --- | --- |
| access_token |A hozzáférési jogkivonatot, amely az alkalmazás kéri.  A hozzáférési jogkivonat nem szabad dekódolni vagy más módon megvizsgálni. Azt is kell kezelni olyan átlátszatlan karakterlánc. |
| token_type |A token típusú értékké. Az egyetlen, amely támogatja az Azure ad-ben típus tulajdonosi. |
| expires_in |Az, hogy mennyi idő a hozzáférési jogkivonat érvénytelen (másodpercben). |
| scope |A hatókörök, amely a token érvényes. Emellett használhatja szerepet, amelyet a gyorsítótár-jogkivonatok későbbi használatra. |
| id_token |Az azonosító jogkivonat, amely az alkalmazás kéri. Az azonosító jogkivonat segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. Azonosító-jogkivonatokat, és azok tartalmát kapcsolatos további információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |
| state |Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is lehet küldeni az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát osztályozva a felmerülő hibákat. A hiba kódja munkamenethibák kezelésére vonatkozó is használhatja. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |
| state |Lásd az előző táblázatban a teljes leírását. Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek.|

## <a name="validate-the-id-token"></a>Az azonosító jogkivonat érvényesítése
Fogadó-azonosító jogkivonat már nem elég a felhasználó hitelesítéséhez. Az azonosító jogkivonat-aláírás ellenőrzése, és ellenőrizze a jogcímeket az alkalmazáskövetelmények szerint. Használja az Azure AD B2C [JSON webes jogkivonatainak (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú hitelesítésen jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek.

Számos nyílt forráskódú kódtár szeretné használni a nyelvtől függően JWTs, ellenőrzésével kapcsolatos érhetők el. Fontolja meg a saját ellenőrzési logika megvalósítása helyett használható nyílt forráskódú könyvtáraink feltárása. Információt is használhatja az ebben a cikkben megtudhatja, hogyan megfelelően használni az ezen kódtárak segítségével.

Az Azure AD B2C az OpenID Connect metaadatok végpontja van. Egy alkalmazás használhatja a végpont információ az Azure AD B2C-t a futásidőben beolvassa. Ezen információk közé tartozik a végpontokat, a jogkivonat tartalma és a jogkivonat-aláíró kulcsok. Nincs a az Azure AD B2C-bérlő minden egyes házirendjének metaadatok JSON-dokumentumok. Ha például a metaadat-dokumentum fabrikamb2c.onmicrosoft.com bérlőben b2c_1_sign_in házirend következő helyen található:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

A konfigurációs dokumentum tulajdonságainak egyike a `jwks_uri`. Ugyanaz a szabályzat értéke a következő lesz:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Határozza meg, mely a házirend-azonosító jogkivonat aláírásához használt (és beolvassa a metaadatokat a helyét), a két lehetősége van. Először a szabályzat neve szerepel a `acr` a jogcím `id_token`. A jogcímeket egy azonosító jogkivonat elemzése kapcsolatos információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). A másik lehetőség egy kódolása a szabályzatot az értékét a `state` paramétert a kérés elküldésekor. Ezt követően dekódolni a `state` paraméter határozza meg, mely házirend lett megadva. Mindkét módszer esetén érvényes.

A metaadat-dokumentum, az OpenID Connect metaadat-végpontról, melyeket beszerezett, miután a érvényesíteni az aláírást a azonosító jogkivonat használhatja az RSA-256 nyilvános kulcsot (Ez a végpont található). Előfordulhat, hogy egy adott időpontban a végponti szereplő több kulcsok, minden egyes által azonosított egy `kid`. Fejlécében `id_token` is tartalmaz egy `kid` jogcím. Azt jelzi, amely ezeket a kulcsokat a azonosító jogkivonat aláírásához használt. További információk, megismerését [jogkivonatok érvényességének](active-directory-b2c-reference-tokens.md#token-validation), tekintse meg a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Az azonosító jogkivonat aláírása, ellenőrzése után több jogcím követelhet meg. Példa:

* Ellenőrizze a `nonce` jogcím ismétlésének támadások megelőzése érdekében. Az értéknek kell lennie, a bejelentkezési kérésben megadott.
* Ellenőrizze a `aud` győződjön meg arról, hogy az azonosító jogkivonat az alkalmazáshoz kiállított jogcímet. Az értéknek kell lennie a az alkalmazás azonosítója.
* Ellenőrizze a `iat` és `exp` jogcímeket kell győződjön meg arról, hogy az azonosító jogkivonat nem járt le.

Végre kell hajtania néhány további ellenőrzések a részletes leírását a [OpenID Connect Core specifikációja](http://openid.net/specs/openid-connect-core-1_0.html). Érdemes azt is, további jogcímek a forgatókönyvtől függően ellenőrzése. Néhány gyakori ellenőrzések a következők:

* Annak ellenőrzése, hogy a felhasználó vagy a szervezet regisztrált az alkalmazáshoz.
* Annak ellenőrzése, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési és a jogosultságokat.
* Annak ellenőrzése, hogy egy bizonyos hitelesítés erőssége történt, például ahogy az az Azure multi-factor Authentication által.

Egy azonosító jogkivonat jogcímeiben kapcsolatos további információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

Teljesen érvényesítése az azonosító jogkivonat után elkezdheti a munkamenet a felhasználóval. Az alkalmazásba, a jogcímek használata az azonosító jogkivonat beszerzése a felhasználói információt. Ez az információ használható megjelenített, rekordokat, engedélyezési és így tovább.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonat beszerzése
Ha egyedül a web apps szüksége van, akkor hajtsa végre a házirendek, továbbléphet a következő néhány szakaszban. Az alábbi szakaszokban található információk csak hitelesített hívásokat indíthat egy webes API-nak kell, és amely az Azure AD B2C által védett webalkalmazások érvényesek.

Most, hogy a bejelentkezett felhasználó az egyoldalas alkalmazás, kérheti le jogkivonatot hívó webes API-k az Azure AD által védett. Akkor is, ha a jogkivonat használatával már megkapta a `token` válasz típusát, ez a módszer használatával szerzi be a további erőforrások jogkivonatokat nélkül átirányítás, a felhasználót, hogy jelentkezzen be újra.

Egy tipikus webes alkalmazás folyamatban, ehhez a kérelem a `/token` végpont.  A végpont azonban nem támogatja CORS-kérések, és frissítési jogkivonatok AJAX-hívások végrehajtása ne legyen lehetőség. A rejtett iframe HTML-elemmel az implicit folyamatot használhatja inkább más webes API-k új tokenekhez. Íme egy példa, a sortörések az olvashatóság érdekében:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az Alkalmazásazonosítót az alkalmazáshoz a hozzárendelt a [az Azure portal](https://portal.azure.com). |
| response_type |Szükséges |Tartalmaznia kell `id_token` OpenID Connect bejelentkezhet.  Válasz típusa emellett tartalmazhat `token`. Ha `token` itt, az alkalmazás azonnal fogadhatnak egy hozzáférési jogkivonatot a hitelesítési végpontra, anélkül, hogy a második kérés a hitelesítési végpontra. Ha használja a `token` válasz típusát, a `scope` paraméternek tartalmaznia kell egy hatókör, amely azt jelzi, hogy melyik erőforrást kell kiállítani token. |
| redirect_uri |Ajánlott |Az átirányítási URI-ját az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Ez pontosan egyeznie kell az átirányítási URI-k a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| scope |Szükséges |Hatókörök szóközzel elválasztott listáját.  Jogkivonatok lekérésének lépéseiről, tartalmazzák, amelyekre szüksége van az importálni kívánt erőforrás minden hatókör. |
| response_mode |Ajánlott |Megadja a módszert, amellyel a létrejövő jogkivonat vissza küldése az alkalmazásnak.  Lehet `query`, `form_post`, vagy `fragment`. |
| state |Ajánlott |A kérésben a token válaszban visszaadott érték.  Bármilyen tartalmat, amelyet használni szeretne, karakterlánc lehet.  Általában egy véletlenszerűen létrehozott, egyedi érték szolgál, webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében.  Az állapot is szolgál az alkalmazás a felhasználói állapot információt kódolása előtt a hitelesítési kérelmet. Például a lapot vagy nézet a felhasználó nem található. |
| egyszeri |Szükséges |A kérésben, az eredményül kapott azonosító jogkivonat jogcímként megtalálható az alkalmazás által generált érték.  Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Általában érték, amely azonosítja a kérés eredetének véletlenszerű, egyedi karakterlánc. |
| parancssor |Szükséges |Frissítés és a egy rejtett IFrame-keretben tokenekhez használja `prompt=none` biztosíthatja, hogy az iframe nem elakadnak a bejelentkezési oldalon, és azonnal visszatér. |
| login_hint |Szükséges |Frissülnek, és a egy rejtett IFrame-keretben tokenekhez, vegye fel annak a felhasználónak a felhasználóneve több munkamenet a felhasználó egy adott időben lehet megkülönböztetni a mutatót. Kibonthatja a felhasználónevet, egy korábbi bejelentkezési használatával a `preferred_username` jogcím. |
| domain_hint |Szükséges |A következők egyike lehet: `consumers` vagy `organizations`.  Frissítése és a egy rejtett IFrame-keretben jogkivonatok lekérésének lépéseiről, esetében meg kell adni a `domain_hint` értékével a kérés.  Bontsa ki a `tid` jogcím egy korábbi bejelentkezési azonosító jogkivonata melyik használandó érték meghatározásához.  Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`.  Ellenkező esetben használjon `domain_hint=organizations`. |

Beállításával a `prompt=none` paramétert, a kérelem vagy sikeres vagy azonnal meghiúsul, és visszaadja az alkalmazásnak.  Sikeres válasz érkezik az alkalmazást a jelzett átirányítási URI-t, a megadott módszer használatával a `response_mode` paraméter.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz `response_mode=fragment` kell kinéznie:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Paraméter | Leírás |
| --- | --- |
| access_token |A jogkivonat, amely az alkalmazás kéri. |
| token_type |A jogkivonat típusa mindig lesz tulajdonosi. |
| state |Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| scope |A hatókörök, amely a hozzáférési jogkivonat érvényes. |

### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is lehet küldeni az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően.  A `prompt=none`, várt hibával néz ki:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható. A karakterlánc reagálni hibákat is használja. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |

Ez a hiba az iframe-kérelmet kap, ha a felhasználó interaktív jelentkezzen be újra egy új token lekérése. Ez úgy, hogy megfelel az alkalmazás képes kezelni.

## <a name="refresh-tokens"></a>Frissítési jogkivonatok
Azonosító-jogkivonatokat, és mindkettő egy rövid idő után jár le jogkivonatot. Az alkalmazás ezek a jogkivonatok rendszeresen frissíteni kell készíteni.  Mindkét típusú jogkivonat frissítéséhez hajtsa végre a rejtett iframe kérésben használtuk a korábbi példában használatával a `prompt=none` paraméter segítségével szabályozhatja az Azure AD-lépéseket.  Megjelenik egy új `id_token` értékét, ügyeljen arra, hogy `response_type=id_token` és `scope=openid`, és a egy `nonce` paraméter.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha szeretne bejelentkezni, a felhasználó ki az alkalmazásból, jelentkezzen ki, hogy az Azure AD átirányítja a felhasználót. Ha nem így tesz, a felhasználó lehet az alkalmazásba a hitelesítő adatok újbóli megadása nélkül hitelesítse magát újra. Ennek az oka egy érvényes egyszeri bejelentkezés munkamenetet az Azure ad-vel rendelkeznek.

A felhasználót, hogy egyszerűen átirányíthatja a `end_session_endpoint` , hogy szerepel-e az azonos OpenID Connect metaadat-dokumentumban ismertetett [az azonosító jogkivonat érvényesítése](#validate-the-id-token). Példa:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| p |Szükséges |A szabályzat használatával jelentkezzen ki az alkalmazás a felhasználó. |
| post_logout_redirect_uri |Ajánlott |Az URL-cím, amely a felhasználó után a rendszer átirányítja a sikeres kijelentkezést. Ha ez a lehetőség nem része, Azure AD B2C egy általános üzenet megjelenítése a felhasználónak. |

> [!NOTE]
> Irányítja a felhasználót, hogy a `end_session_endpoint` törli a felhasználó egyszeri bejelentkezés állapotát az Azure AD B2C-vel néhányat. Azt azonban nem jelentkezzen ki a felhasználó közösségi szolgáltató munkamenet a felhasználó. Ha a felhasználó szolgáltató azonos azonosítása egy későbbi bejelentkezés során, a felhasználók újrahitelesítése, a hitelesítő adatok megadása nélkül. Ha a felhasználó használni szeretne jelentkezzen ki az Azure AD B2C-alkalmazást, azt nem feltétlenül jelenti szeretnének teljesen kijelentkezni például a Facebook-fiókban. Azonban helyi fiókok esetében a felhasználói munkamenet véget ér megfelelően.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>A saját Azure AD B2C-bérlő használata
Próbálja ki ezeket a kérelmeket saját magának, hajtsa végre az alábbi három lépést. Ez a cikk a saját értékeire használja a példaértékeket cserélje le:

1. [Azure AD B2C-bérlő létrehozása](active-directory-b2c-get-started.md). A bérlő nevét használja a kérelmeket.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) -Alkalmazásazonosító beszerzése és a egy `redirect_uri` értéket. Vegye fel az alkalmazás egy webalkalmazás vagy webes API-t. Igény szerint hozhat létre egy alkalmazás titkos kulcs.
3. [Szabályzatok létrehozása](active-directory-b2c-reference-policies.md) beszerzése a házirend nevét.

## <a name="samples"></a>Példák

* [Hozzon létre egy egyoldalas alkalmazás számára a Node.js segítségével](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Egyoldalas alkalmazás létrehozása a .NET-keretrendszerrel](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

