---
title: 'Az Azure Cosmos DB: SQL .NET Standard API, SDK & erőforrások'
description: Ismerkedjen meg az SQL API-val és a .NET SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és a Azure Cosmos DB .NET SDK egyes verzióiban végrehajtott módosításokat.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229023"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET Standard SDK az SQL API-hoz: Megjegyzések letöltése és kibocsátási megjegyzései
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API-dokumentáció**|[.NET API dokumentációja](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Példák**|[.NET platformra írt kódmintái](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Első lépések**|[Ismerkedés a Azure Cosmos DB .NET SDK-val](sql-api-get-started.md)|
|**Alapú webappokról szóló oktatóanyagunkat**|[Webalkalmazás-fejlesztés az Azure Cosmos DB használatával](sql-api-dotnet-application.md)|
|**Aktuális támogatott keretrendszer**|[Microsoft .NET Standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* A .NET SDK-hoz készült [3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) általánosan elérhető változata
* Cél .NET Standard 2,0, amely támogatja a .NET-keretrendszer 4.6.1 + és a .NET Core 2.0 + verzióját.
* Új objektummodell, amelynek legfelső szintű CosmosClient és módszerei a kapcsolódó adatbázis és tároló osztályok között oszlanak meg
* Új, nagy teljesítményű stream API-k
* Beépített támogatás a feed Processor API-k módosításához
* Fluent Builder API-k CosmosClient-hez, tárolóhoz és a adatcsatorna módosításához
* Köznyelvi átviteli sebesség felügyeleti API-k
* Részletes RequestOptions és ResponseTypes az adatbázishoz, a tárolóhoz, az elemekhez, a lekérdezési és adatátviteli kérelmekhez
* Nem particionált tárolók méretezésének lehetősége 
* Bővíthető és testreszabható szerializáló
* Kiterjeszthető kérelmek folyamata egyéni kezelők támogatásával


## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A szolgáltatás elutasítja a kivont SDK használatával Azure Cosmos DB kérelmeket.

<br/>

| Version | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [3.0.0](#3.0.0) |Július 15., 2019 |--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

