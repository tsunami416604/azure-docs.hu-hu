---
title: Az Azure CDN szabálymotor-referencia |} A Microsoft Docs
description: Az Azure CDN dokumentációja szabályok szabálymotor egyezési feltételei és funkciókat.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 3163b33f69f4cc2d6cd4127253c7b6fadfddd6b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994231"
---
# <a name="azure-cdn-rules-engine-reference"></a>Az Azure CDN szabálymotor-referencia
Ez a cikk felsorolja a részletes leírását, a rendelkezésre álló egyezési feltételei és a szolgáltatások esetében az Azure Content Delivery Network (CDN) [szabálymotorral](cdn-rules-engine.md).

A rules engine tervezték hogyan adott típusú kérelmet a végső hatóság dolgozza fel a CDN-t.

**Gyakori használati**:

- Bírálja felül, vagy egy egyéni gyorsítótár-szabályzat meghatározása.
- Biztonságos, vagy megtagadja a tartalomhoz bizalmas.
- Kérelmek átirányítása.
- Az egyéni napló data Store.

## <a name="terminology"></a>Terminológia
Egy szabály van meghatározva használatával [ **feltételes kifejezéseket**](cdn-rules-engine-reference-conditional-expressions.md), [ **feltételeknek megfelelő**](cdn-rules-engine-reference-match-conditions.md), és [ **funkciók**](cdn-rules-engine-reference-features.md). Ezeket az elemeket az alábbi ábrán vannak kiemelve:

 ![CDN egyezési feltételei](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelik, amelyben módon hogyan egy egyezési feltétellel vagy a szolgáltatás kezeli a szöveges értékek függően változik. Egy az egyezési feltétellel vagy a szolgáltatás értelmezhetik szöveg a következő módszerek valamelyikével:

1. [**Konstans érték**](#literal-values) 
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**Reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Konstans érték
Szöveg, amely szöveges értékként értelmezi a % szimbólum kivételével minden speciális karakterek az érték, amely egyeztetni részeként kezeli. Azaz szövegkonstansnak beállítása feltételnek megfelelő-e `\'*'\` csak meggyőződött róla, hogy pontos értékek amikor (azaz `\'*'\`) található.
 
Egy százalék szimbólummal használva URL-Címének kódolása azt (például `%20`).

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek
Szöveg, amely egy helyettesítő karaktert tartalmazó értéket kerül értelmezésre további jelentése rendel a különleges karaktereket. A következő táblázat ismerteti, hogyan értelmezi a következő karakterkészlet:

Karakter | Leírás
----------|------------
\ | Fordított perjel karaktert a jelen táblázatban lévő megadott karakterek egyikét sem. Egy fordított perjel elé a speciális karaktert kell lennie escape-karakterrel kell megadni.<br/>Ha például a következő szintaxist egy csillag lehet kilépni: `\*`
% | Egy százalék szimbólummal használva URL-Címének kódolása azt (például `%20`).
\* | Egy csillag, amely egy vagy több karaktert helyettesítő karakterként.
Űr | Szóköz karakter jelzi, hogy egyeztetési feltételt lehet teljesíteni a megadott értékek vagy minták egyikét.
"érték" | Jednoduchá uvozovka nincs kulcsszó különleges jelentéssel. Azonban egy aposztrófok használatos jelzi, hogy egy érték Szövegkonstansérték kell kezelni. Az alábbi módon használható:<br><br/>-Lehetővé teszi, ha a megadott értéke megegyezik az összehasonlítási érték bármely részének teljesítendő egyeztetési feltételt.  Ha például `'ma'` megfelel a következő karakterláncok bármelyikét: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ business/sablon. **ma**p<br /><br />-Lehetővé teszi egy speciális karaktert a literális karakter adható meg. Például megadhat egy literális karakter belül szimpla idézőjelek között egy készletét a szóköz karakter kötegfájlokban (azaz `' '` vagy `'sample value'`).<br/>-Lehetővé teszi egy üres értéket kell megadni. Adjon meg egy üres értéket szimpla idézőjelek között megadásával (vagyis ").<br /><br/>**Fontos:**<br/>– Ha a megadott érték nem tartalmaz helyettesítő karaktert, majd automatikusan tekintendő konstans érték, ami azt jelenti, hogy nem szükségesek, hogy adjon meg egy szimpla idézőjelek között.<br/>– Ha egy fordított perjel nem ez a táblázat egy másik karakterek elkerülésére, hogy rendszer figyelmen kívül hagyja a szimpla idézőjelek között belül van megadva.<br/>– Egy másik módja egy speciális karaktert adja meg, ahogyan a használatával egy fordított perjel karaktert a literális karakter (azaz `\`).

### <a name="regular-expressions"></a>Reguláris kifejezések

Reguláris kifejezések meghatározása egy mintát, amely egy szöveges érték belül kell keresni. Reguláris kifejezés jelöléssel meghatározott jelentéssel számos különböző szimbólumok határozza meg. Az alábbi táblázat azt jelzi, hogy milyen speciális karakterek egyezési feltételei és reguláris kifejezésekkel támogató szolgáltatások kezeli.

Speciális karakter | Leírás
------------------|------------
\ | Egy fordított perjel lehet kilépni a karakter, az alábbiak szerint informatikai, aminek a karakter konstans érték helyett a reguláris kifejezés jelentése szerinti kell kezelni. Ha például a következő szintaxist egy csillag lehet kilépni: `\*`
% | Egy százalék szimbólummal szerinti attól függ, hogy a használatát.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis egy HTTP-változó azonosítja.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis egy százalék szimbólummal változó és elválasztóként HTTP azonosítására használ.<br />`\%`: Escape-karaktersorozat százalékos szimbólum lehetővé teszi egy literálérték, használható vagy URL-Címének kódolása jelzi (például `\%20`).
\* | Csillag lehetővé teszi, hogy az előző karaktert egyezést kell keresni nulla vagy több alkalommal. 
Űr | Szóköz karakter általában számít literális karakter. 
"érték" | Literális karakter szimpla idézőjelek között kell kezelni. Egy készletét szimpla idézőjelek között nincs kulcsszó különleges jelentéssel.


## <a name="next-steps"></a>További lépések
* [Szabálymotor egyezési feltételei](cdn-rules-engine-reference-match-conditions.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor funkciói](cdn-rules-engine-reference-features.md)
* [A rules engine használatával HTTP a működés felülbírálása](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
