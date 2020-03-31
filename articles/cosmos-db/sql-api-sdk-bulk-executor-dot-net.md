---
title: 'Azure Cosmos DB: Tömeges végrehajtó .NET API, SDK & erőforrások'
description: Tudjon meg mindent a tömeges végrehajtó .NET API és SDK beleértve a kiadási dátumokat, a megszüntetési dátumokat és az Azure Cosmos DB tömeges végrehajtó .NET SDK egyes verziói között végrehajtott módosításokat.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169401"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET tömeges végrehajtó tár: Letöltési információk 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Többi](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Leírás**| A .Net tömeges végrehajtó könyvtár lehetővé teszi, hogy az ügyfélalkalmazások tömeges műveleteket hajtsanak végre az Azure Cosmos DB-fiókokon. Ez a tár bulkimport, bulkupdate és bulkdelete névtereket biztosít. A BulkImport modul tömegesen betudja adni a dokumentumokat optimalizált módon, így a gyűjteményhez kiépített átviteli teljesítmény a lehető legnagyobb mértékben felhasználásra kerül. A BulkUpdate modul tömegesen frissítheti a meglévő adatokat az Azure Cosmos-tárolókban javításként. A BulkDelete modul tömegesen törölheti a dokumentumokat optimalizált módon, hogy a gyűjteményhez kiépített átviteli teljesítmény a lehető legnagyobb mértékben felhasználásra kerüljön.|
|**SDK letöltés**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Tömeges végrehajtó könyvtár a GitHubon**| [Github](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentáció**|[.NET API referenciadokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Első lépések**|[Első lépések a tömeges végrehajtó könyvtár (.NET SDK) segítségével](bulk-executor-dot-net.md)|
| **Jelenlegi támogatott keretrendszer**| Microsoft . |

> [!NOTE]
> Tömeges végrehajtó használata esetén tekintse meg a [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md)legújabb 3.x verzióját, amelynek az SDK-ba beépített tömeges végrehajtója van beépítve. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-előnézet

* Javítva: TotalElapsedTime a BulkDelete válaszában, hogy pontosan mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-előnézet

* Az SDK-függőség módosítása >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Hozzáadott támogatása grafikon tömeges végrehajtó elfogadja ttl a csúcsok és élek

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Kijavítottuk azt a problémát, amely az Azure Cosmos DB rugalmas méretezése során kivételeket okozott, amikor átjáró módban fut. Ez a javítás funkcionálisan egyenértékűvé teszi az 1.4.1 kiadásnak.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* BulkDelete támogatás hozzáadva az SQL API-fiókokhoz a partíciókulcs, a törlendő dokumentumazonosítók elfogadásához. Ez a változás funkcionálisan egyenértékűvé teszi az 1.4.0 kiadással.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Beleértve a MongoBulkExecutor t a .NET Standard 2.0 támogatásával. Ez a funkció funkcionálisan egyenértékűvé teszi az 1.3.0 kiadással, a .NET Standard 2.0 támogatásával.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-előzetes

* A .NET Standard 2.0 a támogatott célkeretrendszerek egyikeként adható hozzá, hogy a tömeges végrehajtó könyvtár működjön a .NET Core alkalmazásokkal.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Kijavítottuk a MongoBulkExecutor egyik problémánkat, amely a dokumentum méretének váratlan növelését okozta a kitöltés hozzáadásával, és bizonyos esetekben túllépte a dokumentum maximális méretkorlátját.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Kijavítottuk a BulkDeleteAsync hibáját, amikor a gyűjtemény beágyazta a partíciókulcs elérési útjait.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* A MongoBulkExecutor most már megvalósítja az IDisposable-ot, és használat után várhatóan ártalmatlanítani fog.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Eltávolítottzár az SDK verzióján. A csomag mostantól az SDK-tól függ, >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Az azonosítók kezelése rögzített, amikor a BulkImport-ot numerikus értékekkel rendelkező POCO-objektumok listájával hívta meg.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Javítva: TotalElapsedTime a BulkDelete válaszában, hogy pontosan mérje a teljes időt, beleértve az újrapróbálkozásokat is.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Rögzített magas CPU-fogyasztás bizonyos forgatókönyvek.
* A nyomkövetés mostantól tracesource-t használ. A felhasználók meghatározhatják a `BulkExecutorTrace` forrás figyelőit.
* Javítva egy ritka eset, amely a 2Mb méretű dokumentumok küldésekor zárolást okozhat.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Frissítve a tömeges végrehajtó, hogy most már az Azure Cosmos DB .NET SDK (2.4.0) legújabb verzióját használja

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Hozzáadott támogatása grafikon tömeges végrehajtó elfogadja ttl a csúcsok és élek

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Kijavítottuk azt a problémát, amely az Azure Cosmos DB rugalmas méretezése során kivételeket okozott, amikor átjáró módban fut.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* BulkDelete támogatás hozzáadva az SQL API-fiókokhoz a partíciókulcs, a törlendő dokumentumazonosítók elfogadásához.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Kijavítottunk egy problémát, amely a tömeges végrehajtó által használt felhasználói ügynök formázási problémája miatt történt.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* A tömeges végrehajtó importálása és az API-k frissítése a Cosmos-tároló rugalmas méretezéséhez való transzparens módon történő adaptálása érdekében, ha a tárolás kivételek nélkül meghaladja az aktuális kapacitást.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* A DocumentDB .NET SDK-függőség 2.1.3-as verzióra emelt.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Kijavítottuk azt a hibát, amely miatt a tömeges végrehajtó jsrt-hibát okozott a rögzített gyűjtemények importálása közben.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Az Azure Cosmos DB SQL API-fiókok BulkDelete művelettámogatása hozzáadva.
* Az Azure Cosmos DB MongoDB-hez való API-jával rendelkező fiókok bulkimport-műveletének támogatása.
* A DocumentDB .NET SDK-függőség 2.0.0-s verzióra emelt. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Az Azure Cosmos DB Gremlin API-fiókok BulkImport-művelet támogatása.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Kisebb hibajavítás az Azure Cosmos DB SQL API-fiókok BulkImport műveletéhez.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Az Azure Cosmos DB SQL API-fiókok BulkImport és BulkUpdate műveleteinek támogatása hozzáadva.

## <a name="next-steps"></a>További lépések

A tömeges végrehajtó Java-könyvtáráról az alábbi cikkben olvashat:

[Java tömeges végrehajtó könyvtár SDK és kiadási információk](sql-api-sdk-bulk-executor-java.md)
