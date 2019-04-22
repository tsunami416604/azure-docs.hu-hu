---
title: Jogkivonatok – Azure Active Directory B2C – áttekintés |} A Microsoft Docs
description: További információ az Azure Active Directory B2C-vel használt tokenek.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680264"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C jogkivonatok áttekintése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD) B2C számos különböző típusú biztonsági jogkivonatokat bocsát ki, amint az feldolgozza azokat egyes [hitelesítési folyamat](active-directory-b2c-apps.md). Ez a dokumentum ismerteti a formátumát, a biztonsági jellemzőkkel és a különböző típusú jogkivonat tartalma.

## <a name="token-types"></a>Jogkivonat típusa

Az Azure AD B2C támogatja a [OAuth 2.0 és OpenID Connect protokollok](active-directory-b2c-reference-protocols.md), amely él jogkivonatokat a hitelesítéshez és az erőforrások biztonságos elérését. Az Azure AD B2C-ben használt összes jogkivonatok olyan [JSON webes jogkivonatainak (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , amelyek tartalmazzák a helyességi feltételek vonatkozó információkat a tulajdonosi és a jogkivonat tárgyában.

A következő jogkivonatok használhatók az Azure AD B2C-vel folytatott kommunikáció:

- *Azonosító jogkivonat* –, amely tartalmazza A JWT-jogcímek, használatával azonosítsa azokat a felhasználókat az alkalmazásban. Ez a token biztonságosan küldése az ugyanazon alkalmazás vagy szolgáltatás két összetevői közötti kommunikációhoz HTTP-kérelmekre. Egy azonosító jogkivonat a jogcímek tetszés szerint használhatja. Fiók adatainak megjelenítése, illetve, hogy döntést hozhasson a hozzáférésről az alkalmazások gyakran használják. Azonosító-jogkivonatokat jelentkezett, de nem titkosítottak. Amikor az alkalmazás- vagy API-t kap egy azonosító jogkivonat, azt ellenőrizni kell az aláírás igazolja, hogy a jogkivonat hiteles. Az alkalmazás- vagy API-t is ellenőrizni kell igazolnia, azaz érvényes néhány jogcímeket. A forgatókönyv követelményeitől függően változhat, a jogcímek, az alkalmazások által ellenőrzött, de az alkalmazásnak el kell végeznie minden forgatókönyvben néhány gyakori jogcím ellenőrzések.
- *Hozzáférési jogkivonat* –, amely tartalmazza A JWT-jogcímek használhatja a saját API-khoz megadott engedélyek azonosításához. Hozzáférési jogkivonatok jelentkezett, de azok nem titkosított. Hozzáférési jogkivonatok az API-k és erőforrás-kiszolgálók elérésére szolgálnak.  Amikor az API-t egy hozzáférési jogkivonatot kap, azt az aláírás igazolja, hogy a token hiteles ellenőrizni kell. Az API-t is ellenőrizni kell igazolnia, azaz érvényes néhány jogcímeket. A forgatókönyv követelményeitől függően változhat, a jogcímek, az alkalmazások által ellenőrzött, de az alkalmazásnak el kell végeznie minden forgatókönyvben néhány gyakori jogcím ellenőrzések.
- *Frissítési jogkivonat* -frissítési biztonsági jogkivonat segítségével szerezzen új azonosító-jogkivonatokat, és hozzáférési jogkivonatokat az OAuth 2.0 flow-ban. Az alkalmazás hosszú távú hozzáféréssel rendelkező erőforrások felhasználók nevében anélkül, hogy a ezeket a felhasználói nyújtanak. Frissítési biztonsági jogkivonat nem átlátszók az alkalmazáshoz. Azok az Azure AD B2C által kiállított és ellenőrzött és csak az Azure AD B2C-vel értelmezi. Hosszú élettartamú, azonban az alkalmazás nem lehet írni az az elvárás, hogy a frissítési jogkivonatok egy meghatározott ideig tart. Frissítési biztonsági jogkivonat bármely pillanatban a többféle okból lehet érvényteleníteni. Az egyetlen módszer, hogy az alkalmazás tudja, hogy ha egy frissítési jogkivonat érvényes, hogy egy jogkivonat kérése az Azure AD B2C-t, így a beváltáshoz kísérlet. Amikor az ügyfél beváltja az új jogkivonatot egy frissítési jogkivonatot, kap egy új frissítési jogkivonatot a jogkivonat válaszban. Mentse az új frissítési jogkivonatot. Lecseréli a frissítési jogkivonat a kérelemben szereplő korábban használt. Ez a művelet segít garantálni, hogy a frissítési biztonsági jogkivonat érvényes, amíg maradnak. 

## <a name="endpoints"></a>Végpontok

A [regisztrált alkalmazás](tutorial-register-applications.md) jogkivonatok fogadása és az Azure AD B2C-vel kommunikál a fenti végpontokkal való kérések küldésével:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Biztonsági jogkivonatokat, amely az alkalmazás megkapja az Azure AD B2C-ből származhat a `/authorize` vagy `/token` végpontok. Ha azonosító-jogkivonatokat a beszerzett a `/authorize` végpontot, hogy elkészültek használatával a [implicit folyamat](active-directory-b2c-reference-spa.md), gyakran használt felhasználó jelentkezik be a javascript-alapú webes alkalmazásokhoz. Ha azonosító-jogkivonatokat a beszerzett a `/token` végpontot, hogy elkészültek használatával a [bizalmas kódfolyamat](active-directory-b2c-reference-oidc.md), amely megőrzi a jogkivonatot a böngészőből rejtett.

## <a name="claims"></a>Igénylések

Azure AD B2C-vel való használatakor, teljes mértékben szabályozhatják a jogkivonatok tartalmát. Konfigurálható [felhasználói folyamatok](active-directory-b2c-reference-policies.md) és [egyéni szabályzatok](active-directory-b2c-overview-custom.md) bizonyos csoportok felhasználói adatok küldése a jogcímek, az alkalmazás számára szükséges. Ezeket a jogcímeket is tartalmaznak alapvető tulajdonságok, mint például **displayName** és **emailAddress**. Az alkalmazások használhatják ezeket a jogcímeket biztonságosan a felhasználók és a kérelmek hitelesítéséhez. 

A jogcímek, az ID jogkivonatok nem bármely adott sorrendben adja vissza. A bevezetett új jogcímeket azonosító-jogkivonatokat a tetszőleges időpontban. Az alkalmazás nem szünet, új jogcímeket jelennek meg. Is [egyéni felhasználói attribútumok](active-directory-b2c-reference-custom-attr.md) a jogcímekben.

Az alábbi táblázat a jogcímeket, hogy várt azonosító-jogkivonatokat, majd elérni az Azure AD B2C által kiállított jogkivonatokban.

| Name (Név) | Jogcím | Példaérték | Leírás |
| ---- | ----- | ------------- | ----------- |
| Célközönség | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | A jogkivonat az illetékes címzett azonosítja. Az Azure AD B2C-vel a célközönségét az alkalmazás azonosítója. Az alkalmazás kell érvényesíteni ezt az értéket, és elutasítja a tokent, ha nem felel meg. Célközönségét egyenértékű az erőforrás. |
| Kiállító | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatát adja vissza. A könyvtárat, amelyben a felhasználó hitelesítési is azonosítja. Az alkalmazás a kibocsátó jogcím győződjön meg arról, hogy a jogkivonat származik-e a megfelelő végpontra, ellenőrizni kell. |
| Tulajdonos: | `iat` | `1438535543` | Az idő, amikor a jogkivonat lett kiállítva, jelöli, alapidőpont szerint. |
| Lejárati idő | `exp` | `1438539443` | Az idő, amikor a jogkivonat érvénytelen, válik jelöli, alapidőpont szerint. Az alkalmazás használ a ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| Nem előtt | `nbf` | `1438535543` | Az idő, amikor a jogkivonat érvényes, válik jelöli, alapidőpont szerint. Ez az idő általában a ugyanaz, mint az idő a jogkivonat lett kiállítva. Az alkalmazás használ a ezt az igényt a jogkivonatok élettartamának érvényességének ellenőrzéséhez. |
| Verzió | `ver` | `1.0` | Az azonosító jogkivonat, az Azure AD B2C által meghatározott verziója. |
| Kód kivonata | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Csak akkor, ha a jogkivonat az OAuth 2.0 hitelesítési kód együtt kiadott egy azonosító jogkivonat szereplő kód kivonatot. Kód kivonatot használható egy engedélyezési kód hitelességének ellenőrzéséhez. Az ellenőrzéssel kapcsolatos további információkért lásd: a [OpenID Connect specifikáció](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hozzáférési jogkivonat kivonata | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Egy hozzáférési jogkivonat-kivonat csak akkor, ha a jogkivonat együtt az OAuth 2.0 hozzáférési jogkivonatban kiadott egy azonosító jogkivonat szerepel. Egy hozzáférési jogkivonat kivonata használható hozzáférési jogkivonat hitelességének ellenőrzéséhez. Az ellenőrzéssel kapcsolatos további információkért lásd: a [OpenID Connect-specifikáció](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Egy egyszeri ismétlésének támadások számának csökkentése érdekében használt stratégiát is. Az alkalmazás is megadhat egy egyszeri egy engedélyezési kérésben a `nonce` lekérdezési paraméter. Az értéknek a kérésben bocsásson ki az kívánja módosítani a `nonce` jogcím csak egy azonosító jogkivonat. Ez a jogcím lehetővé teszi, hogy az alkalmazás ellenőrzéséhez az érték a kérésben megadott értékkel. Az alkalmazásoknak végre kell hajtania az ellenőrzés az azonosító jogkivonat érvényesítése során. |
| Tárgy | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például egy alkalmazás felhasználója. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Az engedélyezési ellenőrzéséhez biztonságosan, például amikor a jogkivonat erőforrások eléréséhez használható. Alapértelmezés szerint megjelenik a tulajdonos jogcímet a felhasználó Objektumazonosítóját. |
| Hitelesítési környezethez tartozó osztályhivatkozása | `acr` | Nem alkalmazható | Csak a régebbi szabályzatokkal használható. |
| Megbízhatósági keretrendszer házirend | `tfp` | `b2c_1_signupsignin1` | A szabályzat az azonosító jogkivonat beszerzéséhez használt neve. |
| Hitelesítési idő | `auth_time` | `1438535543` | Az idő, amikor a felhasználó utoljára megadott hitelesítő adatokat, jelöli, alapidőpont szerint. |
| Hatókör | `scp` | `Read`| Erőforrás-hozzáférési jogkivonatot az engedélyeket. Több megadott engedélyek szóközzel vannak elválasztva. |
| Jogosult fél | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **Alkalmazásazonosító** az ügyfélalkalmazás által kezdeményezett a kérelmet. |

## <a name="configuration"></a>Konfiguráció

A következő tulajdonságok megegyeznek az használt [kezelheti a biztonsági jogkivonatok élettartamának](configure-tokens.md) Azure AD B2C által kibocsátott:

- **Hozzáférési és azonosító jogkivonat élettartama (perc)** – a védett erőforrások eléréséhez az OAuth 2.0 tulajdonosi jogkivonat élettartama. Az alapértelmezett érték 60 perc. (A határokat is beleértve) a minimális érték 5 perc. (A szélsőértékek megengedettek) legfeljebb 1440 perc.

- **Frissítési jogkivonat élettartama (nap)** – az a maximális időtartam, ameddig egy új hozzáférési vagy azonosító jogkivonat beszerzésére használható a frissítési jogkivonatot. Az adott időszakban is vonatkozik egy új frissítési jogkivonat beszerzéséhez, ha az alkalmazása megkapta-e a `offline_access` hatókör. Az alapértelmezett érték 14 nap. (A határokat is beleértve) a minimális érték egy nap. A (inkluzív) maximális érték 90 napig.

- **Frissítési jogkivonat csúszóablak-élettartama (nap)** - e a felhasználónak kötelező hitelesítse magát újra, attól függetlenül, az érvényességi időtartam legutóbbi frissítése az alkalmazás által beszerzett jogkivonattal idő időszak eltelte után. Ez csak biztosítható, hogy ha a kapcsoló beállítása **kötött**. Nagyobb vagy azzal egyenlőnek kell a **frissítési jogkivonat élettartama (nap)** értéket. Ha a kapcsoló beállítása **Unbounded**, nem adhat meg egy adott érték. Az alapértelmezett érték 90 nap. (A határokat is beleértve) a minimális érték egy nap. A (inkluzív) maximális hossz 365 nap során.

A következő használati esetek engedélyezve vannak ezek a tulajdonságok használatával:

- Maradjon bejelentkezve a egy mobilalkalmazásba határozatlan ideig felhasználó engedélyezése mindaddig, amíg a felhasználó az alkalmazás folyamatosan aktív. Beállíthat **frissítési jogkivonat csúszóablak-élettartama (nap)** való **Unbounded** a bejelentkezési felhasználói folyamat.
- Az iparági biztonsági és megfelelőségi követelmények felel meg a megfelelő hozzáférési jogkivonatok élettartamának beállításával.

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el. 

## <a name="compatibility"></a>Kompatibilitás

A következő tulajdonságok megegyeznek az használt [jogkivonat-kompatibilitási kezelése](configure-tokens.md):

- **Kibocsátói (iss) jogcím** – Ez a tulajdonság azonosítja az Azure AD B2C-bérlő, amely kiállította a jogkivonatot. Az alapértelmezett érték `https://<domain>/{B2C tenant GUID}/v2.0/`. Az érték `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` azonosítókat tartalmazza az Azure AD B2C-bérlő és a felhasználói folyamatot, amely a jogkivonat kérése volt használatban. Ha az alkalmazás vagy könyvtár van szüksége az Azure AD B2C-vel meg kell felelnie az a [OpenID Connect-felderítési 1.0 specifikáció](https://openid.net/specs/openid-connect-discovery-1_0.html), használja ezt az értéket.

- **Tárgy (sub) jogcím** – Ez a tulajdonság azonosítja az entitást, amelyhez a token használjon esetleg imperatív állításokat információkat. Az alapértelmezett érték **ObjectID**, amely feltölti a `sub` jogcím a token az a felhasználó Objektumazonosítóját. Az érték **nem támogatott** biztosított csak a visszamenőleges kompatibilitás érdekében. Váltson át a javasolt **ObjectID** , amint tudunk.

- **Házirend Azonosítóját jelölő jogcím** – Ez a tulajdonság azonosítja a jogcímtípus, amelybe a jogkivonat kérése használt szabályzatnév fel van töltve. Az alapértelmezett érték `tfp`. Az érték `acr` biztosított csak a visszamenőleges kompatibilitás érdekében.

## <a name="pass-through"></a>Továbbítás

Felhasználói út indításakor Azure AD B2C hozzáférési jogkivonatot kap Identitásszolgáltatóként. Az Azure AD B2C használja ezt a jogkivonatot a felhasználóval kapcsolatos információk lekéréséhez. Akkor [a felhasználói folyamat jogcím engedélyezése](idp-pass-through-user-flow.md) vagy [jogcím meghatározásához az egyéni házirendek](idp-pass-through-custom.md) a jogkivonat segítségével átadása az alkalmazásokat, amelyek az Azure AD B2C-ben regisztrálja. Az alkalmazás kell használnia egy [v2 felhasználói folyamat](user-flow-versions.md) átadja a token jogcímként előnyeinek kihasználása érdekében.

Az Azure AD B2C jelenleg csak támogatja a hozzáférési jogkivonatot az OAuth 2.0 identitásszolgáltató, például a Facebook és a Google átadása. Minden egyéb identitás-szolgáltatóktól a jogcím vissza üres. 

## <a name="validation"></a>Ellenőrzés

A jogkivonat érvényesítéséhez, az alkalmazás ellenőrizze az aláírás és a jogcímek jogkivonat. Számos nyílt forráskódú kódtár ellenőrzési JWTs, attól függően, a választott nyelven érhetők el. Javasoljuk, hogy a saját ellenőrzési logika megvalósítása helyett inkább ezen lehetőségek közül válogathat.

### <a name="validate-signature"></a>Aláírás ellenőrzése

Jwt-t tartalmaz három szegmensek egy *fejléc*, amely egy *törzs*, és a egy *aláírás*. Az aláírás szegmens a token hitelességének ellenőrzéséhez, hogy az alkalmazás által is megbízhatónak használható. Az Azure AD B2C-jogkivonatok jelentkezett, iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például RSA-256 használatával. 

A fejléc a jogkivonat a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Értékét a **továbbíthatóak** jogcím a token aláírásához használt algoritmust. Értékét a **gyerek** jogcím a token aláírásához használt nyilvános kulcsot. Egy adott időpontban a Azure AD B2C nyilvános-titkos kulcspárok készletét bármelyike segítségével egy tokent jelentkezhetnek be. Az Azure AD B2C rendszeres időközönként forog kulcsok lehetséges készletét. Az alkalmazás automatikusan kezeli a fő módosításokat kell írni. Az Azure AD B2C által használt nyilvános kulcsok frissítései ésszerű gyakoriságot van 24 óránként.

Az Azure AD B2C az OpenID Connect metaadatok végpontja van. Ezt a végpontot használja, alkalmazásokat kérhetnek futtatáskor az Azure AD B2C-vel kapcsolatos információkat. Ezen információk közé tartozik a végpontokat, a jogkivonat tartalma és a jogkivonat-aláíró kulcsok. Az Azure AD B2C-bérlő minden JSON-metaadatok dokumentumok tartalmazza. A metaadat-dokumentumban több hasznos információt tartalmazó JSON-objektum. A metaadatok tartalmaz **jwks_uri**, révén a hely nyilvános kulcsok készlete, amelyek használhatók a jogkivonatok aláírásához. Ajánlott a hely dinamikusan beolvasni a metaadat-dokumentum és-elemzés, hogy helyet, de itt megadott a **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
A következő URL-címen található JSON-dokumentum összes a nyilvános kulcsot tartalmazza egy adott pillanatban használja. Az alkalmazás használhatja a `kid` jogcím a JWT fejlécében a JSON-dokumentum, amely egy adott token aláírására szolgál ki a nyilvános kulcsot. Aláírás-ellenőrzése végez, majd a megfelelő nyilvános kulcsot és a jelzett algoritmus használatával.

A metaadat-dokumentum számára a `B2C_1_signupsignin1` szabályzat a `contoso.onmicrosoft.com` bérlő a következő helyen található:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Mely házirendet egy token aláírásához használt (és, hogy hová lépjen a metaadatait az), két lehetősége van. Először a szabályzat neve szerepel a `acr` jogcím a jogkivonatban. A JWT törzse ki jogcímeket base-64 dekódolás a szervezet és a JSON-karakterlánc, az eredmények deszerializálása szerint elemezhetők. A `acr` jogcím, amellyel a jogkivonatot kiállítani a házirend nevét. A másik lehetőség, hogy a szabályzatot az értékét kódolása a `state` paraméter, amikor a kérelmet, és ezután dekódolni a meghatározásához, hogy mely házirendet lett megadva. Mindkét módszer esetén érvényes.

Aláírás-ellenőrzés végrehajtása leírását ez a dokumentum hatókörén kívül esik. Számos nyílt forráskódú kódtár segítséget nyújt a jogkivonatot érvényesíteni érhetők el.

### <a name="validate-claims"></a>Jogcímek ellenőrzése

Amikor az alkalmazások és API-t kap egy azonosító jogkivonat, azt az azonosító jogkivonat is végre kell hajtania több ellenőrzi a jogcímek ellen. Ellenőrizni kell a következő jogcímek:

- **célközönség** – ellenőrzi, hogy az azonosító jogkivonat szánták, az alkalmazásnak meg kell adni.
- **nem előtt** és **lejárati idő** – ellenőrzi, hogy az azonosító jogkivonat lejárt még nem.
- **kiállító** –, hogy a jogkivonat ellenőrzi adta-e az alkalmazás Azure AD B2C-t.
- **egyszeri** -ismétlésének elleni támadások kockázatait stratégiáját.

Az alkalmazásoknak végre kell hajtania ellenőrzések teljes listájáért tekintse meg a [OpenID Connect specifikáció](https://openid.net).  

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [hozzáférési kódok használata](active-directory-b2c-access-tokens.md).

