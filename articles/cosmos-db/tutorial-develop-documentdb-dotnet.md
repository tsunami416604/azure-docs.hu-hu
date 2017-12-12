---
title: "Az Azure Cosmos DB: Az SQL API-t a .NET fejlesztés |} Microsoft Docs"
description: "Ismerje meg, hogyan fejleszthet Azure Cosmos DB SQL API-t a .NET használatával"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: f019ae72e380f1c8e5b1ec67bd5177144ce1b345
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmosdb-develop-with-the-sql-api-in-net"></a>Az Azure CosmosDB: Az SQL API-t a .NET fejlesztés

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy Azure Cosmos DB fiókot az Azure portál használatával, és majd a dokumentum-adatbázis és gyűjtemény létrehozása egy [partíciókulcs](documentdb-partition-data.md#partition-keys) használatával a [SQL .NET API](documentdb-introduction.md). Hozzon létre egy partíciókulcsot, ha létrehoz egy gyűjteményt, amelyet az alkalmazás kész az adatok növekedésével egyszerűen lehet méretezni. 

Ez az oktatóanyag ismerteti a következő feladatok segítségével a [SQL .NET API](documentdb-sdk-dotnet.md):

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása
> * Egy adatbázis és gyűjtemény létrehozása egy partíciós kulccsal
> * JSON-dokumentumok létrehozása
> * Dokumentum frissítése
> * A particionált gyűjtemények lekérdezése
> * A tárolt eljárások futtatása
> * Dokumentum törlése
> * Adatbázis törlése

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Először hozzon létre egy Azure Cosmos DB fiókot az Azure portálon.

> [!TIP]
> * Már van Azure Cosmos DB fiókja? Ha igen, ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS)
> * Ha az Azure Cosmos DB Emulator használ, adja kövesse a [Azure Cosmos DB emulátor](local-emulator.md) kell beállítania az emulátor, és ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az a **új projekt** párbeszédablakban válassza **sablonok** / **Visual C#** / **Konzolalkalmazás (.NET-keretrendszer)**, nevezze el a projektet, és kattintson a **OK**.
   ![Képernyőfelvétel az Új projekt ablakról](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.
    
    ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Az a **NuGet** lapra, majd **Tallózás**, és írja be **documentdb** be a keresőmezőbe.
<!---stopped here--->
6. A találatok között keresse meg a **Microsoft.Azure.DocumentDB** elemet, majd kattintson a **Telepítés** lehetőségre.
   A csomag-azonosító az Azure Cosmos DB ügyféloldali kódtár [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Képernyőfelvétel a NuGet menüről a Azure Cosmos DB ügyfél SDK-kereséshez](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

## <a id="Connect"></a>Vegye fel a hivatkozásokat a projekthez
Ebben az oktatóanyagban a további lépéseket adja meg az SQL API kódrészletek, létrehozása és frissítése a projekt Azure Cosmos DB erőforrásainak szükséges.

Először adja hozzá ezeket a hivatkozásokat, hogy az alkalmazást.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Az alkalmazás kapcsolódni

Ezután adja hozzá ezt a két állandót és a *ügyfél* változó az alkalmazás.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Majd, head vissza a [Azure-portálon](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beolvasása. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, az Azure Cosmos DB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure portálon lépjen az Azure Cosmos DB fiókjába, kattintson **kulcsok**, és kattintson a **írható-olvasható kulcsok**.

Az URI a portálról másolása és beillesztése azt `<your endpoint URL>` a program.cs fájlban található. Az elsődleges kulcs másolása a portálról, majd illessze be keresztül `<your primary key>`. Ügyeljen arra, hogy távolítsa el a `<` és `>` a értékeiből.

![Képernyőfelvétel a NoSQL-oktatóanyagban a C# Konzolalkalmazás létrehozásához használt Azure-portálon. A Azure Cosmos DB-fiók panelen lévő kulcsok és a kulcsok panelen lévő URI és PRIMARY KEY értékek Azure Cosmos DB fiókkal tartalmazza](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>A documentclient ügyfél segítségével hozható létre

Most, hozzon létre egy új példányt a **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Adatbázis létrehozása

Ezután hozzon létre egy Azure Cosmos DB [adatbázis](documentdb-resources.md#databases) használatával a [Documentclient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metódus vagy [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusában a  **DocumentClient** osztályával a [SQL .NET SDK](documentdb-sdk-dotnet.md). Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Adja meg a partíciós kulcs 

Gyűjtemények tárolói dokumentumok tárolására. Ezek a logikai erőforrások és is [egy vagy több fizikai partíció span](partition-data.md). A [partíciókulcs](documentdb-partition-data.md) a tulajdonság (vagy elérési út) az adatok között a kiszolgálók vagy a partíciók terjesztéséhez használt dokumentumok belül van. Az azonos partíciókulcsú összes dokumentumot partícióra vannak tárolva. 

A partíciós kulcs egy fontos döntés előtt hozzon létre egy gyűjteményt. Partíció kulcsai a tulajdonság (vagy elérési út) az adatok több kiszolgálók között vagy partíciók terjeszteni használható Azure Cosmos DB dokumentumok belül. Cosmos DB csak a partíciós kulcs értékét és a kivonatolt eredménye alapján határozza meg, amely tárolja a dokumentum a partíció. Az azonos partíciókulcsú összes dokumentumot partícióra vannak tárolva, és partíciókulcsok gyűjtemény létrehozása után nem módosítható. 

Ebben az oktatóanyagban a partíciós kulcs beállítva fogjuk `/deviceId` , hogy az összes adat egyetlen eszközhöz egyetlen partícióra van tárolva. Válassza ki, hogy az értékek, amelyek segítségével a azonos gyakoriságát Cosmos DB tudja a terhelést, ha az adatok növekszik, és az adatgyűjtés a teljes teljesítmény elérése érdekében nagy számú partíciós kulcs szeretné. 

Partícionálásra vonatkozó további információkért lásd: [partíció és a skála Azure Cosmos DB?](partition-data.md) 

## <a id="CreateColl"></a>Gyűjtemény létrehozása 

Most, hogy tudjuk, hogy a partíciós kulcs `/deviceId`, lehetővé teszi, hogy hozzon létre egy [gyűjtemény](documentdb-resources.md#collections) használatával a [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metódus vagy [CreateDocumentCollectionIfNotExistsAsync ](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metódusában a **DocumentClient** osztály. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló. 

> [!WARNING]
> Gyűjtemény létrehozása megegyezik árképzési hatással vannak, az alkalmazás Azure Cosmos DB kommunikálni átviteli lefoglalja. További részletekért tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/)
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

Ez a módszer egy REST API hívása Azure Cosmos DB, és a szolgáltatás a kért átviteli sebesség alapján létrehozott partícióknak számos teszi. Módosíthatja a gyűjtemény átviteli azt a teljesítményt kell fejleszteni a SDK használatával vagy a [Azure-portálon](set-throughput.md).

## <a id="CreateDoc"></a>JSON-dokumentumok létrehozása
Az Azure Cosmos DB szúrjon be néhány JSON-dokumentumokat. A [dokumentumok](documentdb-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Ez a minta az osztály egy eszköz olvasása és szúrható be egy új eszközt egy gyűjteményhez olvasása Documentclient hívásakor tartalmaz.

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

Most, olvassa el a dokumentum a partíciós kulcs és a ReadDocumentAsync metódussal azonosítója. Vegye figyelembe, hogy az olvasások adni egy PartitionKey (a megfelelő a `x-ms-documentdb-partitionkey` kérelem fejléce a REST API-ban).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Adatok frissítése

Most tegyük frissítése néhány adat, a ReplaceDocumentAsync metódussal.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Adat törlése

Most már lehetővé teszi, hogy a dokumentum által partíciókulcs és azonosító törlése DeleteDocumentAsync módszer használatával.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>A particionált gyűjtemények lekérdezése

Amikor adatait a particionált gyűjtemények, Azure Cosmos DB automatikusan továbbítja a lekérdezés a partíciókat a partíciókulcs-értékek a szűrőben megadott (ha vannak ilyenek) megfelelő. Ez a lekérdezés például csak a partíciós kulcs "XMS-0001" tartalmazó partíció van átirányítva.

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

## <a name="parallel-query-execution"></a>Párhuzamos lekérdezés-végrehajtás
Az Azure Cosmos DB SQL SDK-k 1.9.0 és hajthat végre a particionált gyűjtemények, lekérdezések kis késés, még akkor is, amikor sok partíciók touch kell támogatási párhuzamos lekérdezés végrehajtási beállítások fent. A következő lekérdezés például több partíció párhuzamosan futó van konfigurálva.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
A következő paraméterek hangolása kezelheti párhuzamos lekérdezés-végrehajtás:

* Úgy, hogy `MaxDegreeOfParallelism`, szabályozhatja, azaz a gyűjtemény partíciókra egyidejű hálózati kapcsolatok maximális száma párhuzamos mértékét. Ha a-1, milyen párhuzamossági az SDK kezeli. Ha a `MaxDegreeOfParallelism` nem megadott vagy kell állítani, 0, amely az alapértelmezett érték, a gyűjtemény partíciók egyetlen hálózati kapcsolattal lesz.
* Úgy, hogy `MaxBufferedItemCount`, akkor is kompromisszumot lekérdezés-késleltetés és ügyféloldali memóriahasználata. Ha kihagyja ezt a paramétert, vagy állítsa-1, párhuzamos lekérdezés-végrehajtás során pufferelt elemek száma. az SDK kezeli.

Megadott gyűjtemény olyan állapotban, a párhuzamos lekérdezések lesz az eredményeket ugyanabban a sorrendben, ahogy soros végrehajtása. Rendezés (ORDER BY és/vagy felső) tartalmazó kereszt-partíció lekérdezés végrehajtásakor a az SQL-SDK állít ki a párhuzamos lekérdezés partíciók között, és egyesíti globálisan rendezett eredmények eredményezett ügyféloldali részben rendezett eredményez.

## <a name="execute-stored-procedures"></a>Tárolt eljárások végrehajtása
Végül végrehajthat atomi tranzakciókról dokumentumok ilyen azonosítójú eszköz, például ha a következő kódot ad hozzá a projekthez még karbantartása összesítések vagy egy eszközt az egyetlen dokumentum aktuális állapotát.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

És ennyi az egész! most már a hatékony méretezést adatok terjesztési közötti partíciók partíciókulcsot használó Azure Cosmos DB alkalmazás fő összetevőit.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Hogy továbbra is használhatja az alkalmazás nem lesz, ha törli a következő lépések az Azure portálon oktatóprogram során létrehozott összes erőforrás:

1. Az Azure-portálon a bal oldali menüben kattintson a **erőforráscsoportok** és kattintson a létrehozott erőforrás egyedi nevét. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők: 

> [!div class="checklist"]
> * Egy Azure Cosmos DB-fiók létrehozása
> * Egy adatbázis és gyűjtemény létrehozása egy partíciós kulccsal
> * Létrehozott JSON-dokumentumok
> * A dokumentumok frissítése
> * A particionált gyűjtemények lekérdezése
> * A tárolt eljárás futott
> * A dokumentum törlése
> * Adatbázis törlése

Ezután folytassa a következő oktatóanyag és további adatok importálása a Cosmos DB fiókját. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
