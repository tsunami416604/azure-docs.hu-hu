---
title: Biztonságos egylapos alkalmazások az Azure AD v2.0 implicit engedélyezési folyamat használatával |} Microsoft Docs
description: Épület webes alkalmazások az Azure AD v2.0 implicit engedélyezési folyamat végrehajtása egylapos alkalmazások.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 07fbda30cdc76e5e4e82b79954d0b0a56e032b50
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protokoll - gyógyfürdők a implicit engedélyezési folyamat használata
A v2.0-végponttal is jelentkezzen be arra a egylapos alkalmazások a Microsoft a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók. Egyetlen lap, és más JavaScript alkalmazásokkal szerepkört futtató elsősorban egy böngésző arc érdekes néhány felkéri, amikor a hitelesítés:

* Ezeknek az alkalmazásoknak a biztonsági jellemzőkkel jelentősen különböznek a hagyományos server-alapú webes alkalmazásokhoz.
* Sok engedélyezési kiszolgálók & identitás-szolgáltatóktól nem támogatják a CORS-kérelmeket.
* Teljes lap böngésző átirányítja elhagyja az alkalmazás válik különösen invazív, a felhasználói felületet.

Az alkalmazás (gondolja, hogy: AngularJS, az Ember.js, React.js, stb) az Azure AD támogatja az OAuth 2.0 Implicit Grant flow. Az implicit engedélyezési folyamat ismertetett a [OAuth 2.0 specifikáció](http://tools.ietf.org/html/rfc6749#section-4.2). A fő előnye, hogy lehetővé teszi az alkalmazásnak, hogy jogkivonatokhoz az Azure AD egy háttérkiszolgáló végrehajtása nélkül hitelesítő exchange. Ez lehetővé teszi az alkalmazásnak, hogy a felhasználó bejelentkezhet, munkamenet karbantartása, és a jogkivonatok megkapásához más webes API-k minden belül az ügyfél JavaScript-kód. Néhány fontos biztonsági szempontot figyelembe kell venni a implicit engedélyezési folyamat - kifejezetten körülbelül használatakor [ügyfél](http://tools.ietf.org/html/rfc6749#section-10.3) és [felhasználó megszemélyesítési](http://tools.ietf.org/html/rfc6749#section-10.3).

Ha szeretné a implicit engedélyezési folyamat és az Azure AD segítségével hitelesítés hozzáadása a JavaScript-alkalmazást, azt javasoljuk, használjon a nyílt forráskódú JavaScript kódtár [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Nincsenek elérhető néhány AngularJS oktatóanyagok [Itt](active-directory-appmodel-v2-overview.md#getting-started) segítséget nyújtanak a kezdéshez. 

Azonban ha inkább nem egy könyvtárban, az alkalmazás egylapos, és küldhet magának, kövesse az alábbi általános lépéseket.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól. Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="protocol-diagram"></a>Protokoll diagramja
A teljes implicit bejelentkezési folyamata az alábbihoz hasonló - lépéseit az alábbiakban ismertetjük.

![OpenId Connect sávok](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése
Kezdetben beléptetni a felhasználót az alkalmazásba, küldjön egy [OpenID Connect](active-directory-v2-protocols-oidc.md) engedélyezési kérelem és a get egy `id_token` a v2.0-végponttal való:

> [!IMPORTANT]
> Ahhoz, hogy sikeresen egy azonosító jogkivonatot, az alkalmazás regisztrációja a kérelem a [regisztrációs portál](https://apps.dev.microsoft.com) kell rendelkeznie a **[Implicit grant](active-directory-v2-protocols-implicit.md)** engedélyezve a a webes ügyféllel. Ha nincs engedélyezve, egy `unsupported_response` hibaüzenetet küld: "a"response_type"bemeneti paraméter megadott értéke nem engedélyezett ennél az ügyfélnél. Várt érték "code" "

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> A kérelem végrehajtása az alábbi hivatkozásra kattintva! Történő bejelentkezés után a böngésző át kell irányítani `https://localhost/myapp/` rendelkező egy `id_token` a böngésző címsorába.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Paraméter |  | Leírás |
| --- | --- | --- |
| bérlő |szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat. További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints). |
| client_id |szükséges |Az alkalmazásazonosító, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazáshoz hozzárendelt. |
| response_type |szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet. Az is előfordulhat, hogy tartalmazza a response_type `token`. Használatával `token` itt lehetővé teszi az alkalmazásnak, hogy fogadjon olyan hozzáférési jogkivonatot azonnal a hitelesítési végpontra anélkül, hogy a hitelesítési végpontra második kérelem végrehajtásához. Ha használja a `token` response_type, a `scope` paraméternek tartalmaznia kell egy hatókör, amely jelzi, melyik erőforrást kell a jogkivonatot bocsásson ki. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszok redirect_uri. Ez pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve az url-kódolású kell lennie. |
| scope |szükséges |Hatókörök szóközökkel elválasztott listája. Az OpenID Connect, magában kell foglalnia a hatókör `openid`, amely az eszköz a hozzájárulási felhasználói felületén a "Bejelentkezés" engedélyt. Opcionálisan is érdemes lehet felvenni a `email` vagy `profile` [hatókörök](active-directory-v2-scopes.md) további felhasználói adatok hozzáféréséhez. A kérelem a különböző erőforrások beleegyezést kérő más hatókörök is. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott jogkivonat vissza küldése az alkalmazásnak. Meg kell `fragment` a implicit engedélyezési folyamat számára. |
| állapot |Ajánlott |A kérelemhez, amely a token válaszul is visszaadott szerepel érték. Bármely, a kívánt tartalmat karakterlánc lehet. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások megelőzése](http://tools.ietf.org/html/rfc6749#section-10.12). Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a is használatos. |
| Nonce |szükséges |A kérelem, az alkalmazás, az eredményül kapott id_token jogcímként szerepeltetni által generált szerepel érték. Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Az érték véletlenszerű, egyedi karakterlánc, amely segítségével azonosíthatja a kérelem forrása általában. |
| parancssor |választható |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg csak érvényes értékei a "bejelentkezés", "none", és "". `prompt=login` arra kényszeríti a felhasználó megadja a hitelesítő adataikat, hogy kérésre nem lehet negálni egyszeri bejelentkezést. `prompt=none` Ellenkező - biztosítja, hogy a felhasználó számára nem jelenik meg minden bármely interaktív kérdés. Ha a kérelem nem hajtható végre csendes keresztül egyszeri bejelentkezést, akkor a v2.0-végpontra hibát adnak vissza. `prompt=consent` az OAuth-hozzájárulás párbeszédpanel akkor indul el, miután a felhasználó jelentkezik be, amely kéri a felhasználót, hogy engedélyezze, hogy az alkalmazás. |
| login_hint |választható |Segítségével előre töltse ki a felhasználónév vagy e-mail cím mező annak a bejelentkezési oldal a felhasználó számára, ha tudja, hogy időben a felhasználónevét. Gyakran alkalmazások ismételt hitelesítés, hogy már kivont a felhasználónév egy korábbi bejelentkezési használatával során fogja használni ezt a paramétert a `preferred_username` jogcímek. |
| domain_hint |választható |Egyike lehet `consumers` vagy `organizations`. Ha tartalmazza, azt az e-mail alapú felderítési folyamat kihagyja, hogy a felhasználó végighalad a v2.0 bejelentkezési oldal, ami egy nagyobb jelentőséggel zökkenőmentes felhasználói élmény. Gyakran alkalmazások használja ezt a paramétert ismételt hitelesítés során kivonja a `tid` a id_token származó jogcímek. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad` használja (a Microsoft Account fogyasztói bérlő), `domain_hint=consumers`. Ellenkező esetben használja `domain_hint=organizations`. |


Ezen a ponton a kérni fogja a felhasználótól kell adnia a hitelesítő adatait, és a hitelesítés végrehajtásához. A v2.0-végpontra is biztosítják, hogy a felhasználó hozzájárult szerepelnek az engedélyeket a `scope` lekérdezési paraméter. Ha a felhasználó nem hozzájárult bármelyik ezeket az engedélyeket, azt kéri a felhasználó számára, hogy a szükséges engedélyekkel. A részletek [engedélyek, beleegyezése és több-bérlős alkalmazásokhoz itt megadott](active-directory-v2-scopes.md).

Miután a felhasználó hitelesíti és engedélyezi a hozzájárulási, a v2.0-végpontra ad vissza az alkalmazást a jelzett választ `redirect_uri`, az ismertetett módon a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=fragment` és `response_type=id_token+token` sortöréssel olvashatóságát, a következőképpen néz:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| access_token |Belefoglalt if `response_type` tartalmaz `token`. A hozzáférési jogkivonat, amely az alkalmazás kéri, ebben az esetben a Microsoft Graph. A hozzáférési jogkivonat nem szabad dekódolni, vagy más módon megvizsgálni, azt nem átlátszó karakterláncként kell kezelni. |
| token_type |Belefoglalt if `response_type` tartalmaz `token`. Mindig `Bearer`. |
| expires_in |Belefoglalt if `response_type` tartalmaz `token`. Azt jelzi, hogy a lexikális elem gyorsítótárazása célra érvényes másodpercben. |
| scope |Belefoglalt if `response_type` tartalmaz `token`. Azt jelzi, hogy a hatókörök, amelyhez a access_token érvényes lesz. |
| id_token |A id_token, amely az alkalmazás kéri. A id_token segítségével ellenőrzi a felhasználó identitását, és a felhasználói munkamenet elindításához. További részleteket a id_tokens és azok tartalmát a [v2.0 jogkivonat végponthivatkozás](active-directory-v2-tokens.md). |
| állapot |Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek. |

#### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , az alkalmazás megfelelően tudja ezeket kezelni:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

## <a name="validate-the-idtoken"></a>A id_token ellenőrzése
Csak fogadó egy id_token túl kicsi a felhasználó; a id_token aláírás érvényesítése kell, és ellenőrizze a jogcímek, az alkalmazás követelményeinek / jogkivonat. Használja a v2.0-végpontra [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényes nyilvános kulccsal történő titkosítás.

Ha szeretné ellenőrizni a `id_token` ügyfél kódot, de általános gyakorlat, hogy küldjön a `id_token` háttérkiszolgálóhoz, és végezze el az érvényesítési hiba. Miután aláírását, valamint a id_token ellenőrizte, van néhány jogcímek ellenőrzéséhez szükséges. Lásd: a [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md) további információt, többek között [jogkivonatok ellenőrzése](active-directory-v2-tokens.md#validating-tokens) és [fontos információk kapcsolatos aláírási kulcs átfordulási](active-directory-v2-tokens.md#validating-tokens). Azt javasoljuk, elemzés és ellenőrzése a szalagtár alkalmazó jogkivonatok - van legalább egy legtöbb nyelvekhez és platformokhoz érhető el.
<!--TODO: Improve the information on this-->

Először is célszerű további ellenőrizhesse a forgatókönyvtől függően. Néhány gyakori érvényesítést a következők:

* A felhasználó/szervezeti biztosítása rendelkezik regisztrált az alkalmazást.
* Biztosítása a felhasználó rendelkezik a megfelelő engedélyezési vagy jogosultságokkal
* Bizonyos erősségével hitelesítés úgy történt, például a többtényezős hitelesítést.

A jogcím szerepel egy id_token további információkért tekintse meg a [v2.0 jogkivonat végponthivatkozás](active-directory-v2-tokens.md).

Teljesen ellenőrzése a id_token, után a felhasználói munkamenet elindításához, és a jogcímek használata a id_token az beszerzése az alkalmazásban a felhasználó adatai. Ez az információ alkalmas megjelenített, rekordok, engedélyek, stb.

## <a name="get-access-tokens"></a>A hozzáférési jogkivonatok lekérésére
Most, hogy a felhasználó már regisztrált az egylapos alkalmazásba, kaphat jogkivonatot hívó webes API-k, az Azure AD által védett, mint a [Microsoft Graph](https://graph.microsoft.io). Akkor is, ha már kapott egy token használatával a `token` response_type, ez a módszer segítségével szerezzen be további forrásanyagokra mutató jogkivonatok anélkül, hogy átirányítja a felhasználót, hogy jelentkezzen be újra.

A normál OpenID Connect/OAuth folyamatában, akkor ezt úgy teheti meg egy kérést a v2.0 `/token` végpont. A v2.0-végpontra azonban nem támogatja a CORS-kérelmeket, a kérdés kívül esik, és frissítési jogkivonatok AJAX-hívások. Ehelyett használhatja a implicit engedélyezési folyamat a rejtett iframe új jogkivonatok lekérésére más webes API: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Másolás és beillesztés próbálja meg a kérelem egy böngészőlapon az alábbi! (Ne felejtse el lecserélni a `domain_hint` és a `login_hint` értékek a megfelelő értékekkel a felhasználó)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| bérlő |szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat. További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints). |
| client_id |szükséges |Az alkalmazás azonosítója, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazáshoz hozzárendelt. |
| response_type |szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezhet. Például a más response_types is tartalmazhat `code`. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszok redirect_uri. Ez pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve az url-kódolású kell lennie. |
| scope |szükséges |Hatókörök szóközökkel elválasztott listája. A jogkivonatok lekérésének tartalmazza az összes [hatókörök](active-directory-v2-scopes.md) érdeklő erőforrás van szüksége. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott jogkivonat vissza küldése az alkalmazásnak. Egyike lehet `query`, `form_post`, vagy `fragment`. |
| állapot |Ajánlott |A kérelemhez, amely a token válaszul is visszaadott szerepel érték. Bármely, a kívánt tartalmat karakterlánc lehet. Egy véletlenszerűen generált egyedi érték webhelyközi kérések hamisításának megakadályozása támadások megelőzése általában szolgál. Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a is használatos. |
| Nonce |szükséges |A kérelem, az alkalmazás, az eredményül kapott id_token jogcímként szerepeltetni által generált szerepel érték. Az alkalmazás ezután ellenőrizheti a hitelesítési karakterláncok ismétlésének támadások mérséklése ezt az értéket. Az érték véletlenszerű, egyedi karakterlánc, amely segítségével azonosíthatja a kérelem forrása általában. |
| parancssor |szükséges |Frissítés & rejtett iframe a jogkivonatok lekérésének, érdemes használni a `prompt=none` annak érdekében, hogy az iframe nem lefagy a v2.0 bejelentkezési oldalára, és azonnal visszaadja az eredményeket. |
| login_hint |szükséges |Frissítése & rejtett iframe a jogkivonatok lekérésének, meg kell adni a felhasználó felhasználóneve ez mutatóban több munkamenet a felhasználó rendelkezik egy időben megkülönböztetésére. A felhasználónév kinyerése egy korábbi bejelentkezési használata a `preferred_username` jogcímek. |
| domain_hint |szükséges |Egyike lehet `consumers` vagy `organizations`. Frissítés & rejtett iframe a jogkivonatok lekérésének, meg kell adni a domain_hint a kérelemben. Ki kell bontania az `tid` egy előző bejelentkezés mely használandó érték meghatározásához a id_token származó jogcímek. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad`, használjon `domain_hint=consumers`. Ellenkező esetben használja `domain_hint=organizations`. |

Köszönet a következőknek a `prompt=none` paraméter, a kérelem vagy sikeres vagy meghiúsul azonnal, és térjen vissza az alkalmazást. A sikeres válasz kapnak az alkalmazást a jelzett `redirect_uri`, az ismertetett módon a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz használatával `response_mode=fragment` láthatóhoz hasonló:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paraméter | Leírás |
| --- | --- |
| access_token |A token által kért az alkalmazáshoz. |
| token_type |Mindig `Bearer`. |
| állapot |Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| scope |A hatókörök, amely a hozzáférési token érvényes. |

#### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , az alkalmazás megfelelően tudja ezeket kezelni. A következőket `prompt=none`, várt hiba történt a következő lesz:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| hiba |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |

Ez a hiba a iframe kérelmet kap, ha a felhasználó kell interaktív jelentkezzen be újra egy új jogkivonatot lekérdezni. Ha szeretné, ebben az esetben a kiválasztott módon szabálykészletében az alkalmazás kezelni.

## <a name="validating-access-tokens"></a>Hozzáférési jogkivonatok ellenőrzése

Miután egy access_token kap, ellenőrizze, hogy az aláírás a jogkivonat, valamint a következő jogcímeket. Választhatja azt is, további ellenőrizhesse a forgatókönyv alapján. 

* **a célközönség** jogcím, győződjön meg arról, hogy a jogkivonat kell fordítani az alkalmazás szándéka
* **kibocsátó** jogcím, győződjön meg arról, hogy a jogkivonat kiállító az alkalmazáshoz a v2.0-végpontra
* **hatálybalépési idő** és **lejárati idő** jogcímeket, győződjön meg arról, hogy a jogkivonat nem járt

A hozzáférési jogkivonat szerepel a jogcímek kapcsolatos további információkért tekintse meg a [v2.0 jogkivonat végponthivatkozás](active-directory-v2-tokens.md)

## <a name="refreshing-tokens"></a>Jogkivonatok frissítése
Az implicit támogatás nem biztosít a frissítési jogkivonatokat. Mindkét `id_token`s és `access_token`s rövid idő alatt, így ezek frissítése elő kell készíteni az alkalmazás rendszeres időközönként jogkivonatok után lejár. Mindkét típusú jogkivonat frissítéséhez a rejtett iframe kérésben promptjai használatával végezheti el a `prompt=none` paraméter segítségével szabályozhatja az Azure AD működését. Ha szeretne kapni egy új `id_token`, használjon `response_type=id_token` és `scope=openid`, valamint egy `nonce` paraméter.

## <a name="send-a-sign-out-request"></a>A Kijelentkezés kérelem küldése
A OpenIdConnect `end_session_endpoint` lehetővé teszi az alkalmazás kérelmet küld a v2.0-végpontra a felhasználói munkamenet befejezését, majd törölje a cookie-kat a v2.0-végpontra állítja be. Webalkalmazás kívül egy felhasználó teljesen bejelentkezni az alkalmazás kell a saját munkamenet befejezése a felhasználó (általában egy token gyorsítótár kiürítése vagy elvetése cookie-k) által, és majd irányítsa át a böngésző számára:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| bérlő |szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosítókat. További részletekért lásd: [alapjai protokoll](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | Ajánlott | Az URL-címet, a felhasználó vissza kell adni a kijelentkezési befejeződése után. Ezt az értéket meg kell egyeznie az átirányítási URI-azonosítók regisztrálva az alkalmazás egyik. Nem található, a felhasználó megjelenik-e alábbi hibaüzenet jelenik meg a v2.0-végpontja. |
