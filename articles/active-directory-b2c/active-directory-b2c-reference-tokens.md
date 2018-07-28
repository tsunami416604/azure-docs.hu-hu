---
title: Jogkivonat-referencia az Azure Active Directory B2C |} A Microsoft Docs
description: Az Azure Active Directory B2C a kiállított jogkivonatokban típusú
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 46e4956aa145aa082de86191ede4adaf9a43fca9
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309026"
---
# <a name="azure-ad-b2c-token-reference"></a>Az Azure AD B2C: Jogkivonat-referencia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C-vel) számos különböző típusú biztonsági jogkivonatokat bocsát ki, amint az feldolgozza azokat egyes [hitelesítési folyamat](active-directory-b2c-apps.md). Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú jogkivonat tartalma.

## <a name="types-of-tokens"></a>Jogkivonatok típusai
Az Azure AD B2C támogatja a [OAuth 2.0 engedélyezési protokollt](active-directory-b2c-reference-protocols.md), amely él mindkét hozzáférési jogkivonatokat és frissítési jogkivonatok. Is támogatja a hitelesítési és bejelentkezési keresztül [OpenID Connect](active-directory-b2c-reference-protocols.md), amely bemutatja, hogy egy harmadik típusú jogkivonat: az azonosító jogkivonat. Mindegyik token jelenik meg a tulajdonosi jogkivonatot.

A tulajdonosi jogkivonatot, amely hozzáférést biztosít a "tulajdonos" védett erőforrásokhoz való könnyű biztonsági jogkivonat. A tulajdonosi minden olyan entitás, amely a token is jelenthet. Az Azure AD B2C először hitelesítenie kell magát egy entitás előtt megkaphatja a tulajdonosi jogkivonattal. De ha a rendszer nem hajtja végre a szükséges lépéseket, biztonságos átvitelét és tárolását a jogkivonatot, azt is lehessen elfogni és egy nem kívánt entitás használja. Néhány biztonsági jogkivonatokat rendelkezik egy beépített mechanizmus megakadályozza az illetéktelen fél használja őket, de ez a mechanizmus nem rendelkezik tulajdonosi jogkivonatokat. Egy biztonságos csatornát, például a transport layer security (HTTPS) kell szállítani.

Tulajdonosi jogkivonattal továbbított kívül egy biztonságos csatornán, ha egy rosszindulatú fél használhatja a man-in-the-middle támadások beszerezni a jogkivonatot, és a védett erőforrások jogosulatlan elérésére. Biztonsági alapelveket alkalmazható, ha a tulajdonosi jogkivonatokat tárolt vagy későbbi használatra a gyorsítótárba. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat.

