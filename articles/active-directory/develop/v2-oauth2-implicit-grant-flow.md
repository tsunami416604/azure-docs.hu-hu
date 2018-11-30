---
title: Egyoldalas alkalmazások az Azure AD v2.0 implicit folyamat használatával biztonságos |} A Microsoft Docs
description: Webes alkalmazások létrehozása az Azure AD v2.0 megvalósítását az implicit folyamat használatával egyoldalas alkalmazások.
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
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 878c2596a1d884e26a4b4a4ed4764cfd9ce6b39b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424100"
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protokoll - gyógyfürdők az implicit folyamat használata

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A v2.0-végponttal egyoldalas alkalmazásaiba a Microsoft a személyes és munkahelyi vagy iskolai fiókok bejelentkezhetnek a felhasználók. Egyoldalas és más, egy böngészőben arc elsősorban a Futtatás néhány érdekes JavaScript-alkalmazások kihívása, amikor a hitelesítés:

* Az ezeknek az alkalmazásoknak biztonsági jellemzőkkel jelentősen eltérnek a hagyományos server-alapú webes alkalmazások.
* Számos engedélyezési kiszolgálók és identitás-szolgáltatóktól nem támogatják a CORS-kérések.
* Teljes oldal böngésző átirányítja a felhasználókat erről az alkalmazás a felhasználói élmény, különösen invazív válnak.

