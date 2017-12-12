---
title: Referencia - Azure AD B2C token |} Microsoft Docs
description: "Az Azure Active Directory B2C kiállított jogkivonatokat típusai"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: ce82fcc82cf411d1596fea56ff368d96eceeff38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-token-reference"></a>Az Azure AD B2C: Token referencia

Az Azure Active Directory B2C (az Azure AD B2C) számos különböző típusú biztonsági jogkivonatokat bocsát ki, amint az feldolgozza azokat egyes [hitelesítési folyamat](active-directory-b2c-apps.md). Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú lexikális elem tartalmát.

## <a name="types-of-tokens"></a>A jogkivonatok típusok
Az Azure AD B2C támogatja a [OAuth 2.0 protokoll](active-directory-b2c-reference-protocols.md), amely él mindkét hozzáférési jogkivonatok és frissítési jogkivonatok. Is támogatja a hitelesítés és bejelentkezés keresztül [OpenID Connect](active-directory-b2c-reference-protocols.md), amely bevezet egy harmadik típusú jogkivonat: a Azonosítót jogkivonatban. Egyes ezeket a jogkivonatokat tulajdonosi jogkivonattal jelzi.

Egy tulajdonosi jogkivonatot egy egyszerűsített biztonsági jogkivonatot, amely védett erőforrásokhoz való hozzáférést a "tulajdonos". A tulajdonosi, amely a token is jelenthet félre. Az Azure AD B2C először hitelesíteniük kell egy entitás előtt megkaphatja a tulajdonosi jogkivonattal. De ha a szükséges lépéseket a rendszer nem hajtja végre a lexikális elem szerepel az átvitel, illetve tárolás biztosításához, azt is hozzá és egy nem kívánt fél által használt. Néhány biztonsági jogkivonatokat rendelkezik egy olyan beépített mechanizmus meggátolja, hogy a nem hitelesített felek használja őket, de tulajdonosi jogkivonatok nem rendelkezik a mechanizmus. Azok a biztonságos csatornákat, például a transport layer security (HTTPS) kell szállítani.

Ha egy tulajdonosi jogkivonatot kívül egy biztonságos csatornán kerül továbbításra, egy rosszindulatú entitás segítségével a-átjárójának támadás jogkivonat és annak segítségével védett erőforrásokhoz való jogosulatlan hozzáférést. Az azonos biztonsági elveket alkalmazza, ha a tulajdonosi jogkivonatok tárolt és gyorsítótárba helyezni későbbi használat. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságos módon tárolja a tulajdonosi jogkivonatokhoz.

