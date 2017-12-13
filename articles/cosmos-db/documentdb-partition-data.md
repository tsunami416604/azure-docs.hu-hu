---
title: "Particionálás és az Azure Cosmos Adatbázisba skálázás |} Microsoft Docs"
description: "Ismerje meg, hogyan particionálási működését Azure Cosmos DB, hogyan lehet konfigurálni a particionálás és kulcsok partícióazonosító és hogyan válassza ki a megfelelő partíciókulcs az alkalmazáshoz."
services: cosmos-db
author: rafats
manager: jhubbard
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 177d28850d57c9a43c22e79cfab3699b11f4d734
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Az Azure Cosmos-Adatbázisba az SQL API-val particionálás

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

[A Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) egy globális elosztott, több modellre adatbázis szolgáltatás célja, hogy gyors és kiszámítható teljesítmény és méretezhetőség zökkenőmentesen együtt az alkalmazás eléréséhez, növekedésével azt. 

Ez a cikk áttekintést Cosmos DB tároló SQL API-val particionálás használata. Lásd: [particionálás és horizontális skálázás](../cosmos-db/partition-data.md) fogalmakat és ajánlott eljárások az Azure Cosmos DB API-k a particionálás áttekintését. 

Első lépésként kóddal, töltse le a projektet a [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

A cikk elolvasása után lesz a következő kérdések megválaszolásához:   

* Hogyan működik az Azure Cosmos Adatbázisba particionálási munkahelyi?
* Hogyan konfigurálhatók a particionálás az Azure Cosmos Adatbázisba
* Mik azok a partíciós kulcsok, és hogyan tegye én választom ki a megfelelő partíciókulcs az alkalmazáshoz?

Első lépésként kóddal, töltse le a projektet a [Azure Cosmos DB teljesítményének tesztelése illesztőprogram minta](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Partíciós kulcsok

Az SQL API-t a partíciós kulcs definíciójában egy JSON-útvonal formájában kell megadni. Az alábbi táblázat példákat partíció fontos definíciókat és a megfelelő értékeket. A partíciós kulcs van megadva egy elérési utat, mint pl. `/department` jelenti. a tulajdonság részleg. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Partíciókulcs</strong></p></td>
            <td valign="top"><p><strong>Leírás</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>A cikk esetén a doc doc.department értékének felel meg.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ tulajdonságok/neve</p></td>
            <td valign="top"><p>Ahol doc-e az elem (beágyazott tulajdonság) doc.properties.name értékének felel meg.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Doc.id értéke megfelel (azonosító és a partíciós kulcs, amelyek ugyanahhoz a tulajdonsághoz).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "részleg neve"</p></td>
            <td valign="top"><p>Megfelel doc ["részleg neve"], ahol doc-e az elem értékét.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> A partíciós kulcs szintaxisa a következő elérési útja a legfontosabb különbség, hogy az elérési út a tulajdonság értéke nem felel meg házirend elérési utak indexelő hasonló, azaz nincs nem helyettesítő karakter végén. Például lehet megadni/részleg /? az index részleg alatt az értékeket, de adja meg a /department a partíciós kulcs definíciójában. A partíciós kulcs implicit módon indexelt, és nem zárható ki indexelő indexelési házirend felülbírálások használatával.
> 
> 

Nézzük milyen hatással van a partíciós kulcs kiválasztásakor az alkalmazás teljesítményét.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Az Azure Cosmos DB SDK-k használata
Az Azure Cosmos DB támogatása az automatikus particionálási [REST API verziója 2015-12-16](/rest/api/documentdb/). Particionált tárolók létrehozásához le kell töltenie 1.6.0 SDK verzió vagy újabb valamelyik támogatott SDK platformon (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Tárolók létrehozása
A következő példában egy .NET-részlet létrehozni egy tárolót a 20 000 kérelemegység / s átviteli eszköz telemetriai adatainak tárolásához. Az SDK-t állítja be a OfferThroughput (amelyek viszont beállítja a `x-ms-offer-throughput` kérelem fejléce a REST API-ban). Itt be van állítva a `/deviceId` partíciókulcsnak. A partíciós kulcs választott mentett a tároló metaadatait, például a nevét, és az indexelési házirendet a többi mellett.

Az ebben a példában azt kivételezett `deviceId` tudjuk, hogy (a) óta eszközök nagy számú, mivel a írások terjeszthető partíciók között egyenlően, és lehetővé téve, hogy a nagy mennyiségű adatot betöltési adatbázis méretezése és (b) számos a kérések, például egy eszköz a legújabb olvasási beolvasása egyetlen deviceId hatóköre, és egyetlen partícióra lekérhetők.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Ez a módszer lehetővé teszi a REST API hívása Cosmos DB, és a szolgáltatás kiépíti a kért átviteli sebesség alapján létrehozott partícióknak számos. A teljesítmény kell fejlődnek tudja módosítani az átviteli sebesség a tároló. 

### <a name="reading-and-writing-items"></a>Olvasott és írt elemek
Most tegyük beszúrni Cosmos DB adatokat. Íme egy minta osztály, amely tartalmazza egy eszközt, olvasása, és egy tárolóba olvasása új eszköz beszúrása Documentclient hívásakor. Ez az az SQL API-t, ami például:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Most olvassa el a cikk a partíciókulcs és azonosító, a frissítést, és utolsó lépésként, törölje azt partíciókulcs és azonosítója. Vegye figyelembe, hogy az olvasások adni egy PartitionKey (a megfelelő a `x-ms-documentdb-partitionkey` kérelem fejléce a REST API-ban).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>A particionált tárolók lekérdezése
A particionált tárolókban lévő adatok lekérdezésekor Cosmos DB automatikusan továbbítja a lekérdezés a partíciókat a partíciókulcs-értékek a szűrőben megadott (ha vannak ilyenek) megfelelő. Ez a lekérdezés például csak a partíciós kulcs "XMS-0001" tartalmazó partíció van átirányítva.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
A következő lekérdezés nem rendelkezik egy szűrőt a partíciós kulcs (DeviceId), és minden olyan partíciónak, ahol hajtotta végre a partíció index alapján történő van rendezve. Vegye figyelembe, hogy meg kell adnia a EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` REST API-ja) kell rendelkeznie az SDK partíciók között a lekérdezés végrehajtásához.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Támogatja a cosmos DB [aggregátumfüggvények](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` és `AVG` over particionálva tárolók indítása az SDK-k 1.12.0 és újabb SQL használatával. Lekérdezések tartalmaznia kell egy egyetlen összesítő operátor, és egyetlen értéket kell adni a leképezésben.

### <a name="parallel-query-execution"></a>Párhuzamos lekérdezés-végrehajtás
A Cosmos DB SDK-k 1.9.0 és hajthat végre a particionált gyűjtemények, lekérdezések kis késés, még akkor is, amikor sok partíciók touch kell támogatási párhuzamos lekérdezés végrehajtási beállítások fent. A következő lekérdezés például több partíció párhuzamosan futó van konfigurálva.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
A következő paraméterek hangolása kezelheti párhuzamos lekérdezés-végrehajtás:

* Úgy, hogy `MaxDegreeOfParallelism`, például a tároló partíciók egyidejű hálózati kapcsolatok maximális száma párhuzamos fokának szabályozhatja. Ha a-1, milyen párhuzamossági az SDK kezeli. Ha a `MaxDegreeOfParallelism` nem megadott vagy kell állítani, 0, amely az alapértelmezett érték, a tároló partíciók egyetlen hálózati kapcsolattal lesz.
* Úgy, hogy `MaxBufferedItemCount`, akkor is kompromisszumot lekérdezés-késleltetés és ügyféloldali memóriahasználata. Ha kihagyja ezt a paramétert, vagy állítsa-1, párhuzamos lekérdezés-végrehajtás során pufferelt elemek száma. az SDK kezeli.

Megadott gyűjtemény olyan állapotban, a párhuzamos lekérdezések lesz az eredményeket ugyanabban a sorrendben, ahogy soros végrehajtása. Rendezés (ORDER BY és/vagy felső) tartalmazó kereszt-partíció lekérdezés végrehajtásakor a az Azure Cosmos DB SDK állít ki a párhuzamos lekérdezés partíciók között, és egyesíti globálisan rendezett eredmények eredményezett ügyféloldali részben rendezett eredményez.

### <a name="executing-stored-procedures"></a>Tárolt eljárások végrehajtása
Ilyen azonosítójú eszköz,-dokumentumokon végzett atomi tranzakciók is futtathat, például ha összesítések vagy csak egy elemet az eszközök aktuális állapotát most karbantartása. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
A következő szakaszban úgy tekintünk, hogyan viheti át a particionált tárolók egypartíciós tárolókból.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben azt használata Azure Cosmos DB tároló SQL API-val particionálás áttekintését biztosítja. Lásd még: [particionálás és horizontális skálázás](../cosmos-db/partition-data.md) fogalmakat és ajánlott eljárások az Azure Cosmos DB API-k a particionálás áttekintését. 

* Hajtsa végre a méretezés és teljesítmény Azure Cosmos DB tesztelték. Lásd: [teljesítmény- és Mérettesztelés az Azure Cosmos DB](performance-testing.md) egy minta.
* Ismerkedés a kódolási a [SDK-k](documentdb-sdk-dotnet.md) vagy a [REST API-n](/rest/api/documentdb/)
* További tudnivalók [Azure Cosmos DB a létesített átviteli sebesség](request-units.md)

