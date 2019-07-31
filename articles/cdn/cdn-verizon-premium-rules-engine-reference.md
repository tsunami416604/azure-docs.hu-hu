---
title: A Azure CDN Rules Engine referenciája | Microsoft Docs
description: A Azure CDN szabályok motorjának a feltételekkel és szolgáltatásokkal kapcsolatos dokumentációja.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593146"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN a Verizon Premium Rules motor referenciája

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)elérhető egyeztetési feltételeinek és funkcióinak részletes leírását tartalmazza.

A szabályok motor úgy lett kialakítva, hogy az adott típusú kérelmeknek a CDN általi feldolgozásának végső hatósága legyen.

**Gyakori felhasználások**:

- Egyéni gyorsítótár-szabályzat felülbírálása vagy definiálása.
- Bizalmas tartalomra vonatkozó kérések biztonságos vagy megtagadása.
- Átirányítási kérelmek.
- Egyéni naplófájlok tárolása.

## <a name="terminology"></a>Terminológia

A szabályok a [**feltételes kifejezések**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), a [**egyezési feltételek**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)és a [**funkciók**](cdn-verizon-premium-rules-engine-reference-features.md)használatával lettek definiálva. Ezek az elemek a következő ábrán vannak kiemelve:

 ![CDN-egyeztetési feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelésének módja attól függően változik, hogy a egyezési feltétel vagy szolgáltatás milyen módon kezeli a szöveges értékeket. Az egyeztetési feltétel vagy szolgáltatás a következő módokon értelmezheti a szöveget:

1. [**Literális értékek**](#literal-values)
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**Reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Literális értékek

A literál értékként értelmezett szöveg az összes speciális karaktert kezeli, a (z)% Symbol kivételével, az értéknek megfelelő részeként. Más szóval a konstans egyezési feltétel `\'*'\` csak akkor teljesül, ha a pontos érték (azaz `\'*'\`) megtalálható.

A százalékos szimbólum az URL-kódolás (például `%20`:) jelölésére szolgál.

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg a speciális karakterek további jelentését rendeli hozzá. A következő táblázat a következő karakterkészletek értelmezését ismerteti:

Karakter | Leírás
----------|------------
\ | A program fordított perjelet használ a táblázatban megadott karakterek bármelyikének megmeneküléséhez. Meg kell adni egy fordított perjelet közvetlenül a kikerülő különleges karakter előtt.<br/>Az alábbi szintaxis például megmenekül egy csillaggal:`\*`
% | A százalékos szimbólum az URL-kódolás (például `%20`:) jelölésére szolgál.
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
Szóköz | A szóköz karakter azt jelzi, hogy egy egyezési feltétel teljesül a megadott értékek vagy minták valamelyikével.
érték | Egyetlen árajánlat nem rendelkezik speciális jelentéssel. Az aposztrófok készlete azonban azt jelzi, hogy egy értéket literál értékként kell kezelni. A szolgáltatás a következő módokon használható:<br><br/>– Lehetővé teszi az egyeztetési feltétel teljesülését, ha a megadott érték megegyezik az összehasonlítási érték bármely részével.  Például `'ma'` a következő sztringek bármelyike megfelel: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />– Lehetővé teszi, hogy egy speciális karakter literál karakterként legyen megadva. Megadhat például egy literál szóköz karaktert úgy, `' '` hogy az idézőjelek közé helyezi a szóköz karaktert (azaz vagy `'sample value'`).<br/>– Lehetővé teszi, hogy a rendszer üres értéket ad meg. Adja meg az üres értéket egy idézőjelek készletének megadásával (azaz: "").<br /><br/>**Fontos:**<br/>– Ha a megadott érték nem tartalmaz helyettesítő karaktert, a rendszer automatikusan literál értékként veszi figyelembe, ami azt jelenti, hogy nem szükséges egyetlen idézőjelet megadni.<br/>– Ha egy fordított perjel nem kerül egy másik karakterbe a táblázatban, figyelmen kívül hagyja a rendszer, ha egy idézőjelek készletében van megadva.<br/>– Egy speciális karakter literál karakterként való megadásának másik módja, ha egy fordított perjelet (azaz `\`) használ.

### <a name="regular-expressions"></a>Reguláris kifejezések

A reguláris kifejezések határozzák meg a szöveges értéken belül keresendő mintát. A reguláris kifejezések jelölése meghatározott kifejezéseket definiál a különböző szimbólumokhoz. A következő táblázat azt mutatja be, hogyan kezeli a speciális karaktereket a reguláris kifejezéseket támogató feltételek és funkciók egyeztetésével.

Speciális karakter | Leírás
------------------|------------
\ | Egy fordított perjel elmenekül a következő karakterrel, ami azt eredményezi, hogy a karaktert literál értékként kell kezelni, ahelyett, hogy a reguláris kifejezés jelentését kellene használnia. Az alábbi szintaxis például megmenekül egy csillaggal:`\*`
% | A százalékos szimbólum jelentése a használattól függ.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis egy HTTP-változót azonosít.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis egy százalékos szimbólum használatával azonosítja a HTTP-változót és elválasztóként.<br />`\%`: A százalékos szimbólum megmenekülése lehetővé teszi, hogy literál értékként vagy URL-kódolással jelezze (például `\%20`:).
\* | A csillag lehetővé teszi, hogy az előző karakter nulla vagy több alkalommal legyen összepárosítva.
Szóköz | A szóköz karaktert általában literál karakterként kezeli a rendszer.
érték | Az aposztrófok literál karakterekként lesznek kezelve. Az idézőjelek halmaza nem rendelkezik speciális jelentéssel.

## <a name="next-steps"></a>További lépések

- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)