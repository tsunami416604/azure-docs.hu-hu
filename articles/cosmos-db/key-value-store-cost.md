---
title: Az Azure Cosmos DB kulcsérték-tárolójaként az egységdíjak igénylése
description: Ismerje meg az Azure Cosmos DB kérelemegység-díjait az egyszerű írási és olvasási műveletekhez, ha kulcs-/értéktárolóként használják.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647506"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Az Azure Cosmos DB mint kulcsfontosságú értéktár – költségáttekintés

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás magas rendelkezésre állású, nagy méretű alkalmazások egyszerű létrehozásához. Alapértelmezés szerint az Azure Cosmos DB automatikusan és hatékonyan indexeli az összes betöltési adatot. Ez lehetővé teszi a gyors és konzisztens [SQL](how-to-sql-query.md) (és [JavaScript)](stored-procedures-triggers-udfs.md)lekérdezések az adatokat. 

Ez a cikk ismerteti az Azure Cosmos DB egyszerű írási és olvasási műveletek költségét, ha kulcs/értéktárolóként használják. Az írási műveletek közé tartoznak az adatelemek beszúrásai, lecserélései, törlései és upserts.Write operations include inserts, replaces, deletes, and upserts of data items. Amellett, hogy garantálja a 99,999%-os rendelkezésre állási SLA az összes több régiós fiókok, az Azure Cosmos DB kínál garantált <10 ms késés olvasási és az (indexelt) írja, a 99percentile. 

## <a name="why-we-use-request-units-rus"></a>Miért használjuk a kérelemegységeket (RT)

Az Azure Cosmos DB teljesítménye a [kérelemegységekben](request-units.md) (RU/s) kifejezett kiosztott átviteli teljesítmény mennyiségén alapul. A kiépítés egy második granularitás, és vásárolt RU /s[(nem tévesztendő össze az óránkénti számlázás](https://azure.microsoft.com/pricing/details/cosmos-db/)). A Nemi us-okat logikai absztrakciónak (pénznemnek) kell tekinteni, amely egyszerűsíti az alkalmazás szükséges átviteli értékének kiépítését. A felhasználóknak nem kell gondolniuk az olvasási és írási átviteli sebességgel való különbségtételre. A VT-k közös pénznemmodellje hatékonyságot hoz létre a kiosztott kapacitás olvasási és írási műveletek közötti megosztásához. Ez a kiosztott kapacitásmodell lehetővé teszi, hogy a szolgáltatás **kiszámítható és konzisztens átviteli teljesítményt, garantált alacsony késést és magas rendelkezésre állást**biztosítson. Végül, míg a RU-modell átviteli kapacitás ábrázolására szolgál, minden egyes kiépített RU is rendelkezik egy meghatározott mennyiségű erőforrás (például memória, magok/CPU és IOPS).

Globálisan elosztott adatbázis-rendszerként a Cosmos DB az egyetlen Olyan Azure-szolgáltatás, amely átfogó SLA-kat biztosít a késés, az átviteli feszültség, a konzisztencia és a magas rendelkezésre állás lefedése érdekében. Az átviteli rendszer kiépítése a Cosmos-fiókhoz társított régiók mindegyikére vonatkozik. Olvasások, Cosmos DB kínál több, jól meghatározott [konzisztenciaszintek](consistency-levels.md) közül választhat. 

Az alábbi táblázat az 1 KB méretű és 100 KB méretű adatelemen alapuló olvasási és írási műveletek végrehajtásához szükséges, kikapcsolt alapértelmezett automatikus indexeléssel rendelkező termékkódokat mutatja be. 

|Cikk mérete|1 Olvasás|1 Írás|
|-------------|------|-------|
|1 KB|1 KT|5 rus|
|100 KB|10 RU|50 rus|

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költségek

Ha 1000 RU/s-ot épít ki, ez 3,6 millió RU/óra, és 0,08 usd-ba kerül az óra (az Egyesült Államokban és Európában). Egy 1 KB-os méretű adatelem esetén ez azt jelenti, hogy 3,6 millió olvasást vagy 0,72 millió írási (3,6 millió RU / 5) a kiosztott átviteli fót használva lehet felhasználni. Normalizálva millió olvasási és írási, a költség lenne 0,022 $ / millió olvasás ($ 0,08 / 3,6) és 0,111 $ / millió írások ($ 0,08 / 0,72). Az egymillióra jutó költség az alábbi táblázatban látható módon minimális lesz.

|Cikk mérete|Költsége 1 millió olvasás|Költsége 1 millió írja|
|-------------|-------|--------|
|1 KB|$0,022|$0,111|
|100 KB|$0,222|$1,111|


Az alapvető blob- vagy objektumtároló-szolgáltatások többsége 0,40 USD/millió olvasási tranzakciót és 5 millió usd írási tranzakciót számít fel. Optimális használat esetén a Cosmos DB akár 98%-kal olcsóbb is lehet, mint ezek a többi megoldás (1 KB-os tranzakciók esetén).

## <a name="next-steps"></a>További lépések

* A [RU-kalkulátor](https://cosmos.azure.com/capacitycalculator/) segítségével becsülje meg a számítási feladatok átviteli fának.

