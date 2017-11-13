---
title: "Bevezetés az Azure Cosmos DB Table API használatába | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy mennyiségű kulcs-érték típusú adatok tárolására és lekérdezésére a népszerű OSS MongoDB API-k használatával."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2017
ms.author: arramac
ms.openlocfilehash: 68c9f37b6e241d39911acff9c12aa4c978b4215d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Alapvető ismeretek az Azure Cosmos DB tábla API szolgáltatásáról

Az [Azure Cosmos DB](introduction.md) Table API-t biztosít olyan, az Azure Table Storage-hoz írt alkalmazások számára, amelyekhez a következő prémium képességek szükségesek:

* [Kulcsrakész globális terjesztés](distribute-data-globally.md).
* [Dedikált teljesítmény](partition-data.md) világszerte.
* Az esetek 99%-ában egyszámjegyű ezredmásodperces késés.
* Garantáltan magas szintű rendelkezésre állás.
* [Automatikus másodlagos indexelés](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Ezek az Azure Table Storage számára írt alkalmazások kódmódosítás nélkül migrálhatók az Azure Cosmos DB-be a Table API-val, és ki tudják használni a prémium képességek előnyeit. .NET esetében a Table API-nak egyetlen ügyfél SDK-ja áll rendelkezésre.

Javasoljuk a következő videó megtekintését, amelyben Aravind Ramachandran az Azure Cosmos DB-hez készült Table API használatának első lépéseit ismerteti:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="table-offerings"></a>Table ajánlatok
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API-ra (előzetes verzió) való áttéréskor:

| | Azure Table Storage | Azure Cosmos DB Table API (előzetes verzió) |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek. | Egy számjegyű ezredmásodperces késés az olvasás/írás műveleteknél – az olvasási műveleteknél 10 ms alatti, az írási műveleteknél 15 ms alatti késés garantált az esetek 99%-ában bármilyen méret esetén, bárhol a világon. |
| Teljesítmény | Változó teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet. | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókokban nincs korlátozva az átviteli sebesség felső határa, és a szolgáltatás táblánként és másodpercenként legalább 10 millió műveletet támogat. |
| Globális terjesztés | Egyetlen régió egyetlen választható olvasható, másodlagos olvasási régióval a magas szintű rendelkezésre állásért. Nem kezdeményezhető feladatátvétel. | [Kulcsrakész globális terjesztés](distribute-data-globally.md) legalább 1–30 régióból. Támogatja az [automatikus és manuális feladatátvételt](regional-failover.md) bármikor és bárhol a világon. |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek. | Az összes tulajdonság automatikus és teljes indexelése, nincs indexkezelés. |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. Az Azure Cosmos DB adatbázismotorja összesítési, térinformatikai és rendezési lehetőségeket egyaránt támogat. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban. | [Öt jól meghatározott konzisztenciaszint](consistency-levels.md), amelyekkel az alkalmazás igényeinek megfelelően szabályozható a rendelkezésre állás, a késés, az átviteli sebesség és a konzisztencia. |
| Díjszabás | Tárolásra optimalizált. | Átviteli sebességre optimalizált. |
| SLA-k | 99,99%-os rendelkezésre állás. | 99,99%-os rendelkezésre állás egyetlen régióban, valamint lehetőség van további régiók felvételére a magasabb rendelkezésre állás céljából. [Iparágvezető és átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/) az általános rendelkezésre állásra vonatkozóan. |

## <a name="get-started"></a>Bevezetés

Hozzon létre egy Azure Cosmos DB-fiókot az [Azure Portalon](https://portal.azure.com). Ezután első lépésként hajtsa végre a [Table API .NET-keretrendszerrel való használatának rövid útmutatóját](create-table-dotnet.md). 

## <a name="next-steps"></a>Következő lépések

Íme, pár hivatkozás az első lépések megtételéhez:
* [.NET-alkalmazás létrehozása a Table API-val](create-table-dotnet.md)
* [Fejlesztés a Table API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md)
* [Táblaadatok lekérdezése a Table API-val](tutorial-query-table.md)
* [Tudnivalók az Azure Cosmos DB globális terjesztésének beállításáról a Table API-val](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table .NET API](table-sdk-dotnet.md)


