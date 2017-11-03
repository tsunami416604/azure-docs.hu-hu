---
title: "Az Azure CDN szabályok motor-referencia |} Microsoft Docs"
description: "Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine"></a>Az Azure CDN szabálymotor
Ez a témakör részletes leírását tartalmazza a rendelkezésre álló egyezés feltételek és a szolgáltatások az Azure Content Delivery Network (CDN) [szabálymotor](cdn-rules-engine.md).

A HTTP szabálymotor lett kialakítva, hogy hogyan adott típusú kérelmet a végső hatóság dolgozza fel a CDN-t.

**Gyakori használati**:

- Bírálja felül, vagy egy egyéni gyorsítótár-házirend meghatározásához.
- Biztonságos, vagy letiltja a bizalmas a tartalomhoz.
- Kérelmek átirányítása.
- Egyéni adatok tárolására.

## <a name="terminology"></a>Terminológia
Egy szabály van meghatározva használatával [ **feltételes kifejezések**](cdn-rules-engine-reference-conditional-expressions.md), [ **megfelelő**](cdn-rules-engine-reference-match-conditions.md), és [  **szolgáltatások**](cdn-rules-engine-reference-features.md). Ezeket az elemeket az alábbi ábrán vannak kiemelve.

 ![CDN-egyeztetés feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelni módon miként egy egyeznek az állapot vagy a szolgáltatás kezeli a szöveges értékek függően változik. Egy egyeznek az állapot vagy a szolgáltatás szöveg értelmezhetik a következő módszerek valamelyikével:

1. [**Szöveges értékek**](#literal-values) 
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**A reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Szöveges értékek
Szöveg, amelyet a rendszer könyvtárelválasztóként értelmezi konstans érték % szimbólum kivételével minden speciális karakterek kezeli az értéket, amelyet egyeztetni részeként. Más szóval szövegkonstans felel meg a feltétel beállítása `\'*'\` csak kell teljesíteni, ha, amely pontos értékek (azaz `\'*'\`) található.
 
A százalékos szimbólum meghatározására szolgál URL-kódolást (pl. `%20`).

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek
Szöveg, amelyet egy helyettesítő karakteres értékként értelmezi további jelentése rendel különleges karaktereket. A következő táblázat ismerteti, hogyan kell értelmezni a következő set karaktereket.

Karakter | Leírás
----------|------------
\ | Fordított perjel karaktert a ebben a táblázatban megadott karaktereket. Egy fordított perjel közvetlenül a speciális karaktert, érdemes lehet escape-karaktersorozatot tartalmazó előtt meg kell adni.<br/>Például a következő szintaxissal lehet kilépni csillag:`\*`
% | A százalékos szimbólum meghatározására szolgál URL-kódolást (pl. `%20`).
* | A csillag karakter egy vagy több karaktert jelölő helyettesítő elemként jelen.
Lemezterület | A szóköz karakter azt jelzi, hogy egyeznek feltétel lehet teljesíteni vagy a megadott értékek vagy mintákat.
"érték" | Szimpla idézőjel nincs speciális jelentéssel. Azonban szimpla idézőjelben készlete szolgál annak jelzésére, hogy egy érték konstansérték kell kezelni. A következőképpen használhatók:<br><br/>-, Akkor meg kell, amikor csak a megadott értéke megegyezik az összehasonlítási érték bármely részének egyezés feltételt.  Például `'ma'` megfelelő a következő karakterláncok bármelyikét: <br/><br/>/Business/**ma**rathon/asset.htm<br/>**Ma**p.gif<br/>/ üzleti/sablont. **ma**p<br /><br />-Lehetővé teszi egy speciális karakter konstans karakterként adni. Például adhatnak meg szövegkonstans szóköz karakter a szóköz karakter a szimpla idézőjelben belül befoglaló (azaz `' '` vagy `'sample value'`).<br/>-Lehetővé teszi egy üres értéket kell megadni. Adjon meg egy üres értéket az szimpla idézőjelben csoportja (azaz ").<br /><br/>**Fontos:**<br/>-Ha a megadott érték nem tartalmaz helyettesítő karakter, majd azt automatikusan minősül konstans érték. Ez azt jelenti, hogy nem kell adjon meg olyan szimpla idézőjelben.<br/>– Ha egy fordított perjel nem karaktert egy másik ebben a táblázatban, majd azt figyelmen kívül a szimpla idézőjelben belül megadott.<br/>-Adjon meg egy speciális karaktert, mert használatával egy fordított perjel karaktert a literális karakter másik módja (azaz `\`).

### <a name="regular-expressions"></a>A reguláris kifejezések

A reguláris kifejezések egy mintát, amely egy szöveges értéket belül keresendő határozza meg. Reguláris kifejezés notation adott jelentését szimbólumok számos különböző határozza meg. A következő táblázat azt jelzi, hogyan különleges karakterek feltételek egyeznek és reguláris kifejezések támogató szolgáltatások kezeli.

Speciális karakter | Leírás
------------------|------------
\ | Egy fordított perjel lehet kilépni a karaktert, a következő lépések azt. Ez azt eredményezi, hogy ez a karakter, ahelyett, hogy a reguláris kifejezés jelentését konstansérték kell kezelni. Például a következő szintaxissal lehet kilépni csillag:`\*`
% | A százalékos szimbólum pontos jelentése attól függ, hogy a használatát.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis egy HTTP-változó azonosítja.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis százalékos szimbólum változó és a elválasztó HTTP azonosítására használ.<br />`\%`: Escape százalékos szimbólum lehetővé teszi, hogy konstans érték használandó vagy URL-kódolást jelzi (pl. `\%20`).
* | A csillag karakter lehetővé teszi, hogy az előző karaktert megfeleltethetők nulla vagy több alkalommal. 
Lemezterület | A szóköz karakter konstans karakterként általában rendszer kezeli. 
"érték" | Szimpla idézőjelben literális karaktereket tekintendők. Szimpla idézőjelben készlete nem rendelkezik speciális jelentéssel.


## <a name="next-steps"></a>Következő lépések
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
