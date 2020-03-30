---
title: Bevezetés az Azure Cosmos DB Table API-ba
description: Megtudhatja, hogyan használhatja az Azure Cosmos DB-t nagy mennyiségű kulcsértékű adat tárolására és lekérdezésére alacsony késleltetéssel az Azure Tables API használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 5b2e2c51eaa878ba0ce8bc31c001575acebe6919
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240155"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Alapvető ismeretek az Azure Cosmos DB tábla API szolgáltatásáról

Az [Azure Cosmos DB](introduction.md) Table API-t biztosít olyan, az Azure Table Storage-hoz írt alkalmazások számára, amelyekhez a következő prémium képességek szükségesek:

* [Kulcsrakész globális elosztás](distribute-data-globally.md).
* [Dedikált teljesítmény](partition-data.md) világszerte.
* Az esetek 99%-ában egyszámjegyű ezredmásodperces késés.
* Garantáltan magas szintű rendelkezésre állás.
* Automatikus másodlagos indexelés.

Ezek az Azure Table Storage számára írt alkalmazások kódmódosítás nélkül migrálhatók az Azure Cosmos DB-be a Table API-val, és ki tudják használni a prémium képességek előnyeit. A Table API a .NET, a Java, a Python és a Node.js környezetekhez is rendelkezik ügyfél-SDK-val.

> [!IMPORTANT]
> A .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) karbantartási módban van, és hamarosan elavult. Frissítsen az új .NET Standard kódtárra, [a Microsoft.Azure.Cosmos.Table-re,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) hogy továbbra is megkapja a Table API által támogatott legújabb funkciókat.

## <a name="table-offerings"></a>Table ajánlatok
Ha jelenleg az Azure Table Storage szolgáltatást használja, az alábbi előnyökben részesülhet az Azure Cosmos DB Table API-ra való áttéréskor:

| | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Késés | Gyors, de nincs felső korlátja a késésnek. | Egyszámjegyű ezredmásodperces késleltetés olvasások és írások, <10 ms késés olvasási és írási olvasási és írási a 99 percentilis, bármilyen mértékben, bárhol a világon. |
| Teljesítmény | Változó teljesítménymodell. A táblák skálázhatósági korlátja másodpercenként 20 000 művelet. | Hatékonyan skálázható a [táblánként dedikált és fenntartott átviteli sebességgel](request-units.md), amelynek rendelkezésre állását SLA-k szavatolják. A fiókokban nincs korlátozva az átviteli sebesség felső határa, és a szolgáltatás táblánként és másodpercenként legalább 10 millió műveletet támogat. |
| Globális terjesztés | Egyetlen régió egyetlen választható olvasható, másodlagos olvasási régióval a magas szintű rendelkezésre állásért. Nem kezdeményezhető feladatátvétel. | [Kulcsrakész globális elosztás](distribute-data-globally.md) egytől tetszőleges számú régióig. Támogatja az [automatikus és manuális feladatátvételt](high-availability.md) bármikor és bárhol a világon. Többfős képesség, amely lehetővé teszi bármely régió számára az írási műveletek elfogadását. |
| Indexelés | Csak elsődleges indexelés a PartitionKey és a RowKey tulajdonságok esetén. Nincsenek másodlagos indexek. | Automatikus és teljes indexelés az összes tulajdonságon alapértelmezés szerint, indexkezelés nélkül. |
| Lekérdezés | A lekérdezés végrehajtásakor az elsődleges kulcshoz tartozó indexet használja, és egyéb esetben csak vizsgálati műveletet végez. | A lekérdezések a gyorsaság céljából kihasználhatják a tulajdonságok automatikus indexelését. |
| Konzisztencia | Erős az elsődleges régióban, végleges a másodlagos régióban. | [Öt jól definiált konzisztenciaszint](consistency-levels.md) a rendelkezésre állás, a késés, az átviteli képesség és a konzisztencia az alkalmazás igényeinek megfelelően. |
| Díjszabás | Tárolásra optimalizált. | Átviteli sebességre optimalizált. |
| SLA-k | 99,9–99,99%-os rendelkezésre állás, a replikációs stratégiától függően. | 99,999%-os olvasási elérhetőség, 99,99%-os írási elérhetőség egy régiós fiókon és 99,999%-os írási elérhetőség a többrégiós fiókokon. [Átfogó SLA-k,](https://azure.microsoft.com/support/legal/sla/cosmos-db/) amelyek lefedik a rendelkezésre állást, a késést, az átviteli és konzisztenciát. |

## <a name="get-started"></a>Bevezetés

Hozzon létre egy Azure Cosmos DB-fiókot az [Azure Portalon](https://portal.azure.com). Ezután első lépésként hajtsa végre a [Table API .NET-keretrendszerrel való használatának rövid útmutatóját](create-table-dotnet.md). 

> [!IMPORTANT]
> Ha az előzetes verzióban hozta létre a Table API-fiókot, hozzon létre egy [új Table API-fiókot](create-table-dotnet.md#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
>

## <a name="next-steps"></a>További lépések

Íme, pár hivatkozás az első lépések megtételéhez:
* [.NET-alkalmazás létrehozása a Table API-val](create-table-dotnet.md)
* [Fejlesztés a Table API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md)
* [Táblaadatok lekérdezése a Table API-val](tutorial-query-table.md)
* [Tudnivalók az Azure Cosmos DB globális terjesztésének beállításáról a Table API-val](tutorial-global-distribution-table.md)
* [Azure Cosmos DB tábla .NET standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB tábla .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Java SDK-tábla](table-sdk-java.md)
* [Azure Cosmos DB tábla node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB Table SDK Pythonhoz](table-sdk-python.md)