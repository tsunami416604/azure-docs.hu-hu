---
title: Egyoldalas alkalmazások biztonságossá tétele a Microsoft Identity platform implicit folyamatának használatával | Azure
description: Webalkalmazások fejlesztése a Microsoft Identity platform használatával az egylapos alkalmazások implicit folyamatának megvalósításával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab5ba4fde7469854954ed19d2e643f2b8a23f34f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193254"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform és implicit engedélyezési folyamat

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A Microsoft Identity platform végpontja segítségével a Microsofttól személyes és munkahelyi vagy iskolai fiókkal is aláírhatja a felhasználókat a saját egyoldalas alkalmazásokba. Az egyoldalas és más JavaScript-alkalmazások, amelyek elsődlegesen egy böngészőben futnak, néhány érdekes kihívást jelentenek a hitelesítés során:

* Ezeknek az alkalmazásoknak a biztonsági jellemzői jelentősen eltérnek a hagyományos kiszolgáló-alapú webalkalmazások.
* Számos engedélyezési kiszolgáló és Identitáskezelő nem támogatja a CORS kérelmeket.
* A teljes oldal böngészője átirányítja az alkalmazást az alkalmazásból, így a felhasználói élmény különösen ininvazív lehet.

Ezekhez az alkalmazásokhoz (AngularJS, ember. js, reagálás. js stb.) a Microsoft Identity platform támogatja a OAuth 2,0 implicit engedélyezési folyamatot. Az implicit folyamat leírása a [OAuth 2,0 specifikációban](https://tools.ietf.org/html/rfc6749#section-4.2)található. Elsődleges előnye, hogy lehetővé teszi, hogy az alkalmazás a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül beszerezze a jogkivonatokat a Microsoft Identity platformból. Ez lehetővé teszi az alkalmazás számára, hogy bejelentkezzen a felhasználóval, fenntartsa a munkamenetet, és más webes API-kra is lekérje az ügyfél JavaScript-kódjában található összes tokent. Néhány fontos biztonsági szempontot figyelembe kell venni, amikor az implicit folyamatot kifejezetten az [ügyfél](https://tools.ietf.org/html/rfc6749#section-10.3) -és a [felhasználói megszemélyesítést](https://tools.ietf.org/html/rfc6749#section-10.3)használja.

Ha az implicit flow és a Microsoft Identity platform használatával szeretne hitelesítést hozzáadni a JavaScript-alkalmazáshoz, javasoljuk, hogy használja a nyílt forráskódú JavaScript-függvénytárat, a [msal. js fájlt](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Ha azonban inkább nem szeretne használni egy könyvtárat az egyoldalas alkalmazásban, és saját maga is küldje a protokollok üzeneteit, kövesse az alábbi általános lépéseket.

> [!NOTE]
> Nem minden Azure Active Directory-(Azure AD-) forgatókönyvet és szolgáltatást támogat a Microsoft Identity platform végpontja. Annak megállapításához, hogy érdemes-e a Microsoft Identity platform-végpontot használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán látható, hogy a teljes implicit bejelentkezési folyamat hogyan néz ki, valamint az egyes lépéseket követő szakaszok részletesebb leírását.

![Az implicit bejelentkezési folyamatot bemutató diagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ahhoz, hogy először aláírja a felhasználót az alkalmazásba, elküldheti az [OpenID Connect](v2-protocols-oidc.md) hitelesítési kérését, és beszerezheti `id_token` a-t a Microsoft Identity platform végpontjának használatával.

> [!IMPORTANT]
> Az azonosító jogkivonat sikeres igényléséhez az alkalmazásnak a [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon való regisztrálásához megfelelően engedélyezve kell lennie az implicit engedélyezési folyamatnak, ehhez válassza a **hozzáférési tokenek** és az **azonosító** jogkivonatok lehetőséget az **implicit támogatás** alatt. szakasz. Ha nincs engedélyezve, a `unsupported_response` rendszer hibaüzenetet küld: **Az "response_type" bemeneti paraméter megadott értéke nem engedélyezett ennél az ügyfélnél. A várt érték a "code"**

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
> Az implicit folyamat használatával történő bejelentkezés teszteléséhez kattintson a elemre <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> A bejelentkezést követően a böngészőt át `https://localhost/myapp/` kell irányítani egy `id_token` -ra a címsorba.
>

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` | szükséges |A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek `common` `organizations` :,,ésbérlőiazonosítók`consumers`. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `client_id` | szükséges | Az alkalmazáshoz hozzárendelt [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal alkalmazás-(ügyfél-) azonosítója. |
| `response_type` | szükséges |Tartalmaznia `id_token` kell az OpenID Connect bejelentkezést. A response_type `token`is tartalmazhat. Az `token` itt elérhetővé teszi, hogy az alkalmazás azonnal kapjon hozzáférési jogkivonatot az engedélyező végponttól anélkül, hogy egy második kérést kellene létesítenie az engedélyezés végpontjának. Ha a `token` response_type használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely azt jelzi, hogy melyik erőforrást kell kibocsátani a jogkivonat számára. |
| `redirect_uri` | ajánlott |Az alkalmazás redirect_uri, ahol a hitelesítési válaszokat el lehet juttatni és fogadni tudja az alkalmazás. Pontosan egyeznie kell a portálon regisztrált redirect_uris, kivéve, ha az URL-címet kódolni kell. |
| `scope` | szükséges |A [hatókörök](v2-permissions-and-consent.md)szóközzel tagolt listája. Az OpenID Connect esetében tartalmaznia kell a hatókört `openid`, amely a hozzájárulási felhasználói felületen a "Bejelentkezés" engedélyre van lefordítva. Igény szerint a további felhasználói információkhoz való hozzáféréshez `profile` is érdemes lehet a `email` vagy hatóköröket is használni. A kérelemben más hatókörök is szerepelhetnek a különböző erőforrásokhoz való belefoglaláshoz. |
| `response_mode` | választható |Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. Az alapértelmezett érték egy hozzáférési jogkivonat lekérdezése, de töredék, ha a kérelem tartalmaz egy id_token. |
| `state` | ajánlott |A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| `nonce` | szükséges |Az alkalmazás által generált kérelemben szereplő érték, amely az eredményül kapott id_token lesz. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. Csak a id_token kérelmezése esetén szükséges. |
| `prompt` | választható |Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben az egyetlen érvényes érték a következők egyike: "bejelentkezhessen," None "," select_account "és" beleegyezett ". `prompt=login`kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen, és zárja be az egyszeri bejelentkezést. `prompt=none`Ez ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérdés nélkül is megjelenik. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a Microsoft Identity platform-végpont hibát ad vissza. `prompt=select_account`elküldi a felhasználót egy olyan fiók-választónak, ahol a munkamenetben megjegyezett összes fiók megjelenik. `prompt=consent`a a felhasználó bejelentkezése után elindítja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint`  |választható |A felhasználó számára a bejelentkezési oldal Felhasználónév/e-mail cím mezőjének előzetes kitöltésére is használható, ha a felhasználónevet az idő előtt ismeri. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, miután már kibontotta a felhasználónevet egy korábbi `preferred_username` bejelentkezésből a jogcím használatával.|
| `domain_hint` | választható |A `consumers` (vagy `organizations`) egyike lehet. Ha belefoglalja ezt a funkciót, a rendszer kihagyja az e-mailes felderítési folyamatot, amelyet a felhasználó a bejelentkezési oldalon áthalad, ami valamivel egyszerűbb felhasználói élményt nyújt. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, ha `tid` kinyerik a jogcímet a id_token. Ha a `tid` jogcím `9188040d-6c67-4c5b-b112-36a304b66dad` értéke (a Microsoft-fiók fogyasztói bérlője), használja `domain_hint=consumers`a következőt:. Ellenkező esetben az ismételt hitelesítés `domain_hint=organizations` során is használhatja. |

Ekkor a rendszer megkéri a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform végpontja azt is biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó beleegyezett a fenti engedélyek **egyikére sem** , a felhasználó beleegyezést kér a szükséges engedélyekkel. További információ: [engedélyek, beleegyezikés és több-bérlős alkalmazások](v2-permissions-and-consent.md).

Miután a felhasználó hitelesíti és beleegyezik, a Microsoft Identity platform végpontja az adott alkalmazásra `redirect_uri`adott választ ad vissza a `response_mode` paraméterben megadott metódus használatával.

#### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz és `response_mode=fragment` `response_type=id_token+token` a következőképpen néz ki (az olvashatóság érdekében sortöréseket használ):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |Tartalmazza, `response_type` ha `token`tartalmaz. Az alkalmazás által kért hozzáférési jogkivonat, ebben az esetben a Microsoft Graph. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` |Tartalmazza, `response_type` ha `token`tartalmaz. Mindig a `Bearer`lesz. |
| `expires_in`|Tartalmazza, `response_type` ha `token`tartalmaz. Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` |Tartalmazza, `response_type` ha `token`tartalmaz. Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. A id_tokens kapcsolatos további információkért lásd: [`id_token reference`](id-tokens.md). <br> **Megjegyzés:** Csak akkor van `openid` megadva, ha a hatókört kérték. |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A `redirect_uri` rendszer a hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="validate-the-id_token"></a>A id_token ellenőrzése

A id_token fogadása nem elegendő a felhasználó hitelesítéséhez; ellenőriznie kell a id_token's aláírását is, és ellenőriznie kell a token jogcímeit az alkalmazás követelményei alapján. A Microsoft Identity platform végpont [JSON web tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) és nyilvános kulcsú titkosítást használ a jogkivonatok aláírásához, és ellenőrzi, hogy érvényesek-e.

Dönthet úgy is, hogy érvényesíti az `id_token` ügyfél kódját, de az általános gyakorlat az, ha a- `id_token` t egy háttér-kiszolgálónak küldi, és ott hajtja végre az ellenőrzést. Miután ellenőrizte a id_token aláírását, van néhány jogcím, amelyet ellenőrizni kell. További információért lásd a [ `id_token` hivatkozást](id-tokens.md) , beleértve a [jogkivonatok érvényesítését](id-tokens.md#validating-an-id_token) és az [aláíró kulcsok átváltásával kapcsolatos fontos információkat](active-directory-signing-key-rollover.md). Azt javasoljuk, hogy használjon egy könyvtárat a tokenek elemzéséhez és érvényesítéséhez – legalább egy elérhető a legtöbb nyelven és platformon.

A forgatókönyvtől függően további jogcímeket is érdemes lehet érvényesíteni. Egyes gyakori érvényesítések a következők:

* Annak biztosítása, hogy a felhasználó/szervezet regisztrálva legyen az alkalmazásra.
* Győződjön meg arról, hogy a felhasználó rendelkezik a megfelelő engedélyekkel/jogosultságokkal.
* A hitelesítés bizonyos erősségének biztosítása, például a többtényezős hitelesítés.

Miután ellenőrizte a id_token, megkezdheti a munkamenetet a felhasználóval, és a id_token található jogcímek használatával lekérheti a felhasználóval kapcsolatos információkat az alkalmazásban. Ezek az információk a megjelenítéshez, a rekordokhoz, a személyre szabáshoz és egyebekhez is használhatók.

## <a name="get-access-tokens"></a>Hozzáférési jogkivonatok beolvasása

Most, hogy aláírta a felhasználót egy egyoldalas alkalmazásba, hozzáférési jogkivonatokat kérhet le a Microsoft Identity platform által védett webes API-k meghívásához, például a [Microsoft Graph](https://developer.microsoft.com/graph). Akkor is használhatja ezt a módszert, ha a `token` response_type használatával már kapott jogkivonatot, és nem kell átirányítani a felhasználót a bejelentkezéshez.

A normál OpenID Connect/OAuth folyamat során ezt a Microsoft Identity platform `/token` -végpontra irányuló kéréssel teheti meg. A Microsoft Identity platform végpontja azonban nem támogatja a CORS-kérelmeket, így az AJAX-hívások lekérése és frissítése a jogkivonatok alól nem áll fenn. Ehelyett a rejtett iframe-ben lévő implicit folyamattal új jogkivonatokat kérhet le más webes API-khoz: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Az URL-cím lekérdezési paramétereinek részletes ismertetését lásd: [a bejelentkezési kérelem küldése](#send-the-sign-in-request).

> [!TIP]
> Próbálja meg másolni & az alábbi kérelem beillesztése egy böngésző lapra! (Ne felejtse el lecserélni az `login_hint` értékeket a felhasználó helyes értékére)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

A `prompt=none` paraméternek köszönhetően ez a kérés sikeres vagy sikertelen lesz, és visszatér az alkalmazáshoz. A rendszer sikeres választ küld az alkalmazásnak a jelzett `redirect_uri`időpontban, a `response_mode` paraméterben megadott metódus használatával.

#### <a name="successful-response"></a>Sikeres válasz

Sikeres válasz `response_mode=fragment` a következőképpen:

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
| `access_token` |Tartalmazza, `response_type` ha `token`tartalmaz. Az alkalmazás által kért hozzáférési jogkivonat, ebben az esetben a Microsoft Graph. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` | Mindig a `Bearer`lesz. |
| `expires_in` | Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` | Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). Tartalmazza, `response_type` ha `id_token`tartalmaz. Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. A id_tokens kapcsolatos további információkért tekintse meg a [ `id_token` hivatkozást](id-tokens.md). <br> **Megjegyzés:** Csak akkor van `openid` megadva, ha a hatókört kérték. |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A `redirect_uri` rendszer a hibaüzeneteket is elküldheti, hogy az alkalmazás megfelelően tudja kezelni őket. A esetében `prompt=none`a várt hiba a következő lesz:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibát az IFRAME-kérelemben kapja, a felhasználónak interaktív módon be kell jelentkeznie egy új jogkivonat lekéréséhez. Ezt az esetet úgy is eldöntheti, hogy bármilyen módon ésszerű legyen az alkalmazáshoz.

## <a name="refreshing-tokens"></a>Tokenek frissítése

Az implicit támogatás nem biztosít frissítési jogkivonatokat. Az s és `access_token`az s is rövid idő után lejár, így az alkalmazásnak fel kell készülnie, hogy rendszeresen frissítse ezeket a jogkivonatokat. `id_token` A jogkivonat bármelyik típusának frissítéséhez ugyanezt a rejtett iframe-kérést is végrehajthatja a `prompt=none` fentiekben a paraméterrel az Identity platform viselkedésének szabályozásához. Ha újt `id_token`szeretne kapni, ne felejtse el használni `response_type=id_token` a és `scope=openid`a, valamint a `nonce` paramétert.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Az OpenID Connect `end_session_endpoint` lehetővé teszi, hogy az alkalmazás küldjön egy kérést a Microsoft Identity platform-végpontnak a felhasználó munkamenetének befejezéséhez és a Microsoft Identity platform-végpont által beállított cookie-k törléséhez. Ha egy felhasználót teljes mértékben szeretne aláírni egy webalkalmazásból, az alkalmazásnak saját munkamenetét kell végződnie a felhasználóval (általában a jogkivonat-gyorsítótár törlésével vagy a cookie-k eldobásával), majd át kell irányítani a böngészőt a következőre:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` |szükséges |A kérelem elérési útjának értékehasználhatóannakszabályozására,hogykijelentkezhetbeazalkalmazásba.`{tenant}` Az engedélyezett értékek `common` `organizations` :,,ésbérlőiazonosítók`consumers`. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | ajánlott | A felhasználó által a kijelentkezés befejeződése után visszaadott URL-cím. Ennek az értéknek meg kell egyeznie az alkalmazáshoz regisztrált átirányítási URI-k egyikével. Ha nem szerepel, a felhasználó egy általános üzenetet fog megjeleníteni a Microsoft Identity platform végpontján. |

## <a name="next-steps"></a>További lépések

* Ugorjon a [MSAL js](sample-v2-code.md) -mintákra az első lépések kódolásához.
