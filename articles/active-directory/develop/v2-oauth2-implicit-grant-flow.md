---
title: OAuth 2.0 implicit támogatási folyamat - Microsoft identity platform | Azure
description: Biztonságos egyoldalas alkalmazások a Microsoft identity platform implicit folyamat használatával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b946ab6157ba63213a4c140221d36f231aa62f0d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535842"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft-identitásplatform és implicit támogatási folyamat

A Microsoft identity platform végpontjával bejelentkezhet a felhasználókegyoldalas alkalmazásaidba a Microsoft személyes és munkahelyi vagy iskolai fiókjaival. Az egyoldalas és más JavaScript-alkalmazások, amelyek elsősorban a böngészőben futnak, néhány érdekes kihívással szembesülnek a hitelesítés során:

* Ezeknek az alkalmazásoknak a biztonsági jellemzői jelentősen eltérnek a hagyományos kiszolgálóalapú webalkalmazásoktól.
* Számos engedélyezési kiszolgáló és identitásszolgáltató nem támogatja a CORS-kérelmeket.
* A teljes oldalas böngésző átirányítása az alkalmazástól különösen invazívvá válik a felhasználói élmény számára.

Ezeknél az alkalmazásoknál (Angular, Ember.js, React.js és így tovább) a Microsoft identity platform támogatja az OAuth 2.0 Implicit grant folyamatot. Az implicit folyamat leírása az [OAuth 2.0 specifikációban található.](https://tools.ietf.org/html/rfc6749#section-4.2) Elsődleges előnye, hogy lehetővé teszi, hogy az alkalmazás a háttérkiszolgáló hitelesítő adatok cseréje nélkül getta le a jogkivonatokat a Microsoft identitásplatformról. Ez lehetővé teszi, hogy az alkalmazás jelentkezzen be a felhasználó, a munkamenet karbantartása, és tokenek más webes API-k mind az ügyfél JavaScript-kód. Van néhány fontos biztonsági szempont, amelyet figyelembe kell venni, amikor az implicit folyamatot kifejezetten az [ügyfél](https://tools.ietf.org/html/rfc6749#section-10.3) és a [felhasználó megszemélyesítése](https://tools.ietf.org/html/rfc6749#section-10.3)körül használja.

Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen.  Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

Ha azonban nem szeretne tárat használni az egyoldalas alkalmazásban, és saját maga is protokollüzeneteket szeretne küldeni, kövesse az alábbi általános lépéseket.

> [!NOTE]
> Nem minden Azure Active Directory (Azure AD) forgatókönyvek és funkciók a Microsoft identity platform végpont támogatja. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Megfelelő forgatókönyvek az OAuth2 implicit támogatáshoz

Az OAuth2 specifikáció kijelenti, hogy az implicit támogatást úgy alakították ki, hogy lehetővé tegye a felhasználói ügynök alkalmazásokat – azaz a böngészőn belül végrehajtó JavaScript-alkalmazásokat. Az ilyen alkalmazások meghatározó jellemzője, hogy a JavaScript-kódot a kiszolgáló erőforrásainak (általában egy webes API) elérésére és az alkalmazás felhasználói élményének ennek megfelelő frissítésére használják. Gondoljon olyan alkalmazásokra, mint a Gmail vagy az Outlook Web Access: amikor kiválaszt egy üzenetet a beérkezett üzenetek mappából, csak az üzenetvizualizációpanel változik az új kijelölés megjelenítéséhez, míg a lap többi része változatlan marad. Ez a jellemző ellentétben áll a hagyományos átirányítási alapú webalkalmazásokkal, ahol minden felhasználói beavatkozás egy teljes oldal visszaadást és az új kiszolgálóválasz teljes oldalas megjelenítését eredményezi.

Alkalmazások, amelyek a JavaScript-alapú megközelítést a szélsőséges nevezzük egyoldalas alkalmazások, vagy SPA-k. Az ötlet az, hogy ezek az alkalmazások csak egy kezdeti HTML oldalt és a kapcsolódó JavaScriptet szolgálnak ki, az összes további interakciót a JavaScript-en keresztül végrehajtott webes API-hívások vezérlik. Azonban a hibrid megközelítések, ahol az alkalmazás többnyire postback-vezérelt, de alkalmi JS-hívásokat hajt végre, nem ritkák – az implicit folyamat használatának megvitatása azok számára is fontos.

Az átirányítási alapú alkalmazások általában cookie-k segítségével biztosítják kéréseiket, azonban ez a megközelítés nem működik olyan jól a JavaScript-alkalmazások esetében. A cookie-k csak azzal a tartománnyal szemben működnek, amelyhez létrehozták őket, míg a JavaScript-hívások más tartományokba irányulhatnak. Valójában ez gyakran előfordul: gondoljon a Microsoft Graph API-t, az Office API-t, az Azure API-t hivatkozó alkalmazásokra – amelyek mindegyike az on kívül található, ahonnan az alkalmazást kiszolgálják. A JavaScript-alkalmazások növekvő tendenciája, hogy egyáltalán nincs háttér, 100%-ban harmadik fél webes API-kra támaszkodva valósítja meg üzleti funkciójukat.

Jelenleg a webes API-k hívásainak védelmének előnyben részesített módja az OAuth2-tulajdonosi jogkivonat-megközelítés használata, ahol minden hívást egy OAuth2 hozzáférési jogkivonat kísér. A webes API megvizsgálja a bejövő hozzáférési jogkivonatot, és ha megtalálja a szükséges hatóköröket, hozzáférést biztosít a kért művelethez. Az implicit folyamat kényelmes mechanizmust biztosít a JavaScript-alkalmazások számára, hogy hozzáférési jogkivonatokat szerezzenek egy webes API-hoz, számos előnyt kínálva a cookie-k tekintetében:

* A tokenek megbízhatóan beszerezhetők a kereszteredlési hívások szükségessége nélkül – az átirányítási URI kötelező regisztrálása, amelyre a tokenek visszatérési garanciák arra, hogy a tokenek nem kerülnek kiitérésre
* A JavaScript-alkalmazások annyi hozzáférési jogkivonatot szerezhetnek be, amennyire szükségük van, annyi webes API-hoz, amennyit megcéloznak – a tartományok korlátozása nélkül
* A HTML5-funkciók, például a munkamenet vagy a helyi tárolás teljes körű felügyeletet biztosítanak a tokengyorsítótárazás és az élettartam-kezelés felett, míg a cookie-k kezelése nem átlátszatlan az alkalmazás számára
* A hozzáférési jogkivonatok nem érzékenyek a helyek közötti kérelemhamisítási (CSRF) támadásokra

Az implicit támogatási folyamat nem ad ki frissítési jogkivonatokat, főként biztonsági okokból. A frissítési jogkivonat nem olyan szűken, mint a hozzáférési jogkivonatok hatóköre, így sokkal több energiát biztosít, így sokkal több kárt okoz, ha kiszivárogna. Az implicit folyamat jogkivonatok kézbesítése az URL-cím, ezért a lehallgatás kockázata nagyobb, mint az engedélyezési kód támogatás.

Azonban egy JavaScript-alkalmazás rendelkezik egy másik mechanizmus áll a rendelkezésére a hozzáférési jogkivonatok megújítása anélkül, hogy ismételten kéri a felhasználó hitelesítő adatokat. Az alkalmazás egy rejtett iframe-et használhat új jogkivonat-kérelmek végrehajtásához az Azure AD engedélyezési végpontja ellen: mindaddig, amíg a böngésző még rendelkezik egy aktív munkamenettel (olvassa el: rendelkezik egy munkamenet-cookie-val) az Azure AD-tartományellen, a hitelesítési kérelem sikeresen elvégezhető felhasználói beavatkozás nélkül.

Ez a modell lehetővé teszi a JavaScript-alkalmazás számára, hogy önállóan megújítsa a hozzáférési jogkivonatokat, és még újakat is szerezzen be egy új API-hoz (feltéve, hogy a felhasználó korábban hozzájárult hozzájuk). Ezzel elkerülhető a nagy értékű műtermék, például egy frissítési jogkivonat beszerzésének, karbantartásának és védelmének további terhe. A rendszermászolat, amely lehetővé teszi a csendes megújítás, az Azure AD munkamenet cookie-t, az alkalmazáson kívül kezeli. Egy másik előnye ennek a megközelítésnek, hogy a felhasználó kijelentkezhet az Azure AD-ből az Azure AD-be bejelentkezett alkalmazások bármelyikével, amelyek a böngésző lapok bármelyikén futnak. Ennek eredménye az Azure AD munkamenet-cookie törlése, és a JavaScript-alkalmazás automatikusan elveszíti a kijelentkezett felhasználó jogkivonatainak megújításának lehetőségét.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Az implicit támogatás megfelel az alkalmazásomnak?

Az implicit támogatás több kockázatot jelent, mint más támogatások, és a területek, amelyeket figyelembe kell vennie, jól dokumentáltak (például a [hozzáférési jogkivonattal való visszaélés az erőforrás tulajdonosának megszemélyesítésére az Implicit folyamatban][OAuth2-Spec-Implicit-Misuse] és [az OAuth 2.0 fenyegetési modell és biztonsági megfontolások).][OAuth2-Threat-Model-And-Security-Implications] A magasabb kockázati profil azonban nagyrészt annak a ténynek köszönhető, hogy az aktív kódot végrehajtó alkalmazásokat egy távoli erőforrás szolgálja ki a böngészőbe. Ha egy SPA architektúrát tervez, nincs háttér-összetevő, vagy javascripten keresztül webes API-t kíván meghívni, ajánlott az implicit folyamat használata a tokenek megszerzéséhez.

Ha az alkalmazás egy natív ügyfél, az implicit folyamat nem egy nagy roham. Az Azure AD-munkamenet cookie-k hiánya egy natív ügyfél környezetében megfosztja az alkalmazást a hosszú élettartamú munkamenet fenntartásának eszközeitől. Ami azt jelenti, hogy az alkalmazás ismételten kéri a felhasználót az új erőforrások hozzáférési jogkivonatának beszerzésekénte.

Ha olyan webalkalmazást fejleszt, amely háttérrendszert tartalmaz, és egy API-t használ fel a háttérkódból, az implicit folyamat sem megfelelő. Más támogatások sokkal nagyobb hatalmat adnak. Például az OAuth2 ügyfél hitelesítő adatok megadása lehetővé teszi, hogy a jogkivonatok, amelyek tükrözik az alkalmazáshoz rendelt engedélyeket, szemben a felhasználói delegálások. Ez azt jelenti, hogy az ügyfél képes az erőforrásokhoz való programozott hozzáférés fenntartására akkor is, ha a felhasználó nem vesz részt aktívan egy munkamenetben, és így tovább. Nem csak, hogy, de az ilyen támogatások nagyobb biztonsági garanciákat. Például a hozzáférési jogkivonatok soha nem haladnak át a felhasználói böngészőn keresztül, nem kockáztatják a mentést a böngésző előzményeiközött, és így tovább. Az ügyfélalkalmazás is végrehajthat erős hitelesítést, amikor jogkivonatot kér.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Protokolldiagram

Az alábbi ábra bemutatja, hogyan néz ki a teljes implicit bejelentkezési folyamat, és az alábbi szakaszok részletesebben ismertetik az egyes lépéseket.

![Az implicit bejelentkezési folyamatot bemutató diagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>A bejelentkezési kérelem elküldése

Először írja alá a felhasználót az alkalmazásba, küldhet [openId connect](v2-protocols-oidc.md) hitelesítési kérelmet, és kérjen egy `id_token` a Microsoft identity platform végpont.

> [!IMPORTANT]
> Egy azonosító jogkivonat és/vagy egy hozzáférési jogkivonat sikeres igényléséhez az alkalmazás regisztrációja az [Azure Portalon – Az alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapnak engedélyeznie kell a megfelelő implicit támogatási folyamatot, ha kiválasztja az **azonosító jogkivonatokat** és.vagy **a hozzáférési jogkivonatokat** az **Implicit támogatási** szakaszban. Ha nincs engedélyezve, `unsupported_response` egy hiba lesz visszaadva: **A megadott érték a bemeneti paraméter "response_type" nem engedélyezett az ügyfél. A várt érték a "kód"**

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
> Ha az implicit folyamat használatával szeretné tesztelni a bejelentkezést, kattintson a <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..gombra.</a> A bejelentkezés után a böngészőt át `https://localhost/myapp/` kell `id_token` irányítani egy címsorba.
>

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` | kötelező |A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További részletekért olvassa el [a protokoll alapjai című témakört.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | kötelező | Az alkalmazás (ügyfél) azonosítója, amely az [Azure Portal – App regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap az alkalmazáshoz rendelt. |
| `response_type` | kötelező |`id_token` Tartalmaznia kell az OpenID Connect bejelentkezéshez. Ez magában foglalhatja `token`a response_type is . Az `token` itt használatával az alkalmazás azonnal kap egy hozzáférési jogkivonatot az engedélyezési végpontról anélkül, hogy egy második kérést kellene benyújtania az engedélyezési végponthoz. Ha a `token` response_type használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely jelzi, hogy melyik erőforráshoz adja ki a jogkivonatot (például user.read a Microsoft Graph-on).  |
| `redirect_uri` | Ajánlott |Az alkalmazás redirect_uri, ahol az alkalmazás elküldheti és fogadhatja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_uris egyikével, kivéve, hogy url-kódolni kell. |
| `scope` | kötelező |A [hatókörök](v2-permissions-and-consent.md)térelválasztott listája . Az OpenID Connect (id_tokens esetén `openid`tartalmaznia kell a hatókört, amely a hozzájárulási felhasználói felület "Bejelentkezés" engedélyére fordítja le. Tetszés szerint a `email` további `profile` felhasználói adatokhoz való hozzáférés hatókörét is meg szeretné adni. Más hatóköröket is felvehet ebben a kérelemben a különböző erőforrások hozzájárulásának kéréséhez, ha hozzáférési jogkivonatot kér. |
| `response_mode` | választható |Megadja azt a módszert, amelyet az eredményül kapott jogkivonat nak az alkalmazásnak való visszaküldéséhez kell használni. Alapértelmezés szerint csak egy hozzáférési jogkivonatot kell lekérdezni, de töredék, ha a kérelem id_token tartalmaz. |
| `state` | Ajánlott |A kérelemben szereplő érték, amely a jogkivonat-válaszban is vissza kerül. Ez lehet egy sor bármilyen tartalmat, amit szeretne. A véletlenszerűen generált egyedi értéket általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)használják. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak. |
| `nonce` | kötelező |Az alkalmazás által létrehozott kérelemben szereplő érték, amely jogcímként szerepelni fog az eredményül kapott id_token. Az alkalmazás ezután ellenőrizheti ezt az értéket a token-visszajátszási támadások csökkentése érdekében. Az érték általában egy randomizált, egyedi karakterlánc, amely a kérelem eredetének azonosítására használható. Csak akkor szükséges, ha id_token kér. |
| `prompt` | választható |A szükséges felhasználói beavatkozás típusát jelzi. Jelenleg csak a "login", a "none", a "select_account" és a "consent" értékek érvényesek. `prompt=login`arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kéréshez, és negating egyszeri bejelentkezés. `prompt=none`az ellenkezője - ez biztosítja, hogy a felhasználó nem jelenik meg semmilyen interaktív prompt egyáltalán. Ha a kérés nem hajtható végre csendben egyszeri bejelentkezéssel, a Microsoft identity platform végpont jain egy hiba. `prompt=select_account`elküldi a felhasználót egy fiókválasztónak, ahol a munkamenetben megjegyzett összes fiók megjelenik. `prompt=consent`a felhasználó bejelentkezése után elindítja az OAuth hozzájárulási párbeszédpanelt, és arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint`  |választható |A felhasználó bejelentkezési lapjának felhasználónév/e-mail cím mezőjének előzetes kitöltésére használható, ha előre ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután `preferred_username` már kinyerték a felhasználónevet egy korábbi bejelentkezési jogcím használatával.|
| `domain_hint` | választható |Ha tartalmazza, akkor kihagyja az e-mail-alapú felderítési folyamat, amely a felhasználó megy keresztül a bejelentkezési oldalon, ami egy kicsit egyszerűbb felhasználói élményt. Ez gyakran használják a Line of Business alkalmazások, amelyek egyetlen bérlőben működnek, ahol egy adott bérlőn belül tartománynevet biztosítanak.  Ez továbbítja a felhasználót az adott bérlő összevonási szolgáltatójának.  Vegye figyelembe, hogy ez megakadályozza, hogy a vendégek bejelentkezzenek az alkalmazásba.  |

Ezen a ponton a rendszer felkéri a felhasználót, hogy adja meg hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft identity platform végpontja azt is biztosítja, hogy a `scope` felhasználó hozzájárult a lekérdezési paraméterben megjelölt engedélyekhez. Ha a felhasználó hozzájárult ezen engedélyek **egyikébe sem,** meg fogja kérni a felhasználót, hogy járuljon hozzá a szükséges engedélyekhez. További információ: [engedélyek, beleegyezések és több-bérlős alkalmazások.](v2-permissions-and-consent.md)

Miután a felhasználó hitelesíti magát, és megadja a jóváhagyást, a Microsoft `redirect_uri`identity platform végpont javallott, válasz az alkalmazás a megadott , a `response_mode` paraméterben megadott módszerrel.

#### <a name="successful-response"></a>Sikeres válasz

A sikeres `response_mode=fragment` válasz `response_type=id_token+token` segítségével, és úgy néz ki, mint a következő (a sortörések az olvashatóság):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |Benne `response_type` van, ha tartalmazza `token`a . Az alkalmazás által kért hozzáférési jogkivonat. A hozzáférési jogkivonatot nem szabad dekódolni vagy más módon ellenőrizni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` |Benne `response_type` van, ha tartalmazza `token`a . Mindig is `Bearer`az lesz. |
| `expires_in`|Benne `response_type` van, ha tartalmazza `token`a . Azt jelzi, hogy a token hány másodpercig érvényes gyorsítótárazási célokra. |
| `scope` |Benne `response_type` van, ha tartalmazza `token`a . Azt a hatókört (hatóköreit) jelzi, amelyekre a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókörök, ha azok nem voltak alkalmazhatók a felhasználóra (abban az esetben, ha az Azure AD-csak hatókörök kért, ha egy személyes fiók bejelentkezéshez használt). |
| `id_token` | Egy aláírt JSON Web Token (JWT). Az alkalmazás dekódolhatja a token szegmenseit, hogy információt kérjen a bejelentkezett felhasználóról. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti azokat, de nem támaszkodhat rájuk semmilyen engedélyezési vagy biztonsági határesetén. A id_tokens kapcsolatos további [`id_token reference`](id-tokens.md)tudnivalókat a . <br> **Megjegyzés:** Csak akkor `openid` adják meg, ha a hatókört kérték. |
| `state` |Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

#### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető a, `redirect_uri` így az alkalmazás tudja kezelni őket megfelelően:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="getting-access-tokens-silently-in-the-background"></a>A hozzáférési jogkivonatok csendes beszerzése a háttérben

Most, hogy aláírta a felhasználót az egyoldalas alkalmazásba, csendben beszerezheti a Microsoft identity platform által védett webes API-k, például a [Microsoft Graph](https://developer.microsoft.com/graph)által biztosított webes API-k hívásához szükséges hozzáférési jogkivonatokat. Még akkor is, ha `token` már kapott egy jogkivonatot a response_type használatával, ezzel a módszerrel további erőforrásoktokenek beszerzésére anélkül, hogy át kell irányítania a felhasználót, hogy jelentkezzen be újra.

A normál OpenID Connect/OAuth folyamat, ezt a Microsoft identity platform `/token` végpontra irányuló kéréssel. A Microsoft identity platform végpontazonban nem támogatja a CORS-kérelmeket, így az AJAX-hívások lekérése és frissítése jogkivonatokat nem kizárt. Ehelyett használhatja az implicit folyamat egy rejtett iframe-ben, hogy új jogkivonatok at más webes API-k:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Az URL-cím lekérdezési paramétereiről a Bejelentkezési kérelem küldése című témakörben talál [részleteket.](#send-the-sign-in-request)

> [!TIP]
> Próbálja másolni & beillesztés az alábbi kérelmet egy böngésző fülre! (Ne felejtse el `login_hint` lecserélni az értékeket a felhasználó nak megfelelő értékre)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

A paraméternek köszönhetően ez a `prompt=none` kérés vagy sikeres lesz, vagy azonnal sikertelen lesz, és visszatér az alkalmazáshoz. A sikeres választ a megadott `redirect_uri`módon küldi el az alkalmazásnak a `response_mode` paraméterben megadott módszerrel.

#### <a name="successful-response"></a>Sikeres válasz

A sikeres `response_mode=fragment` válasz a következőképpen néz ki:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |Benne `response_type` van, ha tartalmazza `token`a . Az alkalmazás által kért hozzáférési jogkivonat, ebben az esetben a Microsoft Graph esetében. A hozzáférési jogkivonatot nem szabad dekódolni vagy más módon ellenőrizni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` | Mindig is `Bearer`az lesz. |
| `expires_in` | Azt jelzi, hogy a token hány másodpercig érvényes gyorsítótárazási célokra. |
| `scope` | Azt a hatókört (hatóköreit) jelzi, amelyekre a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókörök, ha azok nem voltak alkalmazhatók a felhasználóra (abban az esetben, ha az Azure AD-csak hatókörök kért, ha egy személyes fiók bejelentkezéshez használt). |
| `id_token` | Egy aláírt JSON Web Token (JWT). Benne `response_type` van, ha tartalmazza `id_token`a . Az alkalmazás dekódolhatja a token szegmenseit, hogy információt kérjen a bejelentkezett felhasználóról. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti azokat, de nem támaszkodhat rájuk semmilyen engedélyezési vagy biztonsági határesetén. A id_tokens kapcsolatos további tudnivalókat a [ `id_token` hivatkozásban talál.](id-tokens.md) <br> **Megjegyzés:** Csak akkor `openid` adják meg, ha a hatókört kérték. |
| `state` |Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban lévő állapotértékek azonosak-e. |

#### <a name="error-response"></a>Hibaválasz

Hibaválaszok is küldhető a, `redirect_uri` így az alkalmazás képes kezelni őket megfelelően. Abban az `prompt=none`esetben, a , várható hiba lesz:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| `error_description` |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibaüzenetet kapja az iframe-kérelemben, a felhasználónak újra be kell jelentkeznie az új jogkivonat lekéréséhez. Választhat, hogy kezelni ezt az ügyet bármilyen módon van értelme a kérelmet.

## <a name="refreshing-tokens"></a>Tokenek frissítése

Az implicit támogatás nem biztosít frissítési jogkivonatokat. Mind `id_token`az `access_token`s, mind a s rövid idő elteltével lejár, ezért az alkalmazásnak fel kell készülnie a tokenek rendszeres frissítésére. A token ek bármelyikének frissítéséhez ugyanazt a rejtett iframe-kérést végezheti el felülről a `prompt=none` paraméter használatával az identitásplatform viselkedésének szabályozásához. Ha `id_token`újat szeretne kapni, ügyeljen `id_token` arra, `response_type` `scope=openid`hogy a és `nonce` a és a, valamint egy paramétert használjon.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Az OpenID `end_session_endpoint` Connect lehetővé teszi, hogy az alkalmazás kérést küldjön a Microsoft identity platform végpontjára, hogy befejezhesse a felhasználó munkamenetét, és törölje a Microsoft identity platform végpontja által beállított cookie-kat. Ahhoz, hogy egy felhasználó teljes mértékben kilépjen egy webalkalmazásból, az alkalmazásnak be kell tartania a saját munkamenetét a felhasználóval (általában egy tokengyorsítótár törlésével vagy a cookie-k eldobásával), majd át kell irányítania a böngészőt a következő webhelyre:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` |kötelező |A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett `common`értékek `organizations` `consumers`a , , és a bérlői azonosítók. További részletekért olvassa el [a protokoll alapjai című témakört.](active-directory-v2-protocols.md#endpoints) |
| `post_logout_redirect_uri` | Ajánlott | Az URL-cím, amelyhez a felhasználónak vissza kell térnie a kijelentkezés befejezése után. Ennek az értéknek meg kell egyeznie az alkalmazáshoz regisztrált átirányítási URI-k egyikével. Ha nem tartalmazza, a microsoftidentit platform végpontja általános üzenetet jelenít meg a felhasználónak. |

## <a name="next-steps"></a>További lépések

* A kódolás megkezdéséhez vesszen át az [MSAL JS mintákat.](sample-v2-code.md)