További biztonsági szempontok a tulajdonosi jogkivonatok, lásd: [RFC 6750 szakasz 5](http://tools.ietf.org/html/rfc6750).

Jelentős része az Azure AD B2C kiállított jogkivonatokat használják, mint a JSON webes jogkivonatok (JWTs). Jwt-t egy kompakt biztonságos URL-címet a eszközöket információk átvitele a két fél között. JWTs jogcímekként ismert információkat tartalmaznak. Ezek a helyességi feltételek kapcsolatban a tulajdonosi és a token tárgyát. A jogcímek JWTs a JSON-kódolású és továbbítására szerializált objektumok. Mivel az Azure AD B2C által kibocsátott JWTs aláírt, de nincs titkosítva, könnyen vizsgálhatja meg hibakeresési azt jwt-t a tartalmát. Több eszközök érhetők el, amely ehhez, beleértve a [jwt.ms](https://jwt.ms). JWTs kapcsolatos további információkért tekintse meg [JWT specifikációk](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Azonosító-jogkivonatokat

Egy azonosító jogkivonat a biztonsági jogkivonatot, amely az alkalmazás az Azure AD B2C fogad egy formája, amely `/authorize` és `/token` végpontok. Azonosító-jogkivonatokat helyettesítik [JWTs](#types-of-tokens), és használatával azonosítsa azokat a felhasználókat az alkalmazás jogcímeket tartalmaznak. Ha az azonosító-jogkivonatokat szerezte be a `/authorize` végpontot, azok végzett használatával a [implicit engedélyezési folyamat](active-directory-b2c-reference-spa.md), gyakran amellyel a felhasználók jelentkezik be a javascript-alapú webes alkalmazások. Ha az azonosító-jogkivonatokat beszerzett a `/token` végpontot, azok végzett használatával a [bizalmas folyamata](active-directory-b2c-reference-oidc.md), amely tartja a jogkivonatot a böngészőből rejtett. Ez lehetővé teszi, hogy a jogkivonat biztonságosan küldi el a HTTP-kérelmek az azonos alkalmazáshoz vagy szolgáltatáshoz két összetevői közötti kommunikációhoz. A jogcímek használata egy Azonosítót jogkivonatban igényei szerint. Gyakran használják fiók információk megjelenítéséhez vagy egy alkalmazásban a hozzáférés-vezérlési döntéseket.  

Azonosító-jogkivonatokat írt alá, de jelenleg nincsenek titkosítva. Ha az alkalmazás vagy API kap egy azonosító jogkivonatot, kell [az aláírás érvényesítése](#token-validation) , hogy a token hitelességének igazolásához. Az alkalmazás vagy API is ellenőrizni kell a jogkivonat érvényes igazolni néhány jogcímeket. Attól függően, hogy a forgatókönyv-követelményeinek, a jogcímek, az alkalmazás által érvényesített eltérőek lehetnek, de az alkalmazás kell végeznie néhány [közös jogcím érvényesítést](#token-validation) minden forgatókönyvben.

#### <a name="sample-id-token"></a>Minta azonosító tokent
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

Olyan hozzáférési jogkivonatot is egy formája, amely a biztonsági jogkivonatot, amely az alkalmazás fogad az Azure AD B2C `/authorize` és `/token` végpontok. Hozzáférési jogkivonatok is helyettesítik [JWTs](#types-of-tokens), és segítségével azonosíthatja a API-k a megadott engedélyekkel jogcímeket tartalmaznak. Hozzáférési jogkivonatok írt alá, de jelenleg nincsenek titkosítva. Hozzáférési jogkivonatok használandó API-k és erőforrás-kiszolgálók eléréséhez. További tudnivalók a [használja a hozzáférési jogkivonatok](active-directory-b2c-access-tokens.md). 

Ha az API-t kap egy hozzáférési jogkivonatot, kell [az aláírás érvényesítése](#token-validation) , hogy a token hitelességének igazolásához. Az API-t is ellenőrizni kell a jogkivonat érvényes igazolni néhány jogcímeket. Attól függően, hogy a forgatókönyv-követelményeinek, a jogcímek, az alkalmazás által érvényesített eltérőek lehetnek, de az alkalmazás kell végeznie néhány [közös jogcím érvényesítést](#token-validation) minden forgatókönyvben.

### <a name="claims-in-id-and-access-tokens"></a>Az ID és a hozzáférési jogkivonatok jogcímek

Az Azure AD B2C használatakor befolyásolni részletes a tokenek tartalmát. Konfigurálható [házirendek](active-directory-b2c-reference-policies.md) bizonyos beállítása felhasználói adatok küldése az alkalmazás a műveleteket igényel jogcímeket. Ezek a jogcímek tartalmazhatják szabványos tulajdonságait, például a felhasználó `displayName` és `emailAddress`. Tartalmazhatják [felhasználói egyéni attribútumok](active-directory-b2c-reference-custom-attr.md) megadható a B2C-címtárban. Minden azonosítója és a hozzáférési jogkivonatot kapott biztonsági jogcímek az egyes készletét tartalmazza. Az alkalmazások a jogcímek segítségével biztonságosan a felhasználók és a kérelmek hitelesítéséhez.

Figyelje meg, hogy bármely adott sorrendben nem lehet megjeleníteni a jogcímek, az azonosító-jogkivonatokat. Emellett új jogcímeket is bevezetni azonosító-jogkivonatokat bármikor. Az alkalmazás nem kell törés az új jogcímeket belépéskor. Az alábbiakban való hozzáféréskor a azonosítója és a hozzáférési jogkivonatok az Azure AD B2C által kiadott jogcímeket. További jogcímeket házirendek határozzák meg. Eljárás, próbálja ki a minta Azonosítót jogkivonatban a jogcím ellenőrzése illeszti be [jwt.ms](https://jwt.ms). További részletek találhatók a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html).

| Név | Jogcím | Példaérték | Leírás |
| --- | --- | --- | --- |
| Célközönség |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Egy célközönség jogcím azonosítja a jogkivonat az illetékes címzett. Az Azure AD B2C-ben a célközönség megegyezik az alkalmazás azonosítója, az alkalmazás az app-regisztrációs portálon rendelt. Az alkalmazás kell ellenőrizni az értékét, és utasítsa el a jogkivonatot, ha nem felel meg. |
| Kiállító |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |A jogcím azonosítja a biztonságijogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot ad vissza. Az Azure AD-címtárat, amelyben a felhasználó hitelesítési is azonosítja. Az alkalmazás érdemes ellenőrizni a kibocsátó jogcím győződjön meg arról, hogy a jogkivonat származik-e az Azure Active Directory v2.0-végponttól. |
| Ki: |`iat` |`1438535543` |Ezt az igényt az az idő, amelynél a jogkivonat adta ki, epoch idő. |
| Lejárati idő |`exp` |`1438539443` |A lejárati idő jogcím az az idő, amelynél a jogkivonat érvénytelen, válik képviselt epoch idő. Az alkalmazás által használandó ezt az igényt a jogkivonatok élettartama érvényességének ellenőrzésére. |
| Hatálybalépési idő |`nbf` |`1438535543` |Ezt az igényt az az idő, amelynél a jogkivonat lesz érvényes, az időbeli kortartományon kezeli őket. Ez általában akkor ugyanaz, mint az idő a token lett kiállítva. Az alkalmazás által használandó ezt az igényt a jogkivonatok élettartama érvényességének ellenőrzésére. |
| Verzió |`ver` |`1.0` |Ez az azonosító token verziója Azure AD által definiált konfigurációjának kialakításához. |
| Kód kivonata |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Kód kivonatát része egy Azonosítót jogkivonatban csak akkor, ha a jogkivonat kiadott együtt egy OAuth 2.0 hitelesítési kódot. Az engedélyezési kód hitelességének használható kód kivonatát. Az ellenőrzés elvégzéséhez további részletekért lásd: a [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hozzáférési jogkivonat kivonata |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Az access token kivonatoló része egy Azonosítót jogkivonatban csak akkor, ha a jogkivonat együtt OAuth 2.0 hozzáférési tokent ad ki. Az access token kivonatoló segítségével hitelességének olyan hozzáférési jogkivonatot. Az ellenőrzés elvégzéséhez további részletekért lásd: a [OpenID Connect meghatározása](http://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce |`nonce` |`12345` |Egy egyszeri üzenet hitelesítési karakterláncok ismétlésének támadások mérséklése használt stratégia. Az alkalmazás megadhat egy nonce engedélyezési kérelmet használatával a `nonce` lekérdezési paraméter. Megadja a kérelemben szereplő érték lesz kibocsátott, a változtatás nélkül a `nonce` jogcím csak egy azonosító jogkivonat. Ez lehetővé teszi az alkalmazásnak, hogy ellenőrizze a kérelmet, amely az adott azonosító jogkivonatot az alkalmazás munkamenet társítja azt megadott értékkel értékét. Az alkalmazás végre kell hajtania az ellenőrzés az azonosító jogkivonatok érvényesség-ellenőrzése során. |
| Tárgy |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Ez az a rendszerbiztonsági tag, amelyekről a token állításokat információkat, például egy alkalmazás felhasználói. Ez az érték nem módosítható és nem lehet újbóli hozzárendelése és nem használja fel újra. Az engedélyezési ellenőrzéséhez biztonságosan, például az erőforrások eléréséhez a token használatakor használható. Alapértelmezés szerint a tulajdonos jogcímet a felhasználó a címtárban objektumazonosítójú fel van töltve. További tudnivalókért lásd: [Azure Active Directory B2C: Token, munkamenet és egyszeri bejelentkezés konfigurációs](active-directory-b2c-token-session-sso.md). |
| Hitelesítési környezeti osztályait ismertető dokumentációban |`acr` |Nem alkalmazható |Jelenleg nem használt, kivéve a régebbi házirendek. További tudnivalókért lásd: [Azure Active Directory B2C: Token, munkamenet és egyszeri bejelentkezés konfigurációs](active-directory-b2c-token-session-sso.md). |
| Megbízhatósági keretrendszer házirend |`tfp` |`b2c_1_sign_in` |Ez az azonosító jogkivonat használt a házirend nevét. |
| Hitelesítési idő |`auth_time` |`1438535543` |Ezt az igényt az ideje, amellyel egy felhasználó utolsó megadott hitelesítő adatokat, epoch idő. |

### <a name="refresh-tokens"></a>Frissítési jogkivonatok
Frissítési jogkivonatok olyan biztonsági jogkivonatok, az alkalmazás segítségével szerezzen be új azonosító-jogkivonatokat, és a hozzáférési jogkivonatok az OAuth 2.0 folyamatból. Azoknak a felhasználóknak való interakció nélkül a hosszú távú a felhasználók nevében erőforrásokhoz való hozzáférést biztosítanak az alkalmazást.

Egy frissítési fogadásához token token választ, az alkalmazás kell igényelnie a `offline_acesss` hatókör. További részletek a `offline_access` hatókörét, tekintse meg a [az Azure AD B2C protokoll referenciája](active-directory-b2c-reference-protocols.md).

Frissítési jogkivonatok, és mindig lesz, az alkalmazás teljesen átlátszó. Azok az Azure AD által kiállított és ellenőrzött és csak az Azure AD értelmezi. Hosszú élettartamú, de az alkalmazás nem írható az az elvárás, hogy egy frissítési jogkivonat egy meghatározott ideig tart. Frissítési jogkivonatokat a számos okból bármikor érvénytelenített lehet. Csak az alkalmazás tudja, hogy a frissítési jogkivonat érvényes, beváltani azt azáltal, hogy a kérés az Azure AD bejelentkezési kísérletet.

Ha beváltja egy frissítési jogkivonat vagy egy új jogkivonatot (és ha az alkalmazás rendelkezik a `offline_access` hatókör), a token választ kap egy új frissítési jogkivonat. Az újonnan kiadott frissítési jogkivonat mentse. A kérésben használt frissítési jogkivonat azt kell cserélni. Ez segít garantálni, hogy a frissítési jogkivonatokat maradnak, amíg érvényes.

## <a name="token-validation"></a>Jogkivonatok érvényesség-ellenőrzése
Egy token ellenőrzéséhez az alkalmazás ellenőrizze az aláírás és a jogcímek jogkivonat.

Számos nyílt forráskódú szalagtárak JWTs, attól függően, hogy a választott nyelv ellenőrzésével kapcsolatos érhetők el. Azt javasoljuk, hogy a saját ellenőrzési logika megvalósítása helyett inkább megismerheti azokat a beállításokat. A jelen útmutató információk segítségére lehetnek a tárak megfelelően használata.

### <a name="validate-the-signature"></a>Az aláírás érvényesítése
A jwt-t tartalmazza az három, szóközzel elválasztva a `.` karakter. Az első szegmens a *fejléc*, a második pedig a *törzs*, és a harmadik pedig a *aláírás*. Az aláírás szegmens segítségével hitelességének a jogkivonatot, hogy az alkalmazás által megbízhatóak.

Az Azure AD B2C-jogkivonatok aláírt szabványos aszimmetrikus titkosítási algoritmusok, például RSA 256. A fejléc jogkivonat a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

A `alg` jogcím jelzi a token aláírásához használt algoritmust. A `kid` jogcím jelzi az adott a token aláírásához használt nyilvános kulcsot.

Egy adott időpontban az Azure AD bármikor beléphet a jogkivonat közül legalább egy nyilvános-titkos kulcspárokat bizonyos készlet használatával. Az Azure AD rendszeres időközönként forog kulcsok lehetséges készletét, így az alkalmazás kezelni azokat automatikusan kell írni. Az Azure AD által használt nyilvános kulcsok frissítések kereséséhez ésszerű gyakoriságát 24 óránként van.

Az Azure AD B2C az OpenID Connect metaadatok végponttal rendelkezik. Ez lehetővé teszi az alkalmazások az Azure AD B2C futásidőben információ beolvasása. Ezen információk közé tartozik a végpontok, lexikális elem tartalmának és jogkivonat-aláíró kulcsok. A B2C-címtárat a JSON-házirendet metaadat-dokumentum tartalmazza. Például a metaadat-dokumentum esetében a `b2c_1_sign_in` -szabályzat `fabrikamb2c.onmicrosoft.com` helyen található:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`a felhasználó hitelesítésére használt B2C címtár és `b2c_1_sign_in` a házirend, a jogkivonat segítségével. Milyen házirend jogkivonat aláírásához használt (és a metaadatok beolvasása hol), két lehetőség közül választhat. Először a házirend neve szerepel a `acr` jogcím a tokenben. Jogcímek kívül a jwt-t a szervezet által base-64 dekódolás a szervezet és a JSON-karakterláncban eredmények deszerializálása tudja értelmezni. A `acr` jogcím lesz, amely használatával a jogkivonatot bocsásson ki a házirend nevét.  A másik lehetőség egy kódolására a házirend az értékét a `state` paraméter küldje a kérelmet, és ezután dekódolni a meghatározásához, hogy mely házirendet lett megadva. Mindkét módszer esetén érvényes.

A metaadat-dokumentum néhány hasznos adatot tartalmazó JSON-objektum. Ezek közé tartozik az OpenID Connect hitelesítési elvégzéséhez szükséges végpontok helyét. Ezek közé tartoznak `jwks_uri`, ami helyét, a nyilvános kulcsok készlete, amelyek használhatók a jogkivonatok aláírására. Hogy a hely itt valósul meg, de a legjobb, ha a hely dinamikusan beolvasni a metaadat-dokumentum használatával, és a kimenő elemzése `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

A JSON-dokumentum, az URL-címen található összes a nyilvánoskulcs-adatokat egy adott pillanatban használatban tartalmazza. Az alkalmazás használhatja a `kid` jogcím jelölje ki a nyilvános kulcsot a JSON-dokumentum, egy adott token aláírásához használt a JWT-fejlécben. Ezután a megfelelő nyilvános kulccsal és a megadott algoritmus használatával végezheti el az aláírás érvényesítése.

Hogyan aláírás-ellenőrzéshez van ez a dokumentum nem terjed. Számos nyílt forráskódú szalagtárak segítséget nyújt a, ha esetleg szükség lenne rá érhetők el.

### <a name="validate-the-claims"></a>A jogcímek ellenőrzése
Ha az alkalmazás vagy API kap egy azonosító jogkivonatot, azt kell is ellenőrzi a több szemben a Azonosítót jogkivonatban. Ezek közé tartozik, de nem korlátozva:

* A **célközönség** igényelni: Ez ellenőrzi, hogy az azonosító jogkivonatot kell fordítani az alkalmazás szándéka.
* A **nem előtt** és **lejárati idő** jogcímek: ezek győződjön meg arról, hogy az azonosító jogkivonat nem járt le.
* A **kibocsátó** igényelni: Ez ellenőrzi, hogy a jogkivonat részére adták ki az alkalmazást az Azure ad.
* A **nonce**: Ez a hitelesítési karakterláncok ismétlésének támadás megoldás stratégiát is.

Végre kell hajtania, az alkalmazás érvényesítést teljes listáját lásd a [OpenID Connect specification](https://openid.net). Ezeket a jogcímeket a várt értékek részletek szerepelnek az előző [szakasz token](#types-of-tokens).  

## <a name="token-lifetimes"></a>Token élettartama
A következő token élettartama pedig kiállíthatják a Tudásbázis találhatók. Akkor lehet hasznos, ha Ön által fejlesztett és alkalmazások hibakeresését. Figyelje meg, hogy az alkalmazások nem írható várható bármelyik ezek élettartama állandó maradjon. Akkor is, és változni fog. További információ a [token élettartamának testreszabása](active-directory-b2c-token-session-sso.md) az Azure AD B2C.

| Token | Élettartam | Leírás |
| --- | --- | --- |
| Azonosító-jogkivonatokat |Egy óra |Azonosító-jogkivonatokat érvényesek általában egy óra. A webalkalmazás a élettartama segítségével a saját munkamenetek karbantartása (ajánlott) felhasználókkal. A különböző munkamenetek élettartamát is beállíthatja. Ha az alkalmazás egy új ID-token beszerzése, egyszerűen indítson új bejelentkezési kérelmet az Azure AD kell. Ha egy felhasználó egy érvényes böngésző-munkamenet az Azure ad-vel, hogy a felhasználó előfordulhat, hogy nem kell írja be újra a hitelesítő adatokat. |
| Frissítési jogkivonatok |Legfeljebb 14 nap |Egyetlen frissítési jogkivonat esetén legfeljebb 14 napig érvényes. Azonban a frissítési jogkivonat válhat érvénytelen számos oka bármikor. Az alkalmazás továbbra is egy frissítési jogkivonat használatakor, amíg a kérelem sikertelen lesz, vagy az alkalmazás egy új frissítési jogkivonat lecseréli. A frissítési jogkivonat érvénytelen, ha 90 nap eltelt óta. a felhasználó utoljára megadott hitelesítő adatok is válhat. |
| Hitelesítési kódok |Öt perc |Hitelesítési kódok szándékosan rövid élettartamú. Akkor kell váltható azonnal a hozzáférési jogkivonatok, azonosító-jogkivonatokat, vagy a frissítési jogkivonatokat érkezésükkor. |

