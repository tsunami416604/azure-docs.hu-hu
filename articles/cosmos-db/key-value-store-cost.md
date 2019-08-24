---
title: Az Azure Cosmos DB egy kulcs-érték tárolóként kérelem egységekre vonatkozó díjakon
description: További információk az Azure Cosmos DB kérelem egységekre vonatkozó díjakon az egyszerű írási és olvasási műveletek, amikor egy kulcs/érték tároló használatban van.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012799"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB Key Value Store-ban – Cost Overview

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás magas rendelkezésre állású, nagy méretű alkalmazások létrehozását, egyszerűen a. Alapértelmezés szerint a Azure Cosmos DB automatikusan és hatékonyan indexeli az összes betöltött adatot. Ez gyors és konzisztens [SQL](how-to-sql-query.md) -(és [JavaScript](stored-procedures-triggers-udfs.md)-) lekérdezéseket tesz lehetővé az adatkezeléshez. 

Ez a cikk ismerteti az Azure Cosmos DB költsége egyszerű írási és olvasási műveletek, ha használatban van egy kulcs/érték tároló. Az írási műveletek közé tartoznak az adatelemek beszúrása, cseréje, törlése és upsert. A többrégiós fiókok esetében a 99,999%-os rendelkezésre állási SLA garantálása mellett Azure Cosmos DB a esetek 99% percentilis esetében garantált < 10 – MS késést biztosít az olvasási és a (indexelt) írásokhoz. 

## <a name="why-we-use-request-units-rus"></a>Miért kérelemegység (RU) használjuk.

Azure Cosmos DB teljesítmény a [kérelmek egységében](request-units.md) (ru/s) kifejezett kiépített átviteli sebességen alapul. A kiépítés második részletességgel történik, és RU/s-ben vásárolható meg ([nem tévesztendő össze az óránkénti számlázással](https://azure.microsoft.com/pricing/details/cosmos-db/)). Az RUs logikai absztrakciónak (pénznemnek) tekintendő, amely leegyszerűsíti az alkalmazás szükséges átviteli sebességének kiépítési folyamatát. A felhasználóknak nem kell meggondolnia az olvasási és írási sebesség közötti különbségtételt. Az egyetlen pénznem modell kérelemegységet hoz létre, növelhető a hatékonyság a kiépített lemezkapacitás olvasási és írási közötti megosztásához. Ez a kiépített kapacitási modell lehetővé teszi, hogy a szolgáltatás **kiszámítható és konzisztens teljesítményt, garantált kis késést és magas rendelkezésre állást**biztosítson. Végül, míg az RU-modell az átviteli sebességet ábrázolja, minden egyes kiépített RU-nek meghatározott mennyiségű erőforrása van (például memória, magok/CPU és IOPS).

Globálisan elosztott adatbázisrendszer a Cosmos db az egyetlen Azure-szolgáltatás, amely átfogó SLA-kat biztosít a késés, az átviteli sebesség, a konzisztencia és a magas rendelkezésre állás érdekében. A rendszer az Ön által kiépített átviteli sebességet alkalmazza a Cosmos-fiókhoz társított összes régióra. Az olvasási, Cosmos DB több jól definiált kínál [konzisztenciaszintek](consistency-levels.md) a közül választhat. 

A következő táblázat az olvasási és írási műveletek végrehajtásához szükséges RUs számát mutatja az 1 KB-os és a 100-os Tudásbázis adatelem alapján.

|Elem mérete|1 olvasása|1 írási|
|-------------|------|-------|
|1 KB|1 RU|5 Kérelemegységet|
|100 KB|10 RU|50 kérelemegység|

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költség

Ha 1 000 RU/s-t hoz létre, ez a 3 600 000 RU/óra értékre vonatkozik, és az órát (az USA és Európa területén) $0,08 számítjuk fel. 1 KB méretű adatelem esetén ez azt jelenti, hogy 3 600 000 olvasási vagy 720 000 írást (3 600 000 RU/5) használhat a kiépített átviteli sebesség használatával. Az olvasások és írások normalizálása a következő lesz: $0,022/millió ($0,08/3,6) és $0.111/millió írási érték ($0,08/0,72). A költség / millió válik minimális, az alábbi táblázatban látható módon.

|Elem mérete|1 000 000 olvasás díja|1 000 000 írási díja|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


A legtöbb a alapszintű blobot vagy objektum tárolók szolgáltatások ingyenesen 0,40 $ / millió olvasási tranzakciós és 5 $ / millió írási tranzakció. Optimális használat esetén Cosmos DB akár 98%-kal olcsóbb lehet, mint a többi megoldásnál (1 KB-os tranzakció esetén).

## <a name="next-steps"></a>További lépések

* Az [ru-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülheti meg a számítási feladatok átviteli sebességét.

