---
title: Jogkivonatok áttekintése – Azure Active Directory B2C
description: Ismerje meg az Azure Active Directory B2C-ben használt jogkivonatokat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186488"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Jogkivonatok áttekintése az Azure Active Directory B2C szolgáltatásában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) többféle biztonsági jogkivonatot bocsát ki az egyes [hitelesítési folyamatok feldolgozása során.](application-types.md) Ez a dokumentum az egyes jogkivonat-típusok formátumát, biztonsági jellemzőit és tartalmát ismerteti.

## <a name="token-types"></a>Tokentípusok

Az Azure AD B2C támogatja az [OAuth 2.0 és openId connect protokollokat,](protocols-overview.md)amelyek a tokenek hitelesítéséhez és az erőforrásokhoz való biztonságos hozzáféréshez való használatát használják. Az Azure AD B2C-ben használt összes jogkivonat [JSON-webtokenek (JWT-k),](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) amelyek a tulajdonosra és a jogkivonat tárgyára vonatkozó információkat tartalmaznak.

Az Azure AD B2C-vel való kommunikáció során a következő jogkivonatokat használjuk:

- *Azonosító jogkivonat* – Olyan JWT, amely olyan jogcímeket tartalmaz, amelyek segítségével azonosíthatja a felhasználókat az alkalmazásban. Ezt a jogkivonatot biztonságosan küldi http-kérelmek közötti kommunikáció két összetevője ugyanazon alkalmazás vagy szolgáltatás. A jogcímeket egy azonosító jogkivonatban is használhatja, ahogy jónak látja. Gyakran használják fiókadatok megjelenítésére vagy hozzáférés-vezérlési döntések meghozatalára egy alkalmazásban. Az azonosító jogkivonatok aláírtak, de nincsenek titkosítva. Amikor az alkalmazás vagy API azonosító jogkivonatot kap, ellenőriznie kell az aláírást annak igazolására, hogy a jogkivonat hiteles. Az alkalmazás vagy API is érvényesítenie kell néhány jogcímeket a jogkivonatban annak igazolására, hogy az érvényes. A forgatókönyv követelményeitől függően az alkalmazás által érvényesített jogcímek eltérőek lehetnek, de az alkalmazásnak minden esetben el kell végeznie néhány gyakori jogcím-érvényesítést.
- *Hozzáférési jogkivonat* – Olyan JWT, amely olyan jogcímeket tartalmaz, amelyek segítségével azonosíthatja az API-k nak adott engedélyeket. A hozzáférési jogkivonatok aláírva vannak, de nincsenek titkosítva. A hozzáférési jogkivonatok api-k hoz és erőforrás-kiszolgálókhoz való hozzáférés biztosításához használatosak.  Amikor az API kap egy hozzáférési jogkivonatot, ellenőriznie kell az aláírást annak igazolására, hogy a jogkivonat hiteles. Az API-nak is ellenőriznie kell néhány jogcímeket a jogkivonatban annak igazolására, hogy érvényes. A forgatókönyv követelményeitől függően az alkalmazás által érvényesített jogcímek eltérőek lehetnek, de az alkalmazásnak minden esetben el kell végeznie néhány gyakori jogcím-érvényesítést.
- *Frissítési jogkivonat* – A frissítési jogkivonatok új azonosító jogkivonatok és hozzáférési jogkivonatok beszerzésére szolgálnak egy OAuth 2.0-s folyamatban. Az alkalmazás hosszú távú hozzáférést biztosít az erőforrásokhoz a felhasználók nevében anélkül, hogy az ezekkel a felhasználókkal való interakció. A frissítési jogkivonatok nem átlátszatlanok az alkalmazás számára. Ezeket az Azure AD B2C bocsátja ki, és csak az Azure AD B2C vizsgálhatja és értelmezheti. Ezek hosszú élettartamú, de az alkalmazás nem kell írni azzal az elvárással, hogy a frissítési jogkivonat tart egy adott ideig. A frissítési jogkivonatok bármikor érvényteleníthetők különböző okok miatt. Az egyetlen módja annak, hogy az alkalmazás tudja, ha egy frissítési jogkivonat érvényes, hogy megpróbálja beváltani az Azure AD B2C tokenkéréssel. Amikor egy új jogkivonatot vált be egy új jogkivonathoz, egy új frissítési jogkivonatot kap a jogkivonatválaszban. Mentse az új frissítési jogkivonatot. Felülírja a kérelemben korábban használt frissítési jogkivonatot. Ez a művelet segít garantálni, hogy a frissítési jogkivonatok a lehető legtovább érvényesek maradjanak.

