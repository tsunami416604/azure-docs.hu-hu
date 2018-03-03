---
title: "Azure Cosmos DB: SQL .NET Core API-t, az SDK és erőforrások |} Microsoft Docs"
description: "Tudnivalók az SQL .NET Core API és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB .NET Core SDK verziói között végrehajtott módosításokat."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/21/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8604863b9394c5af9494c47f821601441ecee308
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB .NET Core SDK API-SQL: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[.NET API-referenciadokumentáció](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Minták**</td><td>[.NET-Kódminták](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés az Azure Cosmos DB .NET Core-SDK-val](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Webes alkalmazás oktatóanyag**</td><td>[Webalkalmazás fejlesztése a Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td>[.NET szabványos 1.6-os és .NET szabványos 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

Az Azure Cosmos DB .NET Core SDK legújabb verziójával szolgáltatásparitást rendelkezik a [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md).

> [!NOTE] 
> Az Azure Cosmos DB .NET Core SDK még nem kompatibilis az univerzális Windows Platform (UWP-) alkalmazásokat. Ha érdekli a .NET Core-SDK-t támogató UWP-alkalmazások, e-mailek küldése [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Rögzített, hibát talált az egyes versenyhelyzetek, amely időszakos eredményez "Microsoft.Azure.Documents.NotFoundException: az olvasási munkamenet nem érhető el a bemeneti munkameneti jogkivonat" hibák munkamenet konzisztenciaszint használatakor.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Rögzített regressziós ahol FeedOptions.MaxItemCount = -1 egy System.ArithmeticException kivételt okozott: méretet a rendszer negatív.
* Egy új ToString() függvényét QueryMetrics hozzá.
* A gyűjtemények olvasása a partíció statisztikai adatainak kitéve.
* ChangeFeedOptions hozzáadott PartitionKey tulajdonság.
* Kisebb hibajavítások.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Hozzáadja a meghatározhatja, hogy a dokumentumok egyedi indexet a DocumentCollection UniqueKeyPolicy tulajdonság használatával.
 * Rögzített, amelyben az egyéni JsonSerializer beállítások volt nem alatt figyelembe véve az egyes lekérdezések és a tárolt eljárás végrehajtása hiba.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Márkajelzési Azure Cosmos Adatbázist az API-referencia az Azure DocumentDB változásait, dokumentáció, szerelvényekben metaadat-információkat és a NuGet-csomagot. 
 * Diagnosztikai adatok és a késleltetés közvetlen kapcsolatot módban küldött kérelmek a válaszban szereplő tenni. A tulajdonságnevek megkülönböztetik RequestDiagnosticsString és RequestLatency ResourceResponse osztályban.
 * Az SDK-verzió telepítéséhez a legújabb Azure Cosmos DB emulátorának elérhetősége a https://aka.ms/cosmosdb-emulator letölthető.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Több megbízhatóság javításokat és fejlesztések hozzá.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Kapcsolódó belső változások támogató [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Mint a lekérdezési eredmények kulcs adott partíció tartományértéke hatókörének egy FeedOption PartitionKeyRangeId támogatása. 
* StartTime, keressen a módosítások idő lejárta után egy ChangeFeedOption támogatása. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Rögzített problémát okozhat a verem túlcsordulásos kivétel JsonSerializable osztályból.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Egyéni JsonSerializerSettings megadása közben példányának támogatása egy [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) példány.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   A cél keretrendszerek rendelkezésre álló támogatása a .NET-szabvány 1.5.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Rögzített x64 érintő problémát gépek, amelyek nem támogatják a SSE4 utasítást, és SEHException throw Azure Cosmos DB lekérdezések futtatásakor.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Egy új konzisztenciaszint támogatása ConsistentPrefix nevezik.
*   Az egyes partíciók lekérdezési metrikák támogatása.
*   Támogatja a lekérdezések a folytatási kód mérete korlátozza.
*   Sikertelen kérelmek nyomkövetése részletesebb támogatása.
*   Az SDK-val végzett néhány teljesítménynövekedést.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Megtörtént egy probléma javítása, amely figyelmen kívül hagyta a megadott FeedOptions összesített lekérdezések PartitionKey érték.
* Megtörtént egy probléma javítása átlátszó kezelésében, partíció felügyeleti közepes repülési kereszt-partíció Order By lekérdezés végrehajtása közben.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Rögzített egyes az async API-k használatakor az ASP.NET környezet belül holtpont okozó problémát.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Kijavítja a SDK rugalmasabb, így az Automatikus feladatátvétel bizonyos körülmények között.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Javítsa ki a WebException alkalmanként okozó hibát: A távoli név nem sikerült feloldani.
* Támogatása a típusos dokumentumok közvetlenül olvasásakor ReadDocumentAsync API új túlterhelések hozzáadásával.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* LINQ támogatása (COUNT, MIN, MAX, SUM és átlagos) összesítési lekérdezések.
* Javítsa ki a problémát az az oka eseménykezelő ConnectionPolicy objektum.
* Javítsa ki a hibát, amelynek UpsertAttachmentAsync lett nem működik ETag használatakor.
* Javítsa ki a hibát, amelynek több partíciót az order by lekérdezés folytatási nem dolgozott rendezéskor karakterláncmező.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Összesítési lekérdezéseket (COUNT, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-api-sql-query.md#Aggregates).
* Minimális átviteli sebességet 2500 RU/mp 10,100 RU/mp a particionált gyűjtemények szintűre csökkent.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Az Azure Cosmos DB .NET Core SDK lehetővé teszi összeállítását gyors és platformfüggetlen [ASP.NET Core](https://www.asp.net/core) és [.NET Core](https://www.microsoft.com/net/core#windows) Windows, Mac és Linux futtatni. Az Azure Cosmos DB .NET Core SDK legújabb kiadása van teljesen [Xamarin](https://www.xamarin.com) kompatibilis és monó (Linux), iOS és Android alkalmazások összeállításához kell használni.  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Az Azure Cosmos DB .NET Core Preview SDK lehetővé teszi összeállítását gyors és platformfüggetlen [ASP.NET Core](https://www.asp.net/core) és [.NET Core](https://www.microsoft.com/net/core#windows) Windows, Mac és Linux futtatni.

A Azure Cosmos DB .NET Core Preview SDK tartalmazza a legújabb verziójával szolgáltatásparitást a [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) és a következőket támogatja:
* Minden [csatlakozási mód](performance-tips.md#networking): átjáró módban, a közvetlen TCP és a közvetlen HTTPs. 
* Minden [konzisztenciaszintek](consistency-levels.md): erős, munkamenet, amelyet az elavulási és Eventual.
* [A particionált gyűjtemények](partition-data.md). 
* [Több területi adatbázis fiókok és georeplikáció](distribute-data-globally.md).

Ha az SDK-val kapcsolatos kérdése van, továbbá hogy [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb), vagy a fájl a problémát a [github-tárházban](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.8.2](#1.8.2) |2018. február 21. |--- |
| [1.8.1](#1.8.1) |2018. február 05. |--- |
| [1.7.1](#1.7.1) |2017. november 16. |--- |
| [1.7.0](#1.7.0) |2017. november 10. |--- |
| [1.6.0](#1.6.0) |2017. október 17. |--- |
| [1.5.1](#1.5.1) |2017. október 02. |--- |
| [1.5.0](#1.5.0) |2017. augusztus 10. |--- | 
| [1.4.1](#1.4.1) |2017. augusztus 07. |--- |
| [1.4.0](#1.4.0) |2017. augusztus 02. |--- |
| [1.3.2](#1.3.2) |2017. június 12. |--- |
| [1.3.1](#1.3.1) |2017. május 23. |--- |
| [1.3.0](#1.3.0) |2017. május 10. |--- |
| [1.2.2](#1.2.2) |2017. április 19. |--- |
| [1.2.1](#1.2.1) |2017. március 29. |--- |
| [1.2.0](#1.2.0) |2017. március 25. |--- |
| [1.1.2](#1.1.2) |2017. március 20. |--- |
| [1.1.1](#1.1.1) |2017. március 14. |--- |
| [1.1.0](#1.1.0) |2017. február 16. |--- |
| [1.0.0](#1.0.0) |2016. december 21. |--- |
| [0.1.0-preview](#0.1.0-preview) |2016. november 15. |2016. december 31. |

## <a name="see-also"></a>Lásd még:
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

