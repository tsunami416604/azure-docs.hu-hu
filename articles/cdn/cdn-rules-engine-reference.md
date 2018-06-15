---
title: Az Azure CDN szabályok motor-referencia |} Microsoft Docs
description: Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások.
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
ms.openlocfilehash: 602b4303dd1940791c11b8b71ac6a27f0474a6d5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733679"
---
# <a name="azure-cdn-rules-engine-reference"></a>Az Azure CDN szabályok motor-hivatkozás
Ez a cikk részletes leírását tartalmazza a rendelkezésre álló egyezés feltételek és a szolgáltatások számára az Azure Content Delivery Network (CDN) [szabálymotor](cdn-rules-engine.md).

A szabályok motor lett kialakítva, hogy hogyan adott típusú kérelmet a végső hatóság dolgozza fel a CDN-t.

**Gyakori használati**:

- Bírálja felül, vagy egy egyéni gyorsítótár-házirend meghatározásához.
- Biztonságos, vagy letiltja a bizalmas a tartalomhoz.
- Kérelmek átirányítása.
- Egyéni adatok tárolására.

## <a name="terminology"></a>Terminológia
Egy szabály van meghatározva használatával [ **feltételes kifejezések**](cdn-rules-engine-reference-conditional-expressions.md), [ **feltételek egyeznek**](cdn-rules-engine-reference-match-conditions.md), és [ **szolgáltatások**](cdn-rules-engine-reference-features.md). Ezeket az elemeket az alábbi ábrán vannak kiemelve:

 ![CDN-egyeztetés feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

Speciális karakterek kezelik, amelyben módon miként egy egyeznek az állapot vagy a szolgáltatás kezeli a szöveges értékek függően változik. Egy egyeznek az állapot vagy a szolgáltatás szöveg értelmezhetik a következő módszerek valamelyikével:

1. [**Szöveges értékek**](#literal-values) 
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**A reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Szöveges értékek
Szöveg, amelyet a rendszer könyvtárelválasztóként értelmezi konstans érték % szimbólum kivételével minden speciális karaktereket kezeli az értéket, amelyet egyeztetni részeként. Más szóval szövegkonstans felel meg a feltétel beállítása `\'*'\` csak teljesül, hogy pontos értékek amikor (Ez azt jelenti, hogy `\'*'\`) található.
 
A százalékos szimbólum meghatározására szolgál URL-kódolást (például `%20`).

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek
Szöveg helyettesítő értékként értelmezi, speciális karakterek további jelentése rendel hozzá. A következő táblázat ismerteti, hogyan karakterek a következők emelendők értelmezi:

Karakter | Leírás
----------|------------
\ | Fordított perjel karaktert a ebben a táblázatban megadott karaktereket. Egy fordított perjel közvetlenül a speciális karaktert, érdemes lehet escape-karaktersorozatot tartalmazó előtt meg kell adni.<br/>Például a következő szintaxissal lehet kilépni csillag: `\*`
% | A százalékos szimbólum meghatározására szolgál URL-kódolást (például `%20`).
* | A csillag karakter egy vagy több karaktert jelölő helyettesítő elemként jelen.
Szóköz | A szóköz karakter azt jelzi, hogy egyeznek feltétel lehet teljesíteni vagy a megadott értékek vagy mintákat.
"érték" | Szimpla idézőjel nincs speciális jelentéssel. Azonban szimpla idézőjelben készlete szolgál annak jelzésére, hogy egy érték konstansérték kell kezelni. A következőképpen használhatók:<br><br/>-, Akkor meg kell, amikor csak a megadott értéke megegyezik az összehasonlítási érték bármely részének egyezés feltételt.  Például `'ma'` megfelelő a következő karakterláncok bármelyikét: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ üzleti/sablont. **ma**p<br /><br />-Lehetővé teszi egy speciális karakter konstans karakterként adni. Például adhatnak meg szövegkonstans szóköz karakter a szóköz karakter a szimpla idézőjelben belül befoglaló (Ez azt jelenti, hogy `' '` vagy `'sample value'`).<br/>-Lehetővé teszi egy üres értéket kell megadni. Adjon meg egy üres értéket az szimpla idézőjelben csoportja (Ez azt jelenti, hogy ").<br /><br/>**Fontos:**<br/>-Ha a megadott érték nem tartalmaz helyettesítő karakter, majd automatikusan minősül konstans érték, amely azt jelenti, hogy nem kell adjon meg olyan szimpla idézőjelben.<br/>– Ha egy fordított perjel nem karaktert egy másik ebben a táblázatban, akkor rendszer figyelmen kívül hagyja a szimpla idézőjelben belül van megadva.<br/>-Adjon meg egy speciális karaktert, mert használatával egy fordított perjel karaktert a literális karakter másik módja (Ez azt jelenti, hogy `\`).

### <a name="regular-expressions"></a>Reguláris kifejezések

Reguláris kifejezések meghatározása egy mintát, amely egy szöveges értéket belül kell keresni. Reguláris kifejezés notation adott jelentését szimbólumok számos különböző határozza meg. A következő táblázat azt jelzi, hogyan különleges karakterek feltételek egyeznek és reguláris kifejezések támogató szolgáltatások kezeli.

Speciális karakter | Leírás
------------------|------------
\ | Egy fordított perjel lehet kilépni a karaktert, a következő lépések informatikai, amely azt eredményezi, ez a karakter, ahelyett, hogy a reguláris kifejezés jelentését konstansérték kell kezelni. Például a következő szintaxissal lehet kilépni csillag: `\*`
% | A százalékos szimbólum pontos jelentése attól függ, hogy a használatát.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis egy HTTP-változó azonosítja.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis százalékos szimbólum változó és a elválasztó HTTP azonosítására használ.<br />`\%`: Escape százalékos szimbólum lehetővé teszi, hogy konstans érték használandó vagy URL-kódolást jelzéséhez (például `\%20`).
* | A csillag karakter lehetővé teszi, hogy az előző karaktert megfeleltethetők nulla vagy több alkalommal. 
Szóköz | A szóköz karakter konstans karakterként általában rendszer kezeli. 
"érték" | Szimpla idézőjelben literális karaktereket tekintendők. Szimpla idézőjelben készlete nem rendelkezik speciális jelentéssel.


## <a name="next-steps"></a>További lépések
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata HTTP működés felülbírálásához](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
