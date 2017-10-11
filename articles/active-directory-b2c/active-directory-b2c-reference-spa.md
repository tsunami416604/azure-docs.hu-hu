---
title: "Az Azure Active Directory B2C: Egyoldalas alkalmazások implicit engedélyezési folyamat |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre egyoldalas alkalmazások közvetlenül az Azure Active Directory B2C OAuth 2.0 típusú implicit engedélyezési folyamat használatával."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
ms.openlocfilehash: 44ff168599e9078506e1afdd0f1dc4657ef0964d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Az Azure AD B2C: Egyoldalas alkalmazások bejelentkezés OAuth 2.0 típusú implicit engedélyezési folyamat használatával

> [!NOTE]
> A funkció jelenleg előzetes verzió.
> 

Számos modern alkalmazások alkalmazás előtér írt elsősorban a JavaScript rendelkezik. Az alkalmazás íródik gyakran, például az AngularJS, az Ember.js vagy a Durandal keretrendszer használatával. Egyoldalas alkalmazások és más elsősorban a böngészőben futó JavaScript-alkalmazások van néhány további kihívást hitelesítéshez:

* Ezeknek az alkalmazásoknak a biztonsági jellemzőkkel jelentősen különböznek a hagyományos server-alapú webes alkalmazásokhoz.
* Számos engedélyezési kiszolgálók és identitás-szolgáltatóktól nem támogatja az eltérő eredetű erőforrások megosztása (CORS) kérések.
* Lehet, hogy elhagyja az alkalmazás teljes oldalas böngésző átirányítások jelentősen invazív, a felhasználói felületet.

