---
title: 'Azure Cosmos DB: Fejlesztés az SQL API-val .NET-keretrendszerben | Microsoft Docs'
description: Arra vonatkozó ismeretek, hogyan lehet fejlesztési műveleteket végrehajtani az Azure Cosmos DB SQL API-jával a .NET-keretrendszerben
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: a6ed74de159593003e8a18daefce2eb9a5945481
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB: Fejlesztés az SQL API-val .NET-keretrendszerben

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre az Azure Portalon egy Azure Cosmos DB-fiókot, majd [partíciókulccsal](sql-api-partition-data.md#partition-keys) rendelkező dokumentum-adatbázist és gyűjteményt az [SQL .NET API-val](sql-api-introduction.md). Ha a gyűjtemény létrehozásakor meghatározza a partíciókulcsot, azzal előkészíti az alkalmazást arra, hogy az adatok mennyiségének növekedésével zökkenőmentesen átméretezhető legyen.

Ez az oktatóanyag a következő, az [SQL .NET API](sql-api-sdk-dotnet.md)-val elvégezhető feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása
> * Partíciókulccsal rendelkező adatbázis és gyűjtemény létrehozása
> * JSON-dokumentumok létrehozása
> * Dokumentum frissítése
> * Particionált gyűjtemények lekérdezése
> * Tárolt eljárások futtatása
> * Dokumentum törlése
> * Adatbázis törlése

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Hozzáférés egy Azure Cosmos DB-fiókhoz

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  Használhat saját Azure-előfizetést is egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/) regisztrálásával. Ezután [létrehozhat egy Azure Cosmos DB-fiókot](create-sql-api-dotnet.md#create-a-database-account).

* Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.


> [!TIP]
> * Ha az Azure Cosmos DB Emulator használata mellett dönt, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor telepítéséhez.
>
>


## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az **Új projekt** párbeszédpanelen válassza a **Sablonok** / **Visual C#** / **Konzolalkalmazás (.NET-keretrendszer)** elemet, nevezze el a projektet, majd kattintson az **OK** gombra.
   ![Képernyőfelvétel az Új projekt ablakról](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.

    ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. A **NuGet** lapon kattintson a **Tallózás** elemre, majd írja be a **documentdb** kifejezést a keresőmezőbe.
<!---stopped here--->
6. A találatok között keresse meg a **Microsoft.Azure.DocumentDB** elemet, majd kattintson a **Telepítés** lehetőségre.
   Az Azure Cosmos DB ügyfélkódtárának csomagazonosítója a következő: [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Képernyőkép a NuGet menüről az Azure Cosmos DB ügyféloldali SDK megkereséséhez](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

## <a id="Connect"></a>Hivatkozások hozzáadása a projekthez
Az oktatóanyag további lépéseiben a projekt Azure Cosmos DB-erőforrásainak létrehozásához és frissítéséhez szükséges SQL API-kódrészletek szerepelnek.

Először adja hozzá az alábbi hivatkozásokat az alkalmazáshoz.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Az alkalmazás csatlakoztatása

Következő lépésként adja hozzá ezt a két állandót és a *client* (ügyfél) változót az alkalmazáshoz.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beszerzéséért. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure Portalon lépjen az Azure Cosmos DB-fiókra. A bal oldali menüben válassza a **Kulcsok**, majd az **Írható és olvasható kulcsok** lehetőséget.

Másolja ki az URI-t a portálról, és illessze be a `<your endpoint URL>` helyére a program.cs fájlban. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your primary key>` helyére. Az értékekből ne feledje eltávolítani a `<` és `>` karaktereket.

![Képernyőkép a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure Portalról. Megjelenít egy Azure Cosmos DB-fiókot, amelyen az Azure Cosmos DB-fiók szakaszban lévő KEYS elem, valamint a Kulcsok szakaszban lévő URI és PRIMARY KEY értékek vannak kiemelve.](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>A DocumentClient példányának létrehozása

Most hozza létre a **DocumentClient** egy új példányát.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Adatbázis létrehozása

Következő lépésként hozzon létre egy Azure Cosmos DB-[adatbázist](sql-api-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) vagy [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusának használatával az [SQL .NET SDK](sql-api-sdk-dotnet.md)-ból. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Partíciókulcs választása

A gyűjtemények dokumentumok tárolására alkalmas tárolók. Olyan logikai erőforrások, amelyek [több fizikai partícióra is kiterjedhetnek](partition-data.md). A [partíciókulcs](sql-api-partition-data.md) olyan tulajdonság (vagy útvonal) a dokumentumokon belül, amelynek használatával az adatok eloszthatók a kiszolgálók vagy partíciók között. Az ugyanazzal a partíciókulccsal rendelkező összes dokumentum ugyanazon a partíción tárolódik.

A partíciókulcs meghatározása fontos döntés a gyűjtemény létrehozása előtt. A partíciókulcsok olyan tulajdonságok (vagy útvonalak) a dokumentumokon belül, amelyeket az Azure Cosmos DB az adatok több kiszolgáló vagy partíció közötti elosztására használ. A Cosmos DB kivonatolja a partíciókulcs értékét, és a kivonatolt eredmény alapján határozza meg, hogy melyik partíción tárolja a dokumentumot. Az ugyanazon partíciókulccsal rendelkező összes dokumentum ugyanazon a partíción tárolódik, és a gyűjtemény létrehozását követően a partíciókulcsok nem módosíthatók.

Ebben az oktatóanyagban a partíciókulcsot `/deviceId` értékre kell állítania. Így a rendszer egy adott eszköz minden adatát ugyanazon a partíción tárolja. Olyan partíciókulcsot érdemes választani, amely sok, hasonló gyakorisággal használt értékkel rendelkezik, mert így gondoskodni lehet róla, hogy a Cosmos DB az adatok mennyiségének növekedése közben elvégezze a terheléselosztást, és a gyűjtemény teljes kapacitását ki lehessen aknázni.

A particionálással kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partition-data.md) ismertető cikkben talál.

## <a id="CreateColl"></a>Gyűjtemény létrehozása

A partíciókulccsal (`/deviceId`) létrehozhat egy [gyűjteményt](sql-api-resources.md#collections) a **DocumentClient** osztály [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) vagy [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metódusával. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikákat tartalmazó tároló.

> [!WARNING]
> A gyűjtemények létrehozása a díjszabásra is hatással van, mivel átviteli sebességet tart fenn az alkalmazás számára az Azure Cosmos DB-vel folytatott kommunikációhoz. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Ez a metódus egy REST API-hívást indít az Azure Cosmos DB-hez, és a szolgáltatás a kért átviteli sebesség alapján megfelelő számú partíciót biztosít. A gyűjtemények átviteli sebességét a teljesítményre vonatkozó igények változása alapján az SDK használatával vagy az [Azure Portalon](set-throughput.md) módosíthatja.

## <a id="CreateDoc"></a>JSON-dokumentumok létrehozása
Illesszünk be néhány JSON-dokumentumot az Azure Cosmos DB-be. A [dokumentumok](sql-api-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Ez a mintaosztály egy eszközleolvasást tartalmaz, valamint a CreateDocumentAsync hívását, amellyel új eszközleolvasást illeszthet be a gyűjteménybe.

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

// Create a document. Here the partition key is extracted
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Adatok olvasása

Olvassa be a dokumentumot a partíciókulcsa és az azonosítója alapján a ReadDocumentAsync metódussal. Vegye figyelembe, hogy a beolvasások egy PartitionKey értéket is tartalmaznak (a REST API `x-ms-documentdb-partitionkey` kérelemfejlécének megfelelően).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Adatok frissítése

Most frissítsen néhány adatot a ReplaceDocumentAsync metódussal.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>Adat törlése

Töröljön egy dokumentumot a partíciókulcsa és azonosítója alapján a DeleteDocumentAsync metódussal.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Particionált gyűjtemények lekérdezése

A particionált gyűjtemények adatainak lekérdezésekor az Azure Cosmos DB automatikusan átirányítja a lekérdezést a szűrőben meghatározott partíciókulcs-értékeknek megfelelő partíciókhoz (ha vannak ilyenek). Ezt a lekérdezést például az „XMS-0001” partíciókulcsot tartalmazó partícióhoz irányítja.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

A következő lekérdezés nem rendelkezik a partíciókulcsra (DeviceId) vonatkozó szűrővel, és minden partícióra kiterjed, ahol a partíció indexén lesz végrehajtva. Ne feledje, hogy meg kell adnia az EnableCrossPartitionQuery értékét (`x-ms-documentdb-query-enablecrosspartition` a REST API-ban) ahhoz, hogy az SDK egy lekérdezést az összes partíción futtasson.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Lekérdezések párhuzamos végrehajtása
Az Azure Cosmos DB SQL SDK 1.9.0-s és újabb verziói támogatják a párhuzamos lekérdezés-végrehajtási lehetőségeket, amelyekkel kis késleltetésű lekérdezéseket végezhet a particionált gyűjteményeken még akkor is, ha azoknak nagyszámú partíciót kell érinteniük. A következő lekérdezés például a partíciókon való párhuzamos futtatásra van konfigurálva.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

* A `MaxDegreeOfParallelism` érték beállításával szabályozhatja a párhuzamosság szintjét, azaz a gyűjtemény partícióihoz egyszerre létrehozott hálózati kapcsolatokat. Ha ez a paraméter -1, a párhuzamosság szintjét az SDK felügyeli. Ha a `MaxDegreeOfParallelism` érték nincs megadva, vagy az értéke 0 (ez az alapértelmezett érték), akkor csak egy hálózati kapcsolat jön létre a gyűjtemény partícióihoz.
* A `MaxBufferedItemCount` értékének beállításával kompromisszum alakítható ki a lekérdezések késése és az ügyféloldali memóriahasználat között. Ha kihagyja ezt a paramétert, vagy -1 értéket ad meg, akkor a párhuzamos lekérdezés-végrehajtás során pufferelt elemek számát az SDK felügyeli.

Ha a gyűjtemény állapota azonos, a párhuzamos lekérdezés ugyanazon sorrendben adja vissza az értékeket, mint a soros lekérdezés esetén. A rendezést (ORDER BY és/vagy TOP) is magában foglaló, több partícióra kiterjedő lekérdezések végrehajtásakor az SQL SDK párhuzamosan hajtja végre a lekérdezéseket a partíciókon, majd egyesíti a részlegesen tárolt eredményeket az ügyféloldalon, ezáltal globálisan rendezett eredményeket ad vissza.

## <a name="execute-stored-procedures"></a>Tárolt eljárások végrehajtása
Végül pedig elemi tranzakciókat is végrehajthat az azonos eszközazonosítóval rendelkező dokumentumokon, ha például összesített értékeket szeretne megtartani vagy egy eszköz legutóbbi állapotát egy dokumentumon belül. Ehhez adja hozzá az alábbi kódot a projekthez.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

Ennyi az egész! Ezek egy olyan Azure Cosmos DB-alkalmazás fő összetevői, amely partíciókulcsot használ az adatok partíciók közötti elosztásának hatékony méretezéséhez.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi lépésekkel törölheti az Azure Portalon az oktatóanyaghoz létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** elemre, majd kattintson a létrehozott erőforrás egyedi nevére.
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Azure Cosmos DB-fiókot
> * Létrehozott egy partíciókulccsal rendelkező adatbázist és gyűjteményt
> * JSON-dokumentumokat hozott létre
> * Frissített egy dokumentumot
> * Particionált gyűjteményeket kérdezett le
> * Futtatott egy tárolt eljárást
> * Törölt egy dokumentumot
> * Törölt egy adatbázist

Most továbbléphet a következő oktatóanyaghoz, és további adatokat importálhat a Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