A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](http://tools.ietf.org/html/rfc6750).

Számos, a jogkivonatokat, amely az Azure AD B2C-t használják, mint a JSON webes jogkivonatainak (JWTs). Jwt-t a tömör, URL-cím környezetben is biztonságos módszerrel információk átvitele a két fél között. JWTs jogcímek néven információkat tartalmaznak. Ezek a helyességi feltételek vonatkozó információkat a tulajdonosi és a jogkivonat tárgyában. A jogcímeket JWTs olyan kódolt és továbbítására szerializált JSON-objektumok. Az Azure AD B2C által kiállított JWTs jelentkezett, de nincs titkosítva, mert könnyen vizsgálhatja meg a jwt-t, hibakeresés végrehajtása rajtuk a tartalmát. Több eszköz érhető el, amely ezt teheti meg, beleértve a [jwt.ms](https://jwt.ms). JWTs kapcsolatos további információkért tekintse meg [JWT-specifikációk](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Azonosító-jogkivonatokat

Egy azonosító jogkivonat egy biztonsági jogkivonatot, amely az alkalmazás fogad az Azure AD B2C formája `/authorize` és `/token` végpontok. Azonosító-jogkivonatokat jelentésekként jelennek meg [JWTs](#types-of-tokens), és használatával azonosítsa azokat a felhasználókat az alkalmazás jogcímeket tartalmaznak. Ha az azonosító-jogkivonatokat szerezte be az `/authorize` végpontot, hogy elkészültek használatával a [implicit folyamat](active-directory-b2c-reference-spa.md), gyakran használt felhasználó jelentkezik be a JavaScript-alapú webes alkalmazások. Ha azonosító-jogkivonatokat a beszerzett a `/token` végpontot, hogy elkészültek használatával a [bizalmas kódfolyamat](active-directory-b2c-reference-oidc.md), amely megőrzi a jogkivonatot a böngészőből rejtett. Ez lehetővé teszi, hogy a jogkivonat biztonságos küldött HTTP-kérések az ugyanazon alkalmazás vagy szolgáltatás két összetevői közötti kommunikációhoz. Egy azonosító jogkivonat a jogcímek tetszés szerint használhatja. Gyakran használják fiók információk megjelenítéséhez vagy egy alkalmazásban a döntést hozhasson a hozzáférésről.  

Azonosító-jogkivonatokat jelentkezett, de azok jelenleg nem titkosított. Amikor az alkalmazás- vagy API-t kap egy azonosító jogkivonat, kell [érvényesíteni az aláírást](#token-validation) igazolnia, hogy a jogkivonat hiteles-e. Az alkalmazás- vagy API is ellenőrizni kell igazolnia, azaz érvényes néhány jogcímeket. A forgatókönyv követelményeitől függően változhat, a jogcímek, az alkalmazás által ellenőrzött, de az alkalmazás kell végeznie néhány [közös jogcím ellenőrzések](#token-validation) minden forgatókönyvben.

#### <a name="sample-id-token"></a>Minta azonosító jogkivonat
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Hozzáférési jogkivonatok

Hozzáférési jogkivonat is egy biztonsági jogkivonatot, amely az alkalmazás fogad az Azure AD B2C formája `/authorize` és `/token` végpontok. Hozzáférési jogkivonatok is jelentésekként jelennek meg [JWTs](#types-of-tokens), és segítségével azonosíthatja a saját API-khoz megadott engedélyek jogcímeket tartalmaznak. Hozzáférési jogkivonatok jelentkezett, de azok jelenleg nem titkosított. Hozzáférési jogkivonatok használandó API-k és erőforrás-kiszolgálók elérésére. Ismerje meg, hogyan [hozzáférési kódok használata](active-directory-b2c-access-tokens.md). 

Az API-t egy hozzáférési jogkivonatot kap, amikor kell [érvényesíteni az aláírást](#token-validation) igazolnia, hogy a jogkivonat hiteles-e. Az API-t is ellenőrizni kell igazolnia, azaz érvényes néhány jogcímeket. A forgatókönyv követelményeitől függően változhat, a jogcímek, az alkalmazás által ellenőrzött, de az alkalmazás kell végeznie néhány [közös jogcím ellenőrzések](#token-validation) minden forgatókönyvben.

### <a name="claims-in-id-and-access-tokens"></a>Azonosító és a hozzáférési jogkivonatokban lévő jogcímek

Azure AD B2C-vel való használatakor, teljes mértékben szabályozhatják a jogkivonatok tartalmát. Konfigurálható [házirendek](active-directory-b2c-reference-policies.md) bizonyos csoportok felhasználói adatok küldése a jogcímek az alkalmazáshoz szükséges a műveletek. Ezek a jogcímek tartalmazhatják az alapvető tulajdonságok, például a felhasználó `displayName` és `emailAddress`. Emellett tartalmazhat [egyéni felhasználói attribútumok](active-directory-b2c-reference-custom-attr.md) definiálható a B2C-címtárban. Minden azonosító és a hozzáférési jogkivonatot kapott biztonsággal kapcsolatos jogcímek egy bizonyos készletét tartalmazza. Az alkalmazások használhatják ezeket a jogcímeket biztonságosan a felhasználók és a kérelmek hitelesítéséhez.

Vegye figyelembe, hogy a jogcímek, az ID jogkivonatok nem bármely adott sorrendben adja vissza. Emellett új jogcímeket költségektől azonosító-jogkivonatokat a tetszőleges időpontban. Az alkalmazás kell tilos megszüntetnie, ahogy új jogcímeket jelennek meg. Az alábbiakban szerepel az Azure AD B2C által kiadott azonosítója és a hozzáférési jogkivonatokban várhatóan jogcímeket. Bármilyen további jogcím szabályzatok határozzák meg. Eljárás, próbálja meg illessze be azt a mintául szolgáló azonosító jogkivonat jogcímeiben vizsgálatával [jwt.ms](https://jwt.ms). További részletek találhatók a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html).

| Name (Név) | Jogcím | Példaérték | Leírás |
| --- | --- | --- | --- |
| Célközönség |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Célközönség jogcím azonosítja a jogkivonat az illetékes címzett. Az Azure AD B2C-vel a célközönség megegyezik az alkalmazás Alkalmazásazonosító, az alkalmazáshoz, az alkalmazás regisztrációs portálon rendelt. Az alkalmazás kell érvényesíteni ezt az értéket, és elutasítja a tokent, ha nem felel meg. Célközönségét egyenértékű az erőforrás. |
| Kiállító |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Ez a jogcím azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatát adja vissza. Az Azure AD-címtárat, amelyben a felhasználó hitelesítési is azonosítja. Az alkalmazás ellenőrizni kell a kibocsátó jogcím győződjön meg arról, hogy a jogkivonat érkezett az Azure Active Directory v2.0-végpontra. |
| Tulajdonos: |`iat` |`1438535543` |Ez a jogcím az idő, amikor a jogkivonat lett kiállítva, alapidőpont szerint jelöli. |
| Lejárati idő |`exp` |`1438539443` |A lejárati idő jogcím az idő, amikor a jogkivonat érvénytelenné, alapidőpont szerint jelennek meg. Az alkalmazás használjon ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| Nem előtt |`nbf` |`1438535543` |Ez a jogcím az idő, amikor a jogkivonat lesz érvényes, alapidőpont szerint kezeli őket. Ez általában a ugyanaz, mint az idő a jogkivonat lett kiállítva. Az alkalmazás használjon ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| Verzió |`ver` |`1.0` |Ez az azonosító jogkivonat verziója alapján Azure ad-ben. |
| Kód kivonata |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Csak akkor, ha a jogkivonat az OAuth 2.0 hitelesítési kód együtt kiadott kód kivonatot egy azonosító jogkivonat tartalmazza. Kód kivonatot használható egy engedélyezési kód hitelességének ellenőrzéséhez. Az ellenőrzés elvégzéséhez további részletekért tekintse meg a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hozzáférési jogkivonat kivonata |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Csak akkor, ha a jogkivonat együtt az OAuth 2.0 hozzáférési jogkivonatban kiadott egy hozzáférési jogkivonat kivonata egy azonosító jogkivonat tartalmazza. Egy hozzáférési jogkivonat kivonata használható hozzáférési jogkivonat hitelességének ellenőrzéséhez. Az ellenőrzés elvégzéséhez további részletekért tekintse meg a [OpenID Connect-specifikáció](http://openid.net/specs/openid-connect-core-1_0.html)  |
| egyszeri |`nonce` |`12345` |Egy egyszeri ismétlésének támadások számának csökkentése érdekében használt stratégiát is. Az alkalmazás is megadhat egy egyszeri egy engedélyezési kérésben a `nonce` lekérdezési paraméter. Az értéknek a kérelem fogja bocsátja ki kívánja módosítani a a `nonce` jogcím csak egy azonosító jogkivonat. Ez lehetővé teszi az alkalmazásba és ellenőrizze az érték azt a kérést, amely az alkalmazás munkamenet az egy adott azonosító jogkivonat társítja a megadott értékkel. Az alkalmazás végre kell hajtania az ellenőrzés az azonosító jogkivonat érvényesítése során. |
| Tárgy |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Ez az a arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például az alkalmazás a felhasználó egyszerű. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Az engedélyezési ellenőrzéséhez biztonságosan, például amikor a jogkivonat erőforrások eléréséhez használható. Alapértelmezés szerint megjelenik a tulajdonos jogcímet a felhasználó Objektumazonosítóját. További tudnivalókért lásd: [Azure Active Directory B2C: jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak](active-directory-b2c-token-session-sso.md). |
| Hitelesítési környezethez tartozó osztályhivatkozása |`acr` |Nem alkalmazható |Jelenleg nem használt, kivéve a régebbi házirendeket. További tudnivalókért lásd: [Azure Active Directory B2C: jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak](active-directory-b2c-token-session-sso.md). |
| Megbízhatósági keretrendszer házirend |`tfp` |`b2c_1_sign_in` |Ez az az azonosító jogkivonat beszerzéséhez használt a házirend nevét. |
| Hitelesítési idő |`auth_time` |`1438535543` |Ez a jogcím az idő, amikor egy felhasználó legutóbbi megadott hitelesítő adatokat, alapidőpont szerint jelöli. |

### <a name="refresh-tokens"></a>Frissítési jogkivonatok
Frissítési jogkivonatok olyan biztonsági jogkivonatok, amelyek az alkalmazás segítségével szerzi be a új azonosító-jogkivonatokat, és hozzáférési jogkivonatokat az OAuth 2.0 flow-ban. Az alkalmazás a hosszú távú a felhasználók nevében erőforrásokhoz való hozzáférés, anélkül, hogy a ezeket a felhasználói nyújtanak.

Fogadásához egy frissítési jogkivonat a jogkivonat-válasz, az alkalmazás kell igényelnie a `offline_acesss` hatókör. További információkat talál a `offline_access` hatókörét, tekintse meg a [Azure AD B2C protokollreferenciáját](active-directory-b2c-reference-protocols.md).

Frissítési jogkivonatok, és mindig lesz, teljesen átlátszatlan, az alkalmazáshoz. Azok az Azure AD által kiállított és ellenőrzött és értelmezése kizárólag az Azure ad-ben. Hosszú élettartamú, azonban az alkalmazás nem kell írni az az elvárás, hogy a frissítési jogkivonatok egy meghatározott ideig tart. Frissítési biztonsági jogkivonat bármely pillanatban a többféle okból lehet érvényteleníteni. Az egyetlen módszer az alkalmazás tudja, hogy ha egy frissítési jogkivonat érvényes, hogy azáltal, hogy a jogkivonat kérése az Azure AD a beváltáshoz kísérlet.

Amikor egy új jogkivonatot egy frissítési jogkivonat beváltása (, és ha az alkalmazás megkapta-e a `offline_access` hatókör), a jogkivonat választ kap egy új frissítési jogkivonatot. Mentse az újonnan kiállított frissítési jogkivonatot. A frissítési jogkivonat a kérelemben használt azt kell lecserélnie. Ez segít garantálni, hogy a frissítési biztonsági jogkivonat érvényes, amíg maradnak.

## <a name="token-validation"></a>Érvényesítési jogkivonat
A jogkivonat érvényesítéséhez, az alkalmazás ellenőrizze az aláírás és a jogcímek jogkivonat.

Számos nyílt forráskódú kódtár ellenőrzési JWTs, attól függően, a választott nyelven érhetők el. Azt javasoljuk, hogy a saját ellenőrzési logika megvalósítása helyett inkább ezen lehetőségek közül válogathat. Az adatokat az útmutató segítségével megtudhatja, hogyan megfelelően használni az ezeket a kódtárakat.

### <a name="validate-the-signature"></a>Az aláírás ellenőrzése
Jwt-t tartalmaz három szegmensek elválasztva a `.` karakter. Az első szegmens a *fejléc*, a második pedig a *törzs*, és a harmadik pedig a *aláírás*. Az aláírás szegmens a token hitelességének ellenőrzéséhez, hogy az alkalmazás által is megbízhatónak használható.

Az Azure AD B2C-jogkivonatok jelentkezett, iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például RSA-256 használatával. A fejléc a jogkivonat a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A `alg` jogcím azt jelzi, hogy a jogkivonat aláírásához használt algoritmust. A `kid` jogcím azt jelzi, hogy a jogkivonat aláírásához használt adott nyilvános kulcs.

Egy adott időpontban a az Azure AD által meghatározott készlete nyilvános-titkos kulcspárok valamelyikét használja a jogkivonat jelentkezhetnek be. Az Azure AD rendszeres időközönként forog kulcsok lehetséges készletét, így az alkalmazás automatikusan kezeli a fő módosításokat kell írni. Az Azure AD által használt nyilvános kulcsok frissítései ésszerű gyakoriságot van 24 óránként.

Az Azure AD B2C az OpenID Connect metaadatok végpontja van. Ez lehetővé teszi az alkalmazások futtatáskor az Azure AD B2C-vel kapcsolatos információkat lekérni. Ezen információk közé tartozik a végpontokat, a jogkivonat tartalma és a jogkivonat-aláíró kulcsok. A B2C-címtár minden JSON-metaadatok dokumentumok tartalmazza. Például a metaadat-dokumentum számára a `b2c_1_sign_in` szabályzat `fabrikamb2c.onmicrosoft.com` helyen található:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` a felhasználó hitelesítésére használt B2C-címtár és `b2c_1_sign_in` a házirend, a token beszerzéséhez használt. Mely házirendet egy token aláírásához használt (és, hogy hová lépjen a metaadatok beolvasása), két lehetősége van. Először a szabályzat neve szerepel a `acr` jogcím a jogkivonatban. A JWT törzse ki jogcímeket base-64 dekódolás a szervezet és a JSON-karakterlánc, az eredmények deszerializálása szerint elemezhetők. A `acr` jogcímszabály lesz, amellyel a jogkivonatot kiállítani a házirend nevét.  A másik lehetőség egy kódolása a szabályzatot az értékét a `state` paraméter, amikor a kérelmet, és ezután dekódolni a meghatározásához, hogy mely házirendet lett megadva. Mindkét módszer esetén érvényes.

A metaadat-dokumentumban több hasznos információt tartalmazó JSON-objektum. Ezek közé tartozik az OpenID Connect hitelesítési elvégzéséhez szükséges végpontok helyét. Ezek közé tartozik `jwks_uri`, révén a hely nyilvános kulcsok készlete, amelyek használhatók a jogkivonatok aláírásához. Hogy a hely van az itt elérhető, de a legjobb, ha a hely dinamikusan beolvasni a metaadat-dokumentum segítségével, illetve ki elemzés `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

A következő URL-címen található JSON-dokumentum összes a nyilvános kulcsot tartalmazza egy adott pillanatban használja. Az alkalmazás használhatja a `kid` jogcím a JWT fejlécében a JSON-dokumentum, amely egy adott token aláírására szolgál ki a nyilvános kulcsot. Aláírás-ellenőrzése végez, majd a megfelelő nyilvános kulcsot és a jelzett algoritmus használatával.

Aláírás-ellenőrzés végrehajtása leírását ez a dokumentum hatókörén kívül esik. Számos nyílt forráskódú kódtár segítséget ezt, ha szüksége lesz rá érhetők el.

### <a name="validate-the-claims"></a>A jogcímek ellenőrzése
Amikor az alkalmazás- vagy API-t kap egy azonosító jogkivonat, azt az azonosító jogkivonat is végre kell hajtania több ellenőrzi a jogcímek ellen. Ezek közé tartozik, de nem korlátozódnak:

* A **célközönség** igényelni: Ez ellenőrzi, hogy az azonosító jogkivonat szánták, az alkalmazásnak meg kell adni.
* A **nem előtt** és **lejárati idő** jogcímek: ezek győződjön meg arról, hogy az azonosító jogkivonat nem járt le.
* A **kibocsátó** igényelni: Ez ellenőrzi, hogy a jogkivonat bocsátotta az alkalmazás Azure ad-ben.
* A **egyszeri**: Ez a stratégia ismétlésének elleni támadások kockázatait.

Ellenőrzések, végre kell hajtania, az alkalmazás teljes listájáért tekintse meg a [OpenID Connect specifikáció](https://openid.net). Ezeket a jogcímeket várt értéket részletek szerepelnek az előző [jogkivonat szakaszban](#types-of-tokens).  

## <a name="token-lifetimes"></a>A jogkivonatok élettartama
A következő jogkivonatok élettartamának tudását további vannak megadva. Ezek segítségével amikor fejlesztésekor és hibakeresésekor alkalmazásokat. Vegye figyelembe, hogy az alkalmazások nem írható várható bármelyik ezek élettartam változatlan marad. Akkor is, és változni fog. Tudjon meg többet a [jogkivonatok élettartamának testreszabása](active-directory-b2c-token-session-sso.md) Azure AD B2C-ben.

| Jogkivonat | Élettartam | Leírás |
| --- | --- | --- |
| Azonosító-jogkivonatokat |Egy óra |ID jogkivonatok érvényesek általában egy órán keresztül. A webalkalmazás az élettartam használatával saját munkamenetek karbantartása felhasználókkal (ajánlott). Azt is beállíthatja egy másik munkamenet élettartamára. Ha az alkalmazás egy új azonosító jogkivonat beszerzése, egyszerűen kell ellenőriznie egy új bejelentkezési kérelmet az Azure ad-hez. Ha egy felhasználó érvényes böngésző-munkamenet, az Azure ad-vel rendelkezik, adja meg ismét hitelesítő adatait, előfordulhat, hogy nem szükséges a, hogy a felhasználó. |
| Frissítési jogkivonatok |Akár 14 nap |A egyetlen frissítési jogkivonatok esetében legfeljebb 14 napig érvényes. Azonban a frissítési jogkivonatok válhat érvénytelen számos oka bármikor. Az alkalmazás továbbra is próbálja használni a frissítési jogkivonatok mindaddig, amíg a kérelem meghiúsul, vagy amíg az alkalmazás egy új lecseréli a frissítési jogkivonatot. A frissítési jogkivonatok is válhatnak, ha 90 napig teljesül, mivel a felhasználó utoljára megadott hitelesítő adatok érvénytelen. |
| Engedélyezési kód |Öt perc alatt |Engedélyezési kódokat szándékosan rövid ideig tartó. Ezek kell kézbesítésük után azonnal hozzáférési jogkivonatok, azonosító-jogkivonatokat vagy frissítési biztonsági jogkivonat érkezésükkor. |