Ezeket az alkalmazásokat, Azure Active Directory B2C támogatásához (az Azure AD B2C) az OAuth 2.0 típusú implicit engedélyezési folyamat használja. Az OAuth 2.0 engedélyezési implicit grant flow ismertetett [4.2 az OAuth 2.0-s szabvány szakasz](http://tools.ietf.org/html/rfc6749). Az implicit engedélyezési folyamat, az alkalmazás-jogkivonatokat kap az Azure Active Directory (Azure AD) közvetlenül a végpont, minden kiszolgáló-kiszolgáló exchange nélkül engedélyezik. Minden hitelesítési logikát és kezelési vesz munkamenet helyezhető el teljes egészében a JavaScript ügyfélprogramokban, de további oldal átirányítja az.

Az Azure AD B2C bővíti a szabványos OAuth 2.0 típusú implicit engedélyezési folyamat több mint az egyszerű hitelesítéshez és engedélyezéshez. Az Azure AD B2C vezet be a [házirend paraméter](active-directory-b2c-reference-policies.md). A házirend-paraméterrel, az OAuth 2.0 segítségével felhasználói élményt hozzáadása az alkalmazáshoz, például a regisztráció, bejelentkezés és profilok kezelése. Ez a cikk azt mutatja be a implicit engedélyezési folyamat és az Azure AD használatával valósít meg minden olyan ezeket a szolgáltatásokat a egyoldalas alkalmazások. Első lépések segítségével vessen egy pillantást a [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) és [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) minták.

A minta Azure AD B2C-címtár használjuk a ebben a cikkben példa HTTP-kérelmeket, **fabrikamb2c.onmicrosoft.com**. Is használhatja a saját mintaalkalmazás és a házirendeket. Megpróbálhatja a kérelmek saját kezűleg ezen értékek használatával, vagy azokat lecserélheti a saját értékeit.
Megtudhatja, hogyan [beolvasása a saját Azure AD B2C directory, az alkalmazás és a házirendek](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Protokoll diagramja

Az implicit bejelentkezési folyamat alábbihoz hasonló az alábbi ábra. Az egyes lépések a cikk későbbi részében részletes leírása.

![OpenID Connect sávok](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Hitelesítési kérések küldése
Ha a webalkalmazás kell hitelesíteni a felhasználót, majd hajtsa végre egy házirendet, a rendszer arra utasítja a felhasználót, hogy a `/authorize` végpont. Az adatfolyam interaktív része azt, ha a felhasználó végrehajtja a műveletet, attól függően, hogy a házirend. A felhasználó kapja az Azure AD-végpont token Azonosítót.

A kérelem, az ügyfél azt jelzi, az a `scope` paraméter szerzi be a felhasználó szükséges engedélyeket. Az a `p` paraméter azt jelzi, a házirend végrehajtásához. Az alábbi három példák (olvashatóság érdekében sortöréssel) minden használja egy másik házirendet. Követve betekintést nyerhet abba az egyes kérelmek működése, próbálja meg beilleszteni a kérelem egy böngészőbe, és azt futtatja.

### <a name="use-a-sign-in-policy"></a>A bejelentkezési házirend
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

### <a name="use-a-sign-up-policy"></a>A regisztrációs szabályzatban használata
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

### <a name="use-an-edit-profile-policy"></a>Egy házirend-profil szerkesztése
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

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazást a hozzárendelt Alkalmazásazonosító a [Azure-portálon](https://portal.azure.com). |
| response_type |Szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet. A válasz típusa is tartalmazhatja `token`. Ha `token`, az alkalmazás azonnal fogadhat második kérést a hitelesítési végpontra nélkül hozzáférési token a hitelesítési végpontra.  Ha használja a `token` válaszának típusa, a `scope` paraméternek tartalmaznia kell egy hatókör, amely jelzi, melyik erőforrást kell a jogkivonatot bocsásson ki. |
| redirect_uri |Ajánlott |Az átirányítási URI-t, az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Az pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| response_mode |Ajánlott |Meghatározza az eredményül kapott jogkivonat vissza küldése az alkalmazásnak használandó módszert.  Implicit adatfolyamok, használjon `fragment`. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték azt jelzi, az Azure AD mindkét kérnek engedély. A `openid` hatókör azt jelzi, a felhasználói azonosító-jogkivonatokat formájában adatait, és jelentkezzen be a felhasználó engedélyt. (Lesz döntésről bővebben Ez a cikk több későbbi részében.) A `offline_access` hatókör megadása nem kötelező web Apps. Azt jelzi, hogy kell-e az alkalmazást egy frissítési jogkivonat hosszú élettartamú erőforrások elérése érdekében. |
| state |Ajánlott |A kérelemhez, amely az eredmény abban is a lexikális elem szerepel érték. Bármely, a használni kívánt tartalmat karakterlánc lehet. Általában egy véletlenszerűen generált, egyedi érték használata esetén webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében. Az állapot is kódolásához használatos a felhasználói állapot az alkalmazás információkat történt a hitelesítési kérést, mielőtt a lap, amilyenek korábban voltak a. |
| Nonce |Szükséges |A kérelem (az alkalmazás által generált), amely megtalálható az eredményül kapott azonosító jogkivonat jogcímként szerepel érték. Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Általában értéke véletlenszerű, egyedi karakterlánc, amely segítségével azonosíthatja a kérelem forrása. |
| P |Szükséges |A házirend végrehajtásához. Olyan házirendet, amely az Azure AD B2C bérlő létrehozása nevét. A házirend nevének értékét kell kezdődnie **b2c\_1\_**. További információkért lásd: [beépített házirendek az Azure AD B2C](active-directory-b2c-reference-policies.md). |
| parancssor |Optional |A típus a felhasználói beavatkozás szükséges. Az egyetlen érvényes érték jelenleg `login`. Ekkor elemezni, a felhasználó megadja hitelesítő adataikkal, hogy kérésre. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználónak kapcsolatba a házirend-munkafolyamat végrehajtásához. Ez lehet, hogy magában a írja be a felhasználónevét és jelszavát, felhasználó bejelentkezik egy közösségi identitás regisztrál a könyvtár, vagy több lépésből áll. Felhasználói műveletek attól függ, hogyan van definiálva a házirendet.

Miután a felhasználó teljesítette a szabályzat, az Azure AD az alkalmazásban használt értéke a választ ad `redirect_uri`. A megadott metódust használja a `response_mode` paraméter. A válasz megegyezik pontosan az egyes felhasználói művelet forgatókönyv, függetlenül a házirend, hogy végre lett hajtva.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használó `response_mode=fragment` és `response_type=id_token+token` sortöréssel olvashatóságát, a következőképpen néz:

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
| access_token |A hozzáférési jogkivonat, amely az alkalmazás kéri.  A hozzáférési jogkivonat nem szabad dekódolni, vagy más módon megvizsgálni. Nem átlátszó karakterláncként kezelhető. |
| token_type |A jogkivonat típusa érték. A csak az Azure AD támogató típus tulajdonosi. |
| expires_in |Mennyi ideig, amely a hozzáférési jogkivonat érvénytelen (másodpercben). |
| Hatókör |A hatókörök, amely a token érvényes. Is használhatja szerepet, amelyet a gyorsítótár jogkivonatok későbbi használatra. |
| id_token |Az alkalmazás által kért azonosítója jogkivonat. Az azonosító jogkivonat segítségével ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. Azonosító-jogkivonatokat és azok tartalmát kapcsolatos további információkért tekintse meg a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |
| state |Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is küldhetők az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc osztályozva a felmerülő hibákat. Is használhatja a hiba kódja hibaelhárítási célból. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |
| state |Lásd az előző táblázatban a teljes leírását. Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak.|

## <a name="validate-the-id-token"></a>Az azonosító token ellenőrzése
Egy azonosító tokent fogadása elég nem lehet hitelesíteni a felhasználót. Az azonosító jogkivonat-aláírás ellenőrzése, és ellenőrizze a jogcímek, az alkalmazás követelményeinek / jogkivonat. Használja az Azure AD B2C [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényes nyilvános kulccsal történő titkosítás.

Számos nyílt forráskódú kódtárai ellenőrzéséhez a JWTs, attól függően, hogy a használni kívánt nyelven érhetők el. Vegye figyelembe a rendelkezésre álló nyílt forráskódú kódtárai felfedezése megvalósító egyéni ellenőrzési logika helyett. Ez a cikk az információk használatával alakítsa ki a tárak megfelelően használata.

Az Azure AD B2C az OpenID Connect metaadatok végponttal rendelkezik. Egy alkalmazás a végpont használatával futásidőben Azure AD B2C-vel kapcsolatos információk beolvasása. Ezen információk közé tartozik a végpontok, lexikális elem tartalmának és jogkivonat-aláíró kulcsok. Nincs a JSON metaadat-dokumentum-házirendet az Azure AD B2C-bérlőben. Például a metaadat-dokumentum a b2c_1_sign_in házirend a fabrikamb2c.onmicrosoft.com bérlő a következő helyen található:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

A konfigurációs dokumentum tulajdonságainak egyike a `jwks_uri`. Az ugyanabban a házirendben a következő lenne:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Annak meghatározásához, mely házirendet egy azonosító tokent aláírásához használt (és a metaadatok beolvasása helyét), két lehetőség közül választhat. Először a házirend neve szerepel a `acr` a jogcím `id_token`. A jogcímek egy azonosító jogkivonat elemzése kapcsolatos információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). A másik lehetőség egy kódolására a házirend az értékét a `state` paraméter, a kérelem elküldésekor. Ezt követően dekódolni a `state` paraméter segítségével határozza meg, mely házirend lett megadva. Mindkét módszer esetén érvényes.

Után jut hozzá a az OpenID Connect metaadat-végpontjához a metaadat-dokumentum, az RSA-256 nyilvános kulcsot (Ez a végpont található) segítségével ellenőrizze az azonosító jogkivonat aláírását. Előfordulhat, hogy több-kulcsok a végpont egy adott időpontban, minden egyes által azonosított egy `kid`. A fejlécében `id_token` is tartalmaz egy `kid` jogcímek. Azt jelzi, amelyhez ezeket a kulcsokat a Azonosítót jogkivonatban aláírásához használt. További információt, beleértve a megismerését [jogkivonatok ellenőrzése](active-directory-b2c-reference-tokens.md#token-validation), tekintse meg a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Után ellenőrizze az azonosító jogkivonat aláírását, több jogcím ellenőrzés megkövetelése. Példa:

* Ellenőrizze a `nonce` jogcím-token ismétléses támadások megelőzése érdekében. Az értékét a bejelentkezési kérelemben megadott kell lennie.
* Ellenőrizze a `aud` jogcím győződjön meg arról, hogy a Azonosítót jogkivonatban ki az alkalmazást. Az értéknek kell lennie az alkalmazás Azonosítóját a az alkalmazás.
* Ellenőrizze a `iat` és `exp` győződjön meg arról, hogy az azonosító jogkivonat nem járt jogcímeket.

Végre kell hajtania néhány további ellenőrzések részletesen ismerteti a [OpenID Connect Core Spec](http://openid.net/specs/openid-connect-core-1_0.html). Érdemes további ellenőrizhesse, a forgatókönyvtől függően is. Néhány gyakori érvényesítést a következők:

* Győződjön meg arról, hogy a felhasználó vagy a szervezet rendelkezik regisztrálta az alkalmazást.
* Győződjön meg arról, hogy a felhasználó megfelelő jogosultságokkal és engedélyekkel rendelkezik-e.
* Győződjön meg arról, hogy bizonyos erősségével hitelesítési történt, ilyen módon az Azure multi-factor Authentication használatával történő.

A jogcímek egy Azonosítót jogkivonatban kapcsolatos további információkért tekintse meg a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).

Teljesen érvényesítése a Azonosítót jogkivonatban után megkezdheti a munkamenet a felhasználó. Az alkalmazás, a jogcímek használata a Azonosítót jogkivonatban megjelenítheti a felhasználó adatait. Ezek az információk megjelenítése, rekordok, engedélyezési és így tovább használható.

## <a name="get-access-tokens"></a>A hozzáférési jogkivonatok lekérésére
Ha a webalkalmazások kell egyedül házirendek hajtható végre, ugorjon a következő néhány szakasz. A következő szakaszokban található információk csak a webalkalmazások, amelyeknek szükségük van a hitelesített hívásokat egy webes API-hoz, és amelyhez Azure AD B2C által védett alkalmazhatók.

Most, hogy az alkalmazás a felhasználó most jelentkezik be, hívó webes API-k, az Azure AD által védett kaphat jogkivonatot. Akkor is, ha korábban fogadott levelei jogkivonat használatával a `token` választípus, ez a módszer segítségével szerezzen be további erőforrások jogkivonatok nélkül irányít át a felhasználót, hogy jelentkezzen be újra.

Egy tipikus web app folyamatában, akkor ehhez azáltal, hogy a kérelem a `/token` végpont.  A végpont azonban nem támogatja a CORS-kérelmeket, az AJAX-hívások és frissítési jogkivonatok lehetőség nem érhető el. Ehelyett használhatja a implicit engedélyezési folyamat egy rejtett HTML iframe elemben új jogkivonatok lekérésére más webes API-k. Íme egy példa, olvashatóságát sortöréssel:

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

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazást a hozzárendelt Alkalmazásazonosító a [Azure-portálon](https://portal.azure.com). |
| response_type |Szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet.  Az is előfordulhat, hogy tartalmazza a választípus `token`. Ha `token` itt, az alkalmazás azonnal fogadhat olyan hozzáférési jogkivonatot a hitelesítési végpontra, anélkül, hogy egy második kérelmet a hitelesítési végpontra. Ha használja a `token` válaszának típusa, a `scope` paraméternek tartalmaznia kell egy hatókör, amely jelzi, melyik erőforrást kell a jogkivonatot bocsásson ki. |
| redirect_uri |Ajánlott |Az átirányítási URI-t, az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat. Az pontosan egyeznie kell az átirányítási URI-azonosítók regisztrálta a portálon, azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája.  A jogkivonatok lekérésének tartalmazza a hatóköröket a kívánt erőforrás szükséges. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott jogkivonat vissza küldése az alkalmazásnak.  Lehet `query`, `form_post`, vagy `fragment`. |
| state |Ajánlott |A token válaszként visszaadott a kérelemben szereplő érték.  Bármely, a használni kívánt tartalmat karakterlánc lehet.  Általában egy véletlenszerűen generált, egyedi érték használata esetén webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében.  Az állapot is kódolásához használatos a felhasználói állapot az alkalmazás információkat előtt a hitelesítési kérelmet. Például a lap vagy nézet, a felhasználó nem az. |
| Nonce |Szükséges |A kérelem, az eredményül kapott azonosító jogkivonat jogcímként megtalálható az alkalmazás által generált szerepel érték.  Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Általában értéke véletlenszerű, egyedi karakterlánc, amely azonosítja a kérelem forrása. |
| parancssor |Szükséges |Frissítse, valamint egy rejtett iframe a jogkivonatok lekérésére használja `prompt=none` annak érdekében, hogy az iframe nem elakadnak a bejelentkezési oldalon, és azonnal visszaadja az eredményeket. |
| login_hint |Szükséges |Frissítse, és egy rejtett iframe a jogkivonatok lekérésére, vegye fel a mutató használatával tudja megkülönböztetni a felhasználónak kell egyszerre több munkamenetet a felhasználó felhasználóneve. Kibonthatja a felhasználónév a egy korábbi bejelentkezés használatával a `preferred_username` jogcímek. |
| domain_hint |Szükséges |Lehet `consumers` vagy `organizations`.  Frissítése, és a jogkivonatok lekérésének a rejtett iframe, meg kell adni a `domain_hint` a kérelemben szereplő érték.  Bontsa ki a `tid` jogcím egy korábbi bejelentkezés az azonosító jogkivonatból meghatározni, melyik értéket kell használni.  Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`.  Ellenkező esetben használja `domain_hint=organizations`. |

Úgy, hogy a `prompt=none` paraméter, a kérelem vagy sikeres vagy azonnal leáll, és visszaadja az alkalmazás.  A sikeres válasz megadott módon zajlik a jelzett átirányítási URI-t, az alkalmazást a `response_mode` paraméter.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=fragment` dolgozunk:

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
| access_token |A token által kért az alkalmazáshoz. |
| token_type |A jogkivonat típusa mindig lesz tulajdonosi. |
| state |Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| Hatókör |A hatókörök, amely a hozzáférési token érvényes. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is küldhetők az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően.  A `prompt=none`, várt hiba néz ki:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| error |Hiba történt a felmerülő hibákat besorolására használható kód karakterlánc. A karakterlánc hibák reagálni is használja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |

Ez a hiba a iframe kérelmet kap, ha a felhasználó kell interaktív jelentkezzen be újra egy új jogkivonatot lekérdezni. Ez úgy, hogy az alkalmazás számára legjobb képes kezelni.

## <a name="refresh-tokens"></a>Frissítési jogkivonatok
Azonosító-jogkivonatokat és a hozzáférési jogkivonatok mindkét rövid időn belül lejár. Az alkalmazás rendszeres időközönként frissíti ezeket a jogkivonatokat kell készíteni.  Mindkét típusú jogkivonat frissítéséhez hajtsa végre a rejtett iframe kérésben használtuk korábbi példa segítségével a `prompt=none` paraméter segítségével szabályozhatja az Azure AD lépéseket.  Egy új fogadásához `id_token` értéket, használjon `response_type=id_token` és `scope=openid`, és egy `nonce` paraméter.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérés küldése
Ha meg szeretné beléptetni a felhasználót az alkalmazásból, átirányítja a felhasználót az Azure AD-be jelentkezzen ki. Ha nem így tesz, a felhasználó lehet újból hitelesítésre az alkalmazásnak, hogy a hitelesítő adataik ismételt beírása nélkül. Ennek az az oka érvényes egyszeri bejelentkezés munkamenetet az Azure ad-val rendelkeznek.

Egyszerűen irányíthatja át a felhasználót, hogy a `end_session_endpoint` , hogy szerepel-e az azonos OpenID Connect metaadat-dokumentum ismertetett [ellenőrzése a Azonosítót jogkivonatban](#validate-the-id-token). Példa:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| P |Szükséges |A házirend a felhasználói kívül az alkalmazás aláírásához. |
| post_logout_redirect_uri |Ajánlott |Az URL-címet, a felhasználó után a rendszer átirányítja a sikeres kijelentkezési. Ha nincs megadva, az Azure AD B2C alábbi hibaüzenet jelenik meg a felhasználó számára látható. |

> [!NOTE]
> Irányítja a felhasználót, hogy a `end_session_endpoint` néhány, a felhasználó állapotának egyszeri bejelentkezés az Azure AD B2C törli. Azt azonban nem jelentkezik ki a felhasználó közösségi identity provider munkamenet a felhasználó. Ha a felhasználó kijelöli azonos szolgáltató azonosítani egy későbbi bejelentkezés során, a felhasználók újrahitelesítése, a hitelesítő adatok megadása nélkül. Ha egy felhasználó kijelentkezik az Azure AD B2C alkalmazás azt szeretné, akkor nem feltétlenül jelenti teljesen kijelentkezni például Facebook fiókjuk szeretnék. Azonban a helyi fiókok, a felhasználói munkamenet véget ér megfelelően.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>A saját Azure AD B2C bérlő használata
Próbálja meg ezek a kérelmek saját magának, végezze el az alábbi három lépést. Cserélje le a példában szereplő értékeket, ebben a cikkben a saját értékeket használjuk:

1. [Az Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md). A bérlő nevét használja a kérelemben.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) az Alkalmazásazonosító beszerzése és egy `redirect_uri` érték. Vegye fel az alkalmazást egy webalkalmazást vagy webes API. Ha szükséges egy alkalmazás titkos kulcs is létrehozhat.
3. [A házirendek létrehozása](active-directory-b2c-reference-policies.md) beszerzése a házirend nevét.

## <a name="samples"></a>Példák

* [Hozzon létre egy alkalmazás számára a Node.js segítségével](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Hozzon létre egy egyoldalas alkalmazást .NET használatával](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

