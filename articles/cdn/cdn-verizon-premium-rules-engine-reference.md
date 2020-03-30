---
title: Azure CDN-szabályok motorhivatkozása | Microsoft dokumentumok
description: Az Azure CDN-szabályok motorjának referenciadokumentációja megfelel a feltételeknek és a funkcióknak.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996756"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN a Verizon Premium szabályok motorjának referencia

Ez a cikk részletes leírást tartalmaz az Azure Content Delivery Network (CDN) szabálymotorhoz rendelkezésre álló egyezési feltételekről és [funkciókról.](cdn-verizon-premium-rules-engine.md)

A szabálymotor tazon rendszer a végső hatóságként szolgál a CDN által feldolgozott konkrét típusú kérelmek feldolgozására vonatkozóan.

**Gyakori felhasználások:**

- Egyéni gyorsítótár-házirend felülbírálása vagy definiálása.
- Bizalmas tartalomra vonatkozó kérések biztonságossá tétele vagy megtagadása.
- Kérések átirányítása.
- Egyéni naplóadatok tárolása.

## <a name="terminology"></a>Terminológia

A szabály [**feltételes kifejezések**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**egyezési feltételek**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)és [**jellemzők**](cdn-verizon-premium-rules-engine-reference-features.md)használatával kerül meghatározásra . Ezek az elemek a következő ábrán jelennek meg:

 ![CDN egyezési feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelésének módja attól függően változik, hogy az egyezési feltétel vagy jellemző hogyan kezeli a szöveges értékeket. Az egyezési feltétel vagy szolgáltatás a szöveget az alábbi módokon értelmezheti:

1. [**Literális értékek**](#literal-values)
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**Reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Literális értékek

A literális értékként értelmezett szöveg az összes speciális karaktert kezeli, a % szimbólum kivételével, az egyeztetni kívánt érték részeként. Más szóval a literális `\'*'\` egyezési feltétel csak akkor teljesül, ha a pontos értéket `\'*'\`(azaz) megtalálja.

Az URL-kódolás (például) `%20`százalékos szimbóluma jelzi.

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg további jelentést rendel a speciális karakterekhez. Az alábbi táblázat a következő karakterkészlet értelmezését ismerteti:

Karakter | Leírás
----------|------------
\ | A fordított perjel a táblázatban megadott karakterek bármelyikének kikerülésére szolgál. A megkerülendő speciális karakter előtt közvetlenül meg kell adni egy fordított perjelet.<br/>A következő szintaxis például csillagot választ ki:`\*`
% | Az URL-kódolás (például) `%20`százalékos szimbóluma jelzi.
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
Space (Szóköz) | A szóköz karakter azt jelzi, hogy egy egyezési feltétel tegyeztethető ki a megadott értékek vagy minták bármelyikével.
"érték" | Az egyetlen idézőjelnek nincs különleges jelentése. Az egyszeres idézőjelek azonban azt jelzik, hogy egy értéket literális értékként kell kezelni. Ezt a következő módokon lehet használni:<br><br/>- Lehetővé teszi, hogy az egyezési feltétel akkor teljesüljön, amikor a megadott érték megegyezik az összehasonlítási érték bármely részével.  Például `'ma'` az alábbi karakterláncok bármelyikének megfelelne: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />- Ez lehetővé teszi, hogy egy speciális karaktert kell meghatározni, mint egy szó karakter. Megadhatja például a szó szerinti szóköz karaktert, ha egy szóközt `' '` egy `'sample value'`idézőjelek közé foglal (azaz vagy ) egy szóközt.<br/>- Lehetővé teszi egy üres érték megadást. Adjon meg egy üres értéket egy idézőjelek (azaz '') készlet ének megadásával.<br /><br/>**Fontos:**<br/>- Ha a megadott érték nem tartalmaz helyettesítő karaktert, akkor automatikusan literális értéknek minősül, ami azt jelenti, hogy nem szükséges egyszeres idézőjelek készletét megadni.<br/>- Ha a fordított perjel nem kerüli el a tábla egy másik karakterét, a rendszer figyelmen kívül hagyja, ha egy idézőjelek halmazában van megadva.<br/>- Egy másik módja annak, hogy adjon meg egy speciális karaktert, `\`mint egy szó szerinti karakter, hogy elkerülje azt egy fordított perjel (azaz, ).

### <a name="regular-expressions"></a>Reguláris kifejezések

A reguláris kifejezések olyan mintát határoznak meg, amelyet a program egy szöveges értéken belül keres. A reguláris kifejezés jelölése különböző szimbólumok konkrét jelentését határozza meg. Az alábbi táblázat azt mutatja be, hogy a rendszer hogyan kezeli a speciális karaktereket a reguláris kifejezéseket támogató feltételek és jellemzők szerint.

Különleges karakter | Leírás
------------------|------------
\ | A fordított perjel kikerüli az azt követő karaktert, ami azt eredményezi, hogy a karakter tanuskodik, ahelyett, hogy a reguláris kifejezés jelentését venné fel. A következő szintaxis például csillagot választ ki:`\*`
% | A százalékos szimbólum jelentése a használattól függ.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis http változót azonosít.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis egy százalékos szimbólumot használ a HTTP-változó azonosítására és határolójelként.<br />`\%`: A százalékos szimbólum kikerülése lehetővé teszi, hogy literális értékként vagy URL-kódolás jelzésére használják (például `\%20`).
\* | A csillag lehetővé teszi, hogy az előző karakter nulla vagy több alkalommal egyezzen.
Space (Szóköz) | A szóközkaraktereket általában literális karakterként kezeli a program.
"érték" | Az egyszeres idézőjeleket a karakterek konstans karakterekként kezelik. Az egyszeres idézőjelek halmazának nincs különleges jelentése.

A reguláris kifejezéseket támogató feltételek nek és szolgáltatásoknak megfeleltetik a Perl-kompatibilis reguláris kifejezések (PCRE) által meghatározott mintákat.

## <a name="next-steps"></a>További lépések

- [A szabálymotor megfelel a feltételeknek](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabályok motor jellemzői](cdn-verizon-premium-rules-engine-reference-features.md)
- [A HTTP viselkedésének felülbírálása a szabálymotor használatával](cdn-verizon-premium-rules-engine.md)
- [Az Azure CDN – áttekintés](cdn-overview.md)