Ezekhez az alkalmazásokhoz (AngularJS, Ember.js, React.js, stb.) az Azure Active Directory (Azure AD) támogatja az OAuth 2.0 típusú Implicit engedélyezés folyamat. Az implicit folyamatot ismertet a [OAuth 2.0 Ismertetőjének](https://tools.ietf.org/html/rfc6749#section-4.2). A fő előnye, hogy lehetővé teszi az alkalmazásnak, hogy tokenekhez Azure AD-ből egy háttérkiszolgáló végrehajtása nélkül hitelesítő adatok. Ez lehetővé teszi az alkalmazásnak, hogy jelentkezzen be a felhasználói munkamenet fenntartásához és tokenekhez más webes API-k mindezt az ügyfelet JavaScript-kódot. Van néhány fontos biztonsági szempontok figyelembe kell venni az implicit folyamat mintegy kifejezetten használatakor [ügyfél](https://tools.ietf.org/html/rfc6749#section-10.3) és [felhasználó megszemélyesítése](https://tools.ietf.org/html/rfc6749#section-10.3).

Ha azt szeretné, az implicit folyamat és az Azure AD-hitelesítés hozzáadása a JavaScript-alkalmazás használatával, azt javasoljuk, használja a nyílt forráskódú JavaScript-kódtár [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js). 

Azonban ha nem kívánja az egyoldalas alkalmazás szalagtár használatára, és küldhet saját magának, kövesse az alábbi általános lépéseket.

> [!NOTE]
> A 2.0-s verziójú végpont nem minden Azure AD-forgatókönyvet és -funkciót támogat. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán látható, hogy a teljes implicit bejelentkezési folyamat néz ki, és a következő szakaszok ismertetik az egyes lépések részletesebben.

![OpenId Connect sávok](./media/v2-oauth2-implicit-grant-flow/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem küldése

Kezdetben beléptetni a felhasználót az alkalmazásba, küldhet egy [OpenID Connect](v2-protocols-oidc.md) engedélyezési kérést és a get- `id_token` a v2.0-végpont a.

> [!IMPORTANT]
> Sikerült a kérelem egy azonosító jogkivonat, az alkalmazás regisztrációját a a [regisztrációs portál](https://apps.dev.microsoft.com) kell rendelkeznie a **Implicit folyamat engedélyezése** a webes ügyfél engedélyezve. Ha nincs engedélyezve, egy `unsupported_response` hiba adja vissza: **"response_type" bemeneti paraméter megadott értéke nem engedélyezett ennél az ügyfélnél. Várt érték "code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Az implicit folyamat használatával bejelentkezés teszteléséhez kattintson <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Miután bejelentkezett, a böngésző át kell irányítani `https://localhost/myapp/` együtt egy `id_token` címet a címsorba.
> 

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` | szükséges |A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints). |
| `client_id` | szükséges |Az alkalmazás azonosítója, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazás hozzárendelve. |
| `response_type` | szükséges |Tartalmaznia kell `id_token` OpenID Connect bejelentkezhet. A response_type is tartalmazhat `token`. Használatával `token` itt lehetővé teszi az alkalmazások a kapott hozzáférési jogkivonat azonnal a hitelesítési végpontra ne kelljen egy második kérést a hitelesítési végpontra. Ha használja a `token` response_type, a `scope` paraméternek tartalmaznia kell egy hatókör, melyik erőforrást kell kiállítani token jelzi. |
| `redirect_uri` | Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat redirect_uri tulajdonsága. Pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve azt az URL-kódolású kell lennie. |
| `scope` | szükséges |Hatókörök szóközzel elválasztott listáját. Az OpenID Connect, tartalmaznia kell a hatókör `openid`, amelyet a rendszer lefordítja arra a jóváhagyási felhasználói felület a "Bejelentkezés" engedélyt. Szükség esetén is érdemes felvenni a `email` vagy `profile` [hatókörök](v2-permissions-and-consent.md) a további felhasználói adatokhoz való hozzáférést. A különböző erőforrások hozzájárulás kérése a kéréshez más hatókörök is. |
| `response_mode` | választható |Meghatározza a létrejövő jogkivonat vissza küldhet az alkalmazáshoz használandó módszert. Az alapértelmezett lekérdezése egy hozzáférési jogkivonatot, de az töredék, ha a kérelemben id_token. |
| `state` | Ajánlott |A kérésben is a token válaszban visszaadott érték. Bármilyen tartalmat, akinél karakterlánc lehet. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások](https://tools.ietf.org/html/rfc6749#section-10.12). Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet is szolgál. |
| `nonce` | szükséges |A kérésben, az eredményül kapott id_token jogcímként szerepeljenek, az alkalmazás által generált érték. Az alkalmazás ezután ellenőrizheti ezt az értéket ismétlésének támadások számának csökkentése érdekében. Az érték általában véletlenszerű, egyedi karakterlánc, amely a kérés eredetének azonosítására használhatók. Csak akkor szükséges, ha id_token van szükség. |
| `prompt` | választható |Azt jelzi, hogy milyen típusú felhasználói beavatkozás szükséges. Jelenleg csak érvényes értékei a "bejelentkezés", "none", "select_account", és a "jóváhagyás". `prompt=login` a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem negating egyszeri bejelentkezéses kényszeríti. `prompt=none` Ellenkező – biztosítja, hogy a felhasználó el minden olyan interaktív kérdés nem egyike. Ha a kérés nem lehet végrehajtani csendes egyszeri bejelentkezéses keresztül, a v2.0-végpont hibát adnak vissza. `prompt=select_account` a felhasználó küld egy fiókválasztó ahol az összes megjegyzett a munkamenet-fiókkal fog megjelenni. `prompt=consent` Elindítja az OAuth-hozzájárulási párbeszédpanel kéri a felhasználót, hogy az alkalmazás engedélyeket, a felhasználó bejelentkezése után. |
| `login_hint`  |választható |Segítségével előre töltse ki a felhasználónév, e-mail-cím mező, a bejelentkezési oldal a felhasználó számára, ha ismeri a kívánt időben felhasználóneve. Alkalmazások gyakran ismételt hitelesítés kellene már kinyert a felhasználónevet egy korábbi bejelentkezési használata során fogja használni ezt a paramétert a `preferred_username` jogcím.|
| `domain_hint` | választható |Lehetnek `consumers` vagy `organizations`. Ha tartalmazza, azt kihagyja az e-mail-alapú felderítési folyamat, hogy a felhasználó végighalad a 2.0-s verziójú bejelentkezési lapra, és a egy némileg több zökkenőmentes felhasználói élményt. Gyakran alkalmazásokat fogja használni ezt a paramétert ismételt hitelesítés során oly módon, a `tid` a id_token származó jogcímek. Ha a `tid` jogcím értéke `9188040d-6c67-4c5b-b112-36a304b66dad` használja (a Microsoft Account fogyasztói bérlő), `domain_hint=consumers`. Ellenkező esetben használhatja `domain_hint=organizations` újbóli hitelesítés során. |

Ezen a ponton a felhasználó kell adnia megadják hitelesítő adataikat, és a hitelesítés végrehajtásához. A v2.0-végpontra is biztosítja, hogy a felhasználó hozzájárult a megadott engedélyeket a `scope` lekérdezési paraméter. Ha a felhasználó hozzájárult **nincs** ezeket az engedélyeket a rendszer rákérdez, a felhasználó beleegyezését, a szükséges engedélyekkel. További információ: [engedélyek, beleegyezése és több-bérlős alkalmazások](v2-permissions-and-consent.md).

A felhasználó végzi a hitelesítést, és engedélyezi a jóváhagyás után a v2.0-végpont választ küld az alkalmazáshoz, a kijelzett `redirect_uri`, a megadott metódussal a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz

A sikeres válasz használatával `response_mode=fragment` és `response_type=id_token+token` (a sortörések az olvashatóság érdekében) a következőképpen néz:

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
| `access_token` |Belefoglalt if `response_type` tartalmaz `token`. A hozzáférési jogkivonatot, amely az alkalmazás kéri, ebben az esetben a Microsoft Graph. A hozzáférési jogkivonat nem szabad dekódolni vagy más módon megvizsgálni, azt olyan átlátszatlan karakterlánc kell kezelni. |
| `token_type` |Belefoglalt if `response_type` tartalmaz `token`. Mindig `Bearer`. |
| `expires_in`|Belefoglalt if `response_type` tartalmaz `token`. Azt jelzi, hogy a jogkivonat érvényes, a gyorsítótárazás célokra másodpercek számát. |
| `scope` |Belefoglalt if `response_type` tartalmaz `token`. Azt jelzi, hogy a hatókörök, amelyhez a access_token lesz érvényes. Előfordulhat, hogy tartalmazza az összes a hatókörök kért, ha nem voltak érvényes a felhasználó (esetén csak AAD hatókörök, ha egy személyes fiók használatával jelentkezzen be a kért). |
| `id_token` | Egy aláírt JSON webes jogkivonat (JWT). Az alkalmazásnak a bejelentkezett felhasználóval kapcsolatos információkat a token a szegmensek is dekódol. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. Id_tokens kapcsolatos további információkért lásd: a [ `id_token reference` ](id-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` a kért hatókörhöz. |
| `state` |Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak. |

#### <a name="error-response"></a>Hiba történt a válasz

Hibaválaszok is elküldheti az `redirect_uri` , az alkalmazás képes kezelni őket megfelelően:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| `error_description` |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

## <a name="validate-the-idtoken"></a>A id_token ellenőrzése

Id_token érkező azonban nem hitelesíti a felhasználót; kell érvényesíteni az aláírást a id_token, és ellenőrizze az alkalmazás követelményei alapján a jogcímeket. Használja a v2.0-végpont [JSON webes jogkivonatainak (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és a nyilvános kulcsú hitelesítésen jogkivonatok aláírásához, és ellenőrizze, hogy azok érvényesek.

Ha szeretné ellenőrizni a `id_token` ügyfél kódot, de általános gyakorlat az, hogy küldjön a `id_token` háttérkiszolgálóhoz, és végezze el az érvényesítési hiba. A id_token aláírásának ellenőrzése után, ha nincsenek néhány jogcímek ellenőrzéséhez meg kell adni. Lásd: a [ `id_token` referencia](id-tokens.md) olvashat többek között [jogkivonatok érvényességének](id-tokens.md#validating-an-idtoken) és [aláírókulcs kapcsolatos fontos információkat](active-directory-signing-key-rollover.md). Javasoljuk, hogy a jogkivonatok használata a tár elemzés és ellenőrzése – nincs legalább egy elérhető a legtöbb nyelvekhez és platformokhoz.

Érdemes ellenőrizni a forgatókönyvtől függően további jogcímek is. Néhány gyakori ellenőrzések a következők:

* Annak biztosítása, a felhasználó és szervezet regisztrált az alkalmazáshoz.
* Annak biztosítása, a felhasználó nem rendelkezik megfelelő engedélyezési/jogosultsággal.
* Egy bizonyos hitelesítés erőssége biztosító történt, például többtényezős hitelesítést.

Teljesen ellenőrzése a id_token, után megkezdheti a felhasználói munkamenetet, és a jogcímek használata a id_token az információkat kaphat az alkalmazás a felhasználó. Ez az információ használható megjelenített, a rekordokat, a személyre szabása, stb.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonat beszerzése

Most, hogy a felhasználó már bejelentkezett az egyoldalas alkalmazás, kérheti a hívó webes API-kat, mint például az Azure AD által védett hozzáférési jogkivonatok az [Microsoft Graph](https://developer.microsoft.com/graph). Akkor is, ha már kapott segítségével a `token` response_type, ez a módszer használatával szerzi be a további erőforrások jogkivonatokat nélkül átirányítja a felhasználót, hogy jelentkezzen be újra.

A normál OpenID Connect vagy OAuth-folyamat ehhez egy kérelmet, így a 2.0-s verziójú `/token` végpont. A v2.0-végpont azonban nem támogatja CORS-kérések, így AJAX hívások és frissítési jogkivonatok kívül esik a kérdést. Rejtett iframe az implicit folyamatot használhatja inkább más webes API-k új tokenekhez: 

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

A lekérdezési paramétereket az URL-címben a részletekért lásd: [a bejelentkezési kérelem küldése](#send-the-sign-in-request).

> [!TIP]
> Próbálja ki a másolás és beillesztés a kérést egy böngészőlap alább! (Ne felejtse el lecserélni a `domain_hint` és a `login_hint` értékek a felhasználó a megfelelő értékekkel)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint=consumers-or-organizations&login_hint=your-username`
>

Köszönhetően a `prompt=none` paramétert, a kérelem vagy sikeres vagy meghiúsul azonnal, és térjen vissza az alkalmazást. Sikeres választ küld az alkalmazást a jelzett `redirect_uri`, a megadott metódussal a `response_mode` paraméter.

#### <a name="successful-response"></a>A sikeres válasz

A sikeres válasz használatával `response_mode=fragment` következőhöz hasonló:

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
| `access_token` |Belefoglalt if `response_type` tartalmaz `token`. A hozzáférési jogkivonatot, amely az alkalmazás kéri, ebben az esetben a Microsoft Graph. A hozzáférési jogkivonat nem szabad dekódolni vagy más módon megvizsgálni, azt olyan átlátszatlan karakterlánc kell kezelni. |
| `token_type` | Mindig `Bearer`. |
| `expires_in` | Azt jelzi, hogy a jogkivonat érvényes, a gyorsítótárazás célokra másodpercek számát. |
| `scope` | Azt jelzi, hogy a hatókörök, amelyhez a access_token lesz érvényes. Előfordulhat, hogy tartalmazza az összes a hatókörök kért, ha nem voltak érvényes a felhasználó (esetén csak AAD hatókörök, ha egy személyes fiók használatával jelentkezzen be a kért). |
| `id_token` | Egy aláírt JSON webes jogkivonat (JWT). Belefoglalt if `response_type` tartalmaz `id_token`. Az alkalmazásnak a bejelentkezett felhasználóval kapcsolatos információkat a token a szegmensek is dekódol. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. Id_tokens kapcsolatos további információkért lásd: a [ `id_token` referencia](id-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` a kért hatókörhöz. |
| `state` |Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás ellenőrizze, hogy a kérés- és állapot értékei azonosak. |


#### <a name="error-response"></a>Hiba történt a válasz

Hibaválaszok is elküldheti az `redirect_uri` , az alkalmazás képes kezelni őket megfelelően. Abban az esetben, `prompt=none`, várt hiba történt a következő lesz:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| `error_description` |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |

Ez a hiba az iframe-kérelmet kap, ha a felhasználó interaktív jelentkezzen be újra egy új token lekérése. Ha szeretné, ebben az esetben a kiválasztott módon értelme, az alkalmazás kezelni.

## <a name="validating-access-tokens"></a>Hozzáférési jogkivonatok érvényességének

Miután egy access_token kap, ügyeljen arra, hogy érvényesíteni az aláírást a jogkivonatot, valamint a következő jogcímeket. Dönthet úgy is, további jogcímek alapján a forgatókönyv érvényesítéséhez. 

* **célközönség** jogcímet, győződjön meg arról, hogy a jogkivonat kell fordítani az alkalmazás szándéka
* **kiállító** jogcímet, győződjön meg arról, hogy a jogkivonat bocsátotta az alkalmazáshoz a v2.0-végpont
* **nem előtt** és **lejárati idő** jogcímekre, győződjön meg arról, hogy a jogkivonat nem járt le

A hozzáférési jogkivonatban található jogcímek kapcsolatos további információkért lásd: a [hozzáférési jogkivonat-referencia](access-tokens.md)

## <a name="refreshing-tokens"></a>Token frissítése

Az implicit engedélyezés nem tartalmaz frissítési biztonsági jogkivonat. Mindkét `id_token`s és `access_token`s le fog járni, miután rendszeres időközönként jogkivonatok olyan rövid idő alatt, így az alkalmazás frissítéséhez ezek elő kell készíteni. Frissítési jogkivonat mindkét típusú, a rejtett iframe kérésben parancssorai használatával is elvégezheti a `prompt=none` paraméter segítségével szabályozhatja az Azure AD működését. Ha szeretne kapni egy új `id_token`, ügyeljen arra, hogy `response_type=id_token` és `scope=openid`, valamint egy `nonce` paraméter.

## <a name="send-a-sign-out-request"></a>A kijelentkezési kérés küldése

A OpenIdConnect `end_session_endpoint` lehetővé teszi az alkalmazás egy kérelmet küld a v2.0-végpont egy felhasználói munkamenetet, és törölje a cookie-k által a v2.0-végpontra. Egy felhasználó egy webalkalmazás ki teljes mértékben bejelentkezni az alkalmazás kell a saját munkamenet befejezése a felhasználó (általában egy token gyorsítótár kiürítése vagy eldobja a cookie-k) által, és irányíthatja át a böngészőben:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` |szükséges |A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Ajánlott | Az URL-cím, amely a felhasználót a rendszer visszalépteti kijelentkezési befejezése után. Ezt az értéket meg kell egyeznie az átirányítási URI-k az alkalmazás regisztrálva egyikét. Nem tartalmazza az, ha a felhasználó által a v2.0-végpontra egy általános üzenet fog megjelenni. |

## <a name="next-steps"></a>További lépések

* Keresztül lépjen a [MSAL JS minták](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) kódolási megkezdéséhez.
