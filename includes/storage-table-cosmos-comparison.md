---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 93bb0a853199b8b1fc38b8cc903af0a434cab034
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67179658"
---
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API-ra való áttéréskor:

| | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek. | Egy számjegyű ezredmásodperces késés az olvasás/írás műveleteknél – az olvasási műveleteknél 10 ms alatti, az írási műveleteknél 15 ms alatti késés garantált az esetek 99%-ában bármilyen méret esetén, bárhol a világon. |
| Átviteli sebesség | Változó teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet. | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](../articles/cosmos-db/request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókokban nincs korlátozva az átviteli sebesség felső határa, és a szolgáltatás táblánként és másodpercenként legalább 10 millió műveletet támogat. |
| Globális terjesztés | Egyetlen régió egyetlen választható olvasható, másodlagos olvasási régióval a magas szintű rendelkezésre állásért. Nem kezdeményezhető feladatátvétel. | [Kulcsrakész globális terjesztés](../articles/cosmos-db/distribute-data-globally.md) legalább 1–30 régióból. Támogatja az [automatikus és manuális feladatátvételt](../articles/cosmos-db/high-availability.md) bármikor és bárhol a világon. |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek. | Az összes tulajdonság automatikus és teljes indexelése, nincs indexkezelés. |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban. | [Öt jól meghatározott konzisztencia-szint](../articles/cosmos-db/consistency-levels.md) a rendelkezésre állás, a késés, az átviteli sebesség és a konzisztencia kikapcsolásához az alkalmazás igényei alapján. |
| Díjszabás | Tárolásra optimalizált. | Átviteli sebességre optimalizált. |
| SLA-k | 99,99%-os rendelkezésre állás. | 99,99%-os rendelkezésre állású SLA minden enyhén korlátozott konzisztenciájú egyrégiós és többrégiós fiókhoz, valamint 99,999%-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz [Iparágvezető és átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/) az általános rendelkezésre állásra vonatkozóan. |
