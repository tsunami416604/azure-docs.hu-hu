---
title: Azure CDN Standard Rules Engine-hivatkozás | Microsoft Docs
description: Dokumentáció a Azure CDN standard szabályok motorjának egyeztetési feltételeiről és műveleteiről.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615935"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Azure CDN a Microsoft Rules Engine-referenciából

Ez a cikk az Azure Content Delivery Network (CDN) [szabványos szabályok motorjának](cdn-standard-rules-engine.md)elérhető egyeztetési feltételeinek és funkcióinak részletes leírását tartalmazza.

A szabályok motor úgy lett kialakítva, hogy az adott típusú kérelmeknek a CDN általi feldolgozásának végső hatósága legyen.

**Gyakori felhasználások**:

- Egyéni gyorsítótár-szabályzat felülbírálása vagy definiálása.
- Átirányítási kérelmek.
- HTTP-kérelem és válasz fejlécének módosítása

## <a name="terminology"></a>Terminológia

Egy szabály az [**egyeztetési feltételek**](cdn-standard-rules-engine-match-conditions.md)és a [**műveletek**](cdn-standard-rules-engine-actions.md)használatával van definiálva. Ezek az elemek a következő ábrán vannak kiemelve:

 ![CDN-szabályok szerkezete](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Mindegyik szabály legfeljebb 4 egyezési feltételt és 3 műveletet tartalmazhat. CDN-végponton legfeljebb 5 szabály adható meg. Emellett az alapértelmezett szabály a **globális szabály**. Ez olyan szabály, amely nem felel meg a feltételeknek, ahol a belül definiált műveletek mindig aktiválva lesznek. Ez a szabály a jelenlegi 5 szabályhoz tartozó korlátozásban szerepel.

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelésének módja attól függően változik, hogy a egyezési feltétel vagy a actopm hogyan kezeli a szöveges értékeket. Az egyeztetési feltétel vagy szolgáltatás a következő módokon értelmezheti a szöveget:

1. [**Literális értékek**](#literal-values)
2. [**Helyettesítő karakteres értékek**](#wildcard-values)


### <a name="literal-values"></a>Literális értékek

A literál értékként értelmezett szöveg az összes speciális karaktert kezeli, a (z)% Symbol kivételével, az értéknek megfelelő részeként. Ez azt jelenti, hogy az `\'*'\`re beállított konstans egyezési feltétel csak akkor teljesül, ha a pontos érték (azaz `\'*'\`) található.

A százalékos aláírás az URL-kódolás (például `%20`) jelzésére szolgál.

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg a speciális karakterek további jelentését rendeli hozzá. A következő táblázat a következő karakterkészletek értelmezését ismerteti:

Karakter | Leírás
----------|------------
\ | A program fordított perjelet használ a táblázatban megadott karakterek bármelyikének megmeneküléséhez. Meg kell adni egy fordított perjelet közvetlenül a kikerülő különleges karakter előtt.<br/>Az alábbi szintaxis például megmenekül egy csillaggal: `\*`
% | A százalékos aláírás az URL-kódolás (például `%20`) jelzésére szolgál.
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
Űr | A szóköz karakter azt jelzi, hogy egy egyezési feltétel teljesül a megadott értékek vagy minták valamelyikével.
érték | Egyetlen árajánlat nem rendelkezik speciális jelentéssel. Az aposztrófok készlete azonban azt jelzi, hogy egy értéket literál értékként kell kezelni. A szolgáltatás a következő módokon használható:<br><br/>– Lehetővé teszi az egyeztetési feltétel teljesülését, ha a megadott érték megegyezik az összehasonlítási érték bármely részével.  A `'ma'` például a következő karakterláncok bármelyikének felel meg: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />– Lehetővé teszi, hogy egy speciális karakter literál karakterként legyen megadva. Megadhat például egy literál szóköz karaktert úgy, hogy az idézőjelek közé helyez egy szóközt (azaz `' '` vagy `'sample value'`).<br/>– Lehetővé teszi, hogy a rendszer üres értéket ad meg. Adja meg az üres értéket egy idézőjelek készletének megadásával (azaz: "").<br /><br/>**Fontos:**<br/>– Ha a megadott érték nem tartalmaz helyettesítő karaktert, a rendszer automatikusan literál értékként veszi figyelembe, ami azt jelenti, hogy nem szükséges egyetlen idézőjelet megadni.<br/>– Ha egy fordított perjel nem kerül egy másik karakterbe a táblázatban, figyelmen kívül hagyja a rendszer, ha egy idézőjelek készletében van megadva.<br/>– Egy speciális karakter literál karakterként való megadásának másik módja, ha egy fordított perjelet (azaz `\`) használ.

## <a name="next-steps"></a>További lépések

- [Szabványos szabályok motorjának egyeztetési feltételei](cdn-standard-rules-engine-match-conditions.md)
- [Szabványos szabályok motor műveletei](cdn-standard-rules-engine-actions.md)
- [HTTPS kikényszerítés a standard szabályok motor használatával](cdn-standard-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
