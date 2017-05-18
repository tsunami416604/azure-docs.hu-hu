---
title: "Alapvető ismeretek az Azure Cosmos DB tábla API szolgáltatásáról | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy mennyiségű kulcs-érték típusú adatok tárolására és lekérdezésére a népszerű OSS MongoDB API-k használatával."
services: cosmosdb
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8226ecfb5fa8c70b162ae776679302db6cfdcdbc
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Alapvető ismeretek az Azure Cosmos DB tábla API szolgáltatásáról

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása kritikus fontosságú alkalmazások számára. Az Azure Cosmos DB [kulcsrakész globális terjesztést](../documentdb/documentdb-distribute-data-globally.md), [rugalmasan skálázható átviteli sebességet és a világ különböző pontjain lévő tárterületet](partition-data.md), egyszámjegyű ezredmásodperces késést a 99. percentilisben, [öt jól meghatározott konzisztenciaszintet](../documentdb/documentdb-consistency-levels.md), valamint garantáltan magas rendelkezésre állást biztosít – a zavartalan működést [iparágvezető SLA-k](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) szavatolják. Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), így Önnek nem kell séma- és indexkezeléssel foglalkoznia. Egy többmodelles szolgáltatásról van szó, amely támogatja a dokumentum, a kulcs-érték, a gráf és az oszlop típusú adatmodelleket. 

![Az Azure Table Storage API és az Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Az Azure Cosmos DB biztosítja a tábla API-t azoknak az alkalmazásoknak, amelyeknek rugalmas sémájú, kulcs-érték tárolóra van szükségük. Az [Azure Table Storage](../storage/storage-introduction.md) szolgáltatáshoz tartozó SDK-k és REST API-k képesek együttműködni az Azure Cosmos DB szolgáltatással. Az Azure Cosmos DB használatával nagy átviteli sebességet megkövetelő táblákat hozhat létre. Az Azure Cosmos DB jelenleg nyilvános előzetes verzióban támogatja az átviteli sebességre optimalizált táblákat (más néven „prémium szintű táblákat”). 

Az Azure Table Storage továbbra is használható nagy tárigényű és alacsonyabb átviteli sebességet megkövetelő táblákkal. Az Azure Cosmos DB hamarosan be fogja vezetni a tárolásra optimalizált táblák támogatását, valamint a meglévő és az új Azure Table Storage-fiókok Azure Cosmos DB szolgáltatásra való frissítése is meg fog történni.

## <a name="premium-and-standard-table-apis"></a>Prémium és standard szintű tábla API-k
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB előzetes verziójú, prémium szintű tábláira való áttéréskor:

|  | Azure Table Storage | Azure Cosmos DB: Table Storage (előzetes verzió) |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek | Egyszámjegyű ezredmásodperces késés az olvasás/írás műveleteknél – az olvasási műveleteknél 10 ms alatti, az írási műveleteknél 15 ms alatti késés garantált a 99. percentilisben bármilyen méret esetén, bárhol a világon |
| Átviteli sebesség | Hatékonyan skálázható, de nincs dedikált teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](../documentdb/documentdb-request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókokban nincs korlátozva az átviteli sebesség felső határa, és a szolgáltatás táblánként és másodpercenként legalább 10 millió műveletet támogat |
| Globális terjesztés | Egyetlen régió – egyetlen opcionális, olvasható, másodlagos olvasási régióval a magas rendelkezésre állás céljából. Nem kezdeményezhető feladatátvétel | [Kulcsrakész globális terjesztés](../documentdb/documentdb-distribute-data-globally.md) legalább 1–30 régióból. Támogatja az [automatikus és manuális feladatátvételt](../documentdb/documentdb-regional-failovers.md) – bármikor és bárhol a világon |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek | Az összes tulajdonság automatikus és teljes indexelése, nincs indexkezelés |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. Az Azure Cosmos DB adatbázismotorja összesítési, térinformatikai és rendezési lehetőségeket egyaránt támogat. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban | [Öt jól meghatározott konzisztenciaszint](../documentdb/documentdb-consistency-levels.md), amelyekkel az alkalmazás igényeinek megfelelően szabályozható a rendelkezésre állás, a késés, az átviteli sebesség és a konzisztencia |
| Díjszabás | Tárolásra optimalizált  | Átviteli sebességre optimalizált |
| SLA-k | 99,9%-os rendelkezésre állás | 99,99%-os rendelkezésre állás egyetlen régióban, valamint lehetőség van további régiók felvételére a magasabb rendelkezésre állás céljából. [Iparágvezető és átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) az általános rendelkezésre állásra vonatkozóan |

## <a name="how-to-get-started"></a>Első lépések

Hozzon létre egy Azure Cosmos DB-fiókot az [Azure Portalon](https://portal.azure.com), és tegye meg az első lépéseket a [Tábla API a .NET használatával című rövid útmutató](create-table-dotnet.md) segítségével. 

## <a name="next-steps"></a>Következő lépések

Íme, pár hivatkozás az első lépések megtételéhez:
* Ismerkedés az [Azure Cosmos DB tábla API szolgáltatásával](create-table-dotnet.md) meglévő .NET tábla SDK esetén.
* További információ az [Azure Cosmos DB szolgáltatással történő globális terjesztésről](../documentdb/documentdb-distribute-data-globally.md).
* További információ az [Azure Cosmos DB-ben kiépített átviteli sebességről](../documentdb/documentdb-request-units.md).