## <a name="endpoints"></a>Végpontok

A [regisztrált alkalmazás](tutorial-register-applications.md) jogkivonatokat kap, és kommunikál az Azure AD B2C-vel a következő végpontokra küldött kérelmekkel:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Az Azure AD B2C-től kapott biztonsági jogkivonatok `/token` származhatnak a vagy a `/authorize` végpontok. Ha azonosító jogkivonatokat szerez `/authorize` be a végpontról, akkor az [implicit folyamat](implicit-flow-single-page-application.md)használatával történik, amelyet gyakran használnak a JavaScript-alapú webalkalmazásokba bejelentkező felhasználók számára. Ha az azonosító jogkivonatokat `/token` a végpontról szerzi be, az [engedélyezési kód folyamathasználatával](openid-connect.md#get-a-token)történik, amely a jogkivonatot elrejti a böngészőből.

## <a name="claims"></a>Jogcímek

Az Azure AD B2C használatakor részletesen szabályozhatja a jogkivonatok tartalmát. Beállíthatja [a felhasználói folyamatok](user-flow-overview.md) és [az egyéni házirendek](custom-policy-overview.md) küldeni bizonyos felhasználói adatok az alkalmazáshoz szükséges jogcímek. Ezek a jogcímek olyan szabványos tulajdonságokat tartalmazhatnak, mint a **displayName** és **az emailAddress**. Az alkalmazások ezeket a jogcímeket a felhasználók és kérések biztonságos hitelesítésére használhatják.

Az azonosító jogkivonatokban lévő jogcímek nem adott sorrendben kerülnek visszaadásra. Új jogcímek bármikor bevezethetők az azonosító jogkivonatokban. Az alkalmazás nem bontható, mint az új jogcímek kerülnek bevezetésre. Egyéni felhasználói [attribútumokat](user-flow-custom-attributes.md) is felvehet a jogcímekbe.

Az alábbi táblázat felsorolja az okat a jogcímeket, amelyek az Azure AD B2C által kiadott azonosítójogtokban és hozzáférési jogkivonatokban számíthatnak.

| Név | Jogcím | Példaérték | Leírás |
| ---- | ----- | ------------- | ----------- |
| Célközönség | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | A jogkivonat címzettjét azonosítja. Az Azure AD B2C esetében a közönség az alkalmazásazonosító. Az alkalmazás nak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha nem egyezik. A célközönség az erőforrás szinonimája. |
| Kiállító | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS), amely felépíti, és visszaadja a jogkivonatot. Azt a könyvtárat is azonosítja, amelyben a felhasználó hitelesítésre került. Az alkalmazásnak ellenőriznie kell a kibocsátó jogcímét, hogy megbizonyosodjon arról, hogy a jogkivonat a megfelelő végpontról származik. |
| Kiadva: | `iat` | `1438535543` | A token kiadásának időpontja, amely et a korszak ban ábrázolt. |
| Lejárati idő | `exp` | `1438539443` | A token érvénytelenné válásának időpontja, amely et a korszak idővel ábrázolják. Az alkalmazás nak ezt a jogcímet kell használnia a jogkivonat élettartama érvényességének ellenőrzéséhez. |
| Előtte nem. | `nbf` | `1438535543` | A token érvényességének időpontja, amely et a korszak ban jelezve ábrázolják. Ez az idő általában megegyezik a jogkivonat kiadásának időpontjával. Az alkalmazás nak ezt a jogcímet kell használnia a jogkivonat élettartama érvényességének ellenőrzéséhez. |
| Verzió | `ver` | `1.0` | Az Azonosító jogkivonat verziója, az Azure AD B2C által meghatározott. |
| Kód kivonat | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Az azonosító jogkivonatban szereplő kódkivonat csak akkor, ha a jogkivonatot egy OAuth 2.0 engedélyezési kóddal együtt adják ki. Az engedélyezési kódok hitelességének ellenőrzésére kódkivonat használható. Az ellenőrzés végrehajtásáról az [OpenID Connect specifikációban](https://openid.net/specs/openid-connect-core-1_0.html)talál további információt.  |
| Hozzáférési jogkivonat-kivonat | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Egy hozzáférési jogkivonat-kivonat csak akkor, ha a jogkivonat egy OAuth 2.0 hozzáférési jogkivonattal együtt kerül kiadásra. A hozzáférési jogkivonat-kivonat egy hozzáférési jogkivonat hitelességének ellenőrzésére használható. Az ellenőrzés végrehajtásáról az [OpenID Connect specifikációban](https://openid.net/specs/openid-connect-core-1_0.html) talál további információt.  |
| Nonce között | `nonce` | `12345` | A nonce egy olyan stratégia, amely et a token visszajátszási támadások enyhítésére használják. Az alkalmazás megadhat egy nonce egy `nonce` engedélyezési kérelem a lekérdezési paraméter használatával. A kérelemben megadott értéket a program csak `nonce` egy azonosító jogkivonat jogcímében változatlanul bocsátja ki. Ez a jogcím lehetővé teszi, hogy az alkalmazás ellenőrizze az értéket a kérelemben megadott értékkel szemben. Az alkalmazásnak végre kell hajtania ezt az ellenőrzést az azonosítótoken-érvényesítési folyamat során. |
| Tárgy | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | A fő, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra és nem használható fel újra. Az engedélyezési ellenőrzések biztonságos végrehajtására használható, például amikor a jogkivonatot egy erőforrás eléréséhez használják. Alapértelmezés szerint a tulajdonosjogcím a címtárban lévő felhasználó objektumazonosítójával van feltöltve. |
| Hitelesítési környezet osztályának hivatkozása | `acr` | Nem alkalmazható | Csak régebbi házirendekkel használható. |
| Megbízhatósági keretpolitika | `tfp` | `b2c_1_signupsignin1` | Az azonosító jogkivonat megszerzéséhez használt házirend neve. |
| Hitelesítési idő | `auth_time` | `1438535543` | Az az időpont, amikor a felhasználó utoljára megadta a hitelesítő adatokat, és amely et a megadott időpontban. Nincs megkülönböztetés a között, hogy a hitelesítés egy friss bejelentkezés, egyszeri bejelentkezési (SSO) munkamenet, vagy egy másik bejelentkezési típus. Az `auth_time` az utolsó alkalom, hogy az alkalmazás (vagy felhasználó) hitelesítési kísérletet kezdeményezett az Azure AD B2C ellen. A hitelesítéshez használt módszer nem differenciált. |
| Hatókör | `scp` | `Read`| Az erőforrás nak egy hozzáférési jogkivonathoz megadott engedélyek. A többszörösen megadott engedélyeket szóköz választja el egymástól. |
| Felhatalmazott fél | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A kérést kezdeményező ügyfélalkalmazás **alkalmazásazonosítója.** |

## <a name="configuration"></a>Konfiguráció

Az alábbi tulajdonságok az Azure AD B2C által kibocsátott [biztonsági jogkivonatok élettartamának kezelésére](configure-tokens.md) szolgálnak:

- **Access &-azonosító-jogkivonat élettartama (perc)** – Az OAuth 2.0-s tulajdonosi jogkivonat élettartama, amely a védett erőforráshoz való hozzáféréshez használható. Az alapértelmezett érték 60 perc. A minimális (beleértve) 5 perc. A maximális (beleértve) 1440 perc.

- **Frissítési jogkivonat élettartama (nap)** – Az a maximális időtartam, amely előtt egy frissítési jogkivonat új hozzáférési vagy id-jogkivonat megszerzéséhez használható. Az időszak magában foglalja egy új frissítési jogkivonat `offline_access` beszerzését is, ha az alkalmazás megkapta a hatókört. Az alapértelmezett érték 14 nap. A minimális (bezárólag) egy nap. A maximális (beleértve) 90 nap.

- **Token csúszó ablak élettartamának frissítése (nap)** – Ezen időszak letelte után a felhasználó kénytelen újrahitelesíteni, függetlenül az alkalmazás által beszerzett legutóbbi frissítési jogkivonat érvényességi idejétől. Csak akkor adható meg, ha a kapcsoló **kötött.** Nagyobbnak vagy egyenlőnek kell lennie a **frissítési jogkivonat élettartama (nap)** értékével. Ha a kapcsoló **nincs korlátozás nélkül**értékre van állítva, nem adhat meg konkrét értéket. Az alapértelmezett érték 90 nap. A minimális (bezárólag) egy nap. A maximális (beleértve) 365 nap.

A következő használati esetek engedélyezve vannak a következő tulajdonságok használatával:

- Lehetővé teszi a felhasználó számára, hogy határozatlan ideig bejelentkezve maradjon egy mobilalkalmazásba, feltéve, hogy a felhasználó folyamatosan aktív az alkalmazásban. Beállíthatja **a frissítési jogkivonat csúszó ablak élettartama (nap)** **a korlátlan** a bejelentkezési felhasználói folyamat.
- A megfelelő hozzáférési jogkivonat-élettartam beállításával megfelelt az iparág biztonsági és megfelelőségi követelményeinek.

Ezek a beállítások nem érhetők el a jelszó-visszaállításfelhasználói folyamatokhoz.

## <a name="compatibility"></a>Kompatibilitás

A [tokenkompatibilitás kezelésére](configure-tokens.md)a következő tulajdonságok használhatók:

- **Kiállítói (iss) jogcím** – Ez a tulajdonság azonosítja az Azure AD B2C bérlő, amely kiadta a jogkivonatot. Az alapértelmezett érték `https://<domain>/{B2C tenant GUID}/v2.0/`. Az érték `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` tartalmazza az azonosítók az Azure AD B2C bérlő és a felhasználói folyamat, amely a jogkivonat-kérelemben használt. Ha az alkalmazásnak vagy a tárnak szüksége van az Azure AD B2C-re, hogy megfeleljen az [OpenID Connect Discovery 1.0 specifikációnak,](https://openid.net/specs/openid-connect-discovery-1_0.html)használja ezt az értéket.

- **Tárgy (al) jogcím** – Ez a tulajdonság azonosítja azt az entitást, amelyhez a jogkivonat adatokat állít. Az alapértelmezett érték az **ObjectID**, `sub` amely feltölti a jogcímet a jogkivonatban a felhasználó objektumazonosítójával. A **Nem támogatott** érték csak a visszamenőleges kompatibilitás érdekében van megadva. Javasoljuk, hogy a lehető leghamarabb váltson **objectid** azonosítóra.

- **Házirend-azonosítót képviselő jogcím** – Ez a tulajdonság azonosítja azt a jogcímtípust, amelyben a jogkivonat-kérelemben használt házirendnév fel van töltve. Az alapértelmezett érték `tfp`. Az érték `acr` csak a visszamenőleges kompatibilitás érdekében van megadva.

## <a name="pass-through"></a>Továbbítás

Amikor egy felhasználói út elindul, az Azure AD B2C egy hozzáférési jogkivonatot kap egy identitásszolgáltatótól. Az Azure AD B2C ezt a jogkivonatot használja a felhasználó adatainak lekéréséhez. [Engedélyezi a jogcímet a felhasználói folyamatban,](idp-pass-through-user-flow.md) vagy [az egyéni szabályzatban egy jogcímet definiál,](idp-pass-through-custom.md) hogy a jogkivonatot továbbítsa az Azure AD B2C-ben regisztrált alkalmazásoknak. Az alkalmazás nak [egy v2 felhasználói folyamatot](user-flow-versions.md) kell használnia a jogkivonat jogcímként való átadása érdekében.

Az Azure AD B2C jelenleg csak az OAuth 2.0 identitásszolgáltatók hozzáférési jogkivonatának átadását támogatja, beleértve a Facebookot és a Google-t. Az összes többi identitásszolgáltató esetében a jogcím üresen kerül visszaadásra.

## <a name="validation"></a>Ellenőrzés

Egy jogkivonat érvényesítéséhez az alkalmazás nak ellenőriznie kell a jogkivonat aláírását és jogcímeket is. Számos nyílt forráskódú kódtár áll rendelkezésre a JWT-k érvényesítéséhez, a kívánt nyelvtől függően. Javasoljuk, hogy vizsgálja meg ezeket a lehetőségeket, nem pedig a saját érvényesítési logika megvalósítása.

### <a name="validate-signature"></a>Aláírás ellenőrzése

A JWT három szegmenst, egy *fejlécet,* egy *törzset*és egy *aláírást*tartalmaz. Az aláírási szegmens a jogkivonat hitelességének ellenőrzésére használható, hogy az alkalmazás megbízható legyen. Az Azure AD B2C-jogkivonatok at az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például az RSA 256 segítségével vannak aláírva.

A token fejléce a jogkivonat aláírásához használt kulcsra és titkosítási módszerre vonatkozó információkat tartalmazza:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Az **alg** jogcím értéke a jogkivonat aláírásához használt algoritmus. A **kid** jogcím értéke a jogkivonat aláírásához használt nyilvános kulcs. Az Azure AD B2C bármikor aláírhat egy jogkivonatot a nyilvános és titkos kulcspárok bármelyikkészletének használatával. Az Azure AD B2C rendszeresen elforgatja a lehetséges kulcsokat. Az alkalmazást meg kell írni, hogy ezeket a kulcsváltozásokat automatikusan kezelni. Az Azure AD B2C által használt nyilvános kulcsok frissítéseinek ésszerű gyakorisága 24 óránként.

Az Azure AD B2C rendelkezik egy OpenID Connect metaadat-végpont. Ezzel a végponthasználatával az alkalmazások futásidőben kérhetnek információkat az Azure AD B2C-ről. Ez az információ végpontokat, jogkivonat-tartalmat és jogkivonat-aláíró kulcsokat tartalmaz. Az Azure AD B2C-bérlő minden szabályzathoz tartalmaz egy JSON-metaadat-dokumentumot. A metaadat-dokumentum egy JSON-objektum, amely számos hasznos információt tartalmaz. A metaadatok **jwks_uri**tartalmaznak , amely megadja a jogkivonatok aláírásához használt nyilvános kulcsok készletének helyét. Ez a hely itt érhető el, de a legjobb, ha dinamikusan lekéri a helyet a metaadat-dokumentum és a **jwks_uri**elemzésével:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Az ezen az URL-címen található JSON-dokumentum tartalmazza az adott pillanatban használatban lévő összes nyilvános kulcsinformációt. Az alkalmazás használhatja a `kid` jogcímet a JWT fejlécében, hogy válassza ki a nyilvános kulcsot a JSON-dokumentumban, amely egy adott jogkivonat aláírására szolgál. Ezután a megfelelő nyilvános kulcs és a megadott algoritmus használatával végrehajthatja az aláírás-ellenőrzést.

A házirend `B2C_1_signupsignin1` metaadat-dokumentuma `contoso.onmicrosoft.com` a következő helyen található:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Annak meghatározásához, hogy melyik házirendet használták a jogkivonat aláírásához (és hová kell menni a metaadatok kérésére), két lehetősége van. Először is, a szabályzat `acr` neve szerepel a jogcím a jogkivonatban. A JWT testéből úgy elemezheti a jogcímeket, hogy a 64-es alap dekódolja a testet, és deszerializálja az eredményeket eredményező JSON-karakterláncot. A `acr` jogcím a jogkivonat kiadásához használt házirend neve. A másik lehetőség az, hogy a házirendet a paraméter értékében kódolja a `state` kérelem kiadásakor, majd dekódolja azt, hogy melyik házirendet használta. Mindkét módszer érvényes.

Az aláírás-ellenőrzés végrehajtásának leírása nem tartozik a dokumentum hatálya alá. Számos nyílt forráskódú kódtár áll rendelkezésre a jogkivonat érvényesítéséhez.

### <a name="validate-claims"></a>Jogcímek ellenőrzése

Amikor az alkalmazások vagy API kap egy azonosító jogkivonatot, azt is több ellenőrzést kell végrehajtania az azonosító jogkivonat jogcímekkel szemben. A következő állításokat kell ellenőrizni:

- **közönség** - Ellenőrzi, hogy az azonosító jogkivonatot az alkalmazásnak szánták-e.
- **nem előtt** és **lejárati idő** - Ellenőrzi, hogy az azonosító jogkivonat nem járt le.
- **kibocsátó** – Ellenőrzi, hogy a jogkivonatot az Azure AD B2C adta-e ki az alkalmazásnak.
- **nonce** - A stratégia token visszajátszás támadás enyhítése.

Az alkalmazás által végrehajtandó ellenőrzések teljes listáját az [OpenID Connect specifikációban tájékozhatja.](https://openid.net)

## <a name="next-steps"></a>További lépések

További információ a [hozzáférési jogkivonatok használatáról.](access-tokens.md)

