---
title: Térinformatikai adatbázis használata Azure Cosmos DB SQL API-fiókban
description: Megtudhatja, hogyan hozhat létre, indexelheti és lekérdezheti a térbeli objektumokat Azure Cosmos DB és az SQL API-val.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: e48f6c52aa2d633ea20fd0dae70c7aa1380bb50d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441884"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Térinformatikai és GeoJSON-helyadatok használata Azure Cosmos DB SQL API-fiókkal

Ez a cikk a Azure Cosmos DB térinformatikai funkciójának bevezetését ismerteti. Jelenleg csak Cosmos DB SQL API-fiókok támogatják a térinformatikai adatok tárolását és elérését. A cikk elolvasása után a következő kérdésekre tud válaszolni:

* Hogyan a térbeli adatainak tárolása Azure Cosmos DB-ben?
* Hogyan lehet lekérdezni a térinformatikai adatAzure Cosmos DB az SQL és a LINQ szolgáltatásban?
* Hogyan engedélyezheti vagy letilthatja a térbeli indexelést a Azure Cosmos DBban?

Ez a cikk bemutatja, hogyan használható a térbeli adatmennyiség az SQL API-val. Tekintse meg a [GitHub-projektet](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) a kód mintáinak megtekintéséhez.

## <a name="introduction-to-spatial-data"></a>A térbeli adatainak bemutatása
A térbeli adatmennyiségek a térben lévő objektumok helyzetét és alakját ismertetik. A legtöbb alkalmazásban ezek a föld és a térinformatikai adat objektumainak felelnek meg. A térbeli adat használható egy személy, egy hely vagy egy város határának, illetve egy-tó helyének a jelölésére. Gyakori használati esetek gyakran tartalmaznak közelségi lekérdezéseket, például "az aktuális hely közelében található összes kávézó megkeresése". 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB támogatja a [GeoJSON-specifikáció](https://tools.ietf.org/html/rfc7946)alapján megjelenített térinformatikai pont adatok indexelését és lekérdezését. A GeoJSON-adatstruktúrák mindig érvényes JSON-objektumok, így a Azure Cosmos DB a speciális eszközök vagy könyvtárak nélkül tárolhatók és lehívhatók. A Azure Cosmos DB SDK-k olyan segítő osztályokat és metódusokat biztosítanak, amelyek megkönnyítik a térbeli információkkal való munkavégzést. 

### <a name="points-linestrings-and-polygons"></a>Pontok, Linestring és sokszögek
Egy **pont** egyetlen helyet jelöl a térben. A térinformatikai adatokat tartalmazó pont a pontos helyet jelöli, amely egy élelmiszerbolt, egy kioszk, egy autó vagy egy város utcanév-címe lehet.  A pontok a GeoJSON (és Azure Cosmos DB) jelennek meg a koordináta-pár vagy a hosszúság és a földrajzi szélesség használatával. Íme egy példa JSON egy ponthoz.

**Azure Cosmos DBi pontok**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A GeoJSON-specifikáció meghatározza az első és a szélességi hosszúságot. A többi leképezési alkalmazáshoz hasonlóan a hosszúsági és a szélességi fok is szögben jelenik meg. A hosszúsági értékeket a Prime Meridian-ből mérjük, és 180 fok és 180,0 fok között kell mérni, a szélességi értékeket pedig az-90,0 fok és a 90,0 fok közé kell mérni. 
> 
> Azure Cosmos DB értelmezi a WGS-84 hivatkozási rendszeren ábrázolt koordinátákat. A koordináta-hivatkozási rendszerek részletes ismertetését alább találja.
> 
> 

Ez egy Azure Cosmos DB-dokumentumba ágyazható be, ahogy az a következő példában látható: helyadatok tartalmazó felhasználói profil:

**Azure Cosmos DBban tárolt hellyel rendelkező profil használata**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

A pontok mellett a GeoJSON a Linestring és a sokszögeket is támogatja. A **linestring** két vagy több pontból álló sorozatot jelöl, és az azokat összekapcsolási szegmenseket. A térinformatikai Linestring általában az autópályák és a folyók ábrázolására használják. A **sokszög** a csatlakoztatott pontok határa, amelyek egy lezárt LineString alkotnak. A sokszögeket általában olyan természetes formák ábrázolására használják, mint a tavak vagy a politikai illetékességek, például a városok és az Államok. Íme egy példa egy sokszögre Azure Cosmos DB. 

**Sokszögek a GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A GeoJSON-specifikáció megköveteli, hogy érvényes sokszögek esetén a megadott koordináta-pároknak meg kell egyezniük az elsővel, hogy egy bezárt alakzatot hozzon létre.
> 
> A sokszögben lévő pontokat a megfelelő sorrendben kell megadni. A megegyező sorrendben megadott sokszög a régió inverzét jelöli.
> 
> 

A Point, a LineString és a sokszög mellett a GeoJSON azt is meghatározza, hogyan kell csoportosítani a több térinformatikai helyet, valamint azt, hogy hogyan társítható tetszőleges tulajdonságok a térinformatikai **szolgáltatáshoz**. Mivel ezek az objektumok érvényesek a JSON-ra, a Azure Cosmos DB tárolhatók és feldolgozhatók. A Azure Cosmos DB azonban csak a pontok automatikus indexelését támogatja.

### <a name="coordinate-reference-systems"></a>Koordináta-rendszerek
Mivel a föld alakja szabálytalan, a térinformatikai adathalmazok számos koordináta-hivatkozási rendszerben (CRS) jelennek meg, amelyek mindegyike saját hivatkozási kerettel és mértékegységgel rendelkezik. Például a "Britain országos hálózata" egy hivatkozási rendszer, amely az Egyesült Királyság számára pontos, de nem azon kívül. 

A jelenleg használatban lévő legnépszerűbb CRS a [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)globális geodéziai rendszer. A GPS-eszközök és számos leképezési szolgáltatás, például a Google Maps és a Bing Maps API-k a WGS-84-et használják. A Azure Cosmos DB csak a WGS-84 CRS használatával támogatja a térinformatikai adatai indexelését és lekérdezését. 

## <a name="creating-documents-with-spatial-data"></a>Dokumentumok létrehozása térbeli adattal
Ha GeoJSON-értékeket tartalmazó dokumentumokat hoz létre, azok automatikusan egy térbeli indextel lesznek indexelve a tároló indexelési házirendjének megfelelően. Ha egy Azure Cosmos DB SDK-val dolgozik egy dinamikusan gépelt nyelven (például Python vagy Node. js), akkor érvényes GeoJSON kell létrehoznia.

**Dokumentum létrehozása térinformatikai adattal a Node. js-ben**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Ha az SQL API-kkal dolgozik, a `Microsoft.Azure.Documents.Spatial` névtérben lévő `Point` és `Polygon` osztályok segítségével ágyazhatja be a hely adatait az alkalmazásobjektumok között. Ezek az osztályok segítenek leegyszerűsíteni a térbeli adatainak szerializálását és deszerializálását a GeoJSON.

**Dokumentum létrehozása térinformatikai adattal a .NET-ben**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Ha nem rendelkezik a szélességi és a hosszúsági adatokkal, de a fizikai címeket vagy a hely nevét, például a várost vagy az országot/régiót, megkeresheti a tényleges koordinátákat egy olyan helymeghatározáshoz-szolgáltatás használatával, mint a Bing Maps REST Services. További információ a Bing Maps helymeghatározáshoz [itt](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Térbeli típusok lekérdezése
Most, hogy áttekintette a térinformatikai adatbevitelt, vessünk egy pillantást arra, hogyan lehet lekérdezni ezeket az adatAzure Cosmos DBt az SQL és a LINQ használatával.

### <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített függvények
A Azure Cosmos DB a következő Nyílt térinformatikai konzorcium (OGC) beépített függvényeket támogatja a térinformatikai lekérdezésekhez. Az SQL nyelv beépített funkcióinak teljes készletével kapcsolatos további információkért lásd: [lekérdezés Azure Cosmos db](how-to-sql-query.md).

|**Használat**|**Leírás**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | A két GeoJSON pont, a sokszög vagy a LineString kifejezés közötti távolságot adja vissza.|
|ST_WITHIN (spatial_expr, spatial_expr) | Egy logikai kifejezést ad vissza, amely azt jelzi, hogy az első GeoJSON objektum (pont, sokszög vagy LineString) a második GeoJSON objektumon (pont, sokszög vagy LineString) belül van-e.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Egy logikai kifejezést ad vissza, amely azt jelzi, hogy a két megadott GeoJSON-objektum (pont, sokszög vagy LineString) metszve van-e.|
|ST_ISVALID| Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes-e.|
| ST_ISVALIDDETAILED| Egy logikai értéket tartalmazó JSON-értéket ad vissza, ha a megadott GeoJSON pont, sokszög vagy LineString kifejezés érvényes, és ha az érték érvénytelen, akkor a karakterlánc értékét is adja meg.|

A térbeli függvények használatával közelségi lekérdezéseket végezhet a térbeli adatokon. Például egy olyan lekérdezés, amely az összes olyan családi dokumentumot adja vissza, amely a megadott hely 30 km-n belül található a ST_DISTANCE beépített függvény használatával. 

**Lekérdezés**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Ha a térbeli indexelést az indexelési házirend tartalmazza, akkor a "távolsági lekérdezések" hatékonyan lesznek kézbesítve az indexen keresztül. A térbeli indexeléssel kapcsolatos további információkért tekintse meg az alábbi szakaszt. Ha nem rendelkezik térbeli indexszel a megadott elérési utakhoz, a térbeli lekérdezéseket továbbra is végrehajthatja `x-ms-documentdb-query-enable-scan` kérelem fejlécének megadásával, amelynek értéke "true" (igaz) értékre van állítva. A .NET-ben ezt úgy teheti meg, hogy a választható **FeedOptions** argumentumot átadja a [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) beállítással az igaz értékre. 

A ST_WITHIN használatával ellenőrizhető, hogy egy pont egy Sokszögen belül található-e. A gyakran használt sokszögek olyan határokat jelölnek, mint például a zip-kódok, az állami határok vagy a természetes képződmények. Ha a térbeli indexelést is tartalmazza az indexelési házirendben, a "belül" lekérdezéseket a rendszer hatékonyan kézbesíti az indexen keresztül. 

A ST_WITHINban található sokszög argumentumok csak egyetlen gyűrűt tartalmazhatnak, azaz a sokszögek nem tartalmazhatnak lyukakat bennük. 

**Lekérdezés**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Results**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Hasonlóan ahhoz, ahogyan a nem egyező típusok működnek Azure Cosmos DB lekérdezésekben, ha az argumentumban megadott Helykód helytelen formátumú vagy érvénytelen, akkor a rendszer nem **definiált** értékre értékeli, és a kiértékelt dokumentumot kihagyja a lekérdezés eredményeiből. Ha a lekérdezés nem ad vissza találatot, a ST_ISVALIDDETAILED futtatásával ellenőrizze, hogy a térbeli típus érvénytelen-e.     
> 
> 

A Azure Cosmos DB támogatja az inverz lekérdezések végrehajtását is, azaz a sokszögeket vagy vonalakat indexelheti Azure Cosmos DB, majd lekérdezheti a megadott pontot tartalmazó területeket. Ezt a mintát gyakran használják a logisztikában annak azonosítására, hogy például egy teherautó belép vagy kilép egy kijelölt régióból. 

**Lekérdezés**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Results**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizhető, hogy a térbeli objektum érvényes-e. Például a következő lekérdezés ellenőrzi egy pont érvényességét egy tartományon kívüli szélességi értékkel (-132,8). ST_ISVALID csak egy logikai értéket ad vissza, és a ST_ISVALIDDETAILED a logikai azonosítót és egy olyan karakterláncot ad vissza, amely az érvénytelennek minősülő okot adja meg.

**Lekérdezés**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Ezek a függvények a sokszögek ellenőrzéséhez is használhatók. Például itt használjuk a ST_ISVALIDDETAILEDt egy nem lezárt sokszög ellenőrzéséhez. 

**Lekérdezés**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Results**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ-lekérdezés a .NET SDK-ban
Az SQL .NET SDK emellett a következő, `Distance()` és `Within()` a LINQ-kifejezéseken belüli használatra szolgáló helyettes metódusokat is tartalmazza. Az SQL LINQ-szolgáltató lefordítja ezt a metódust a megfelelő SQL beépített függvények hívására (ST_DISTANCE és ST_WITHIN, illetve). 

Íme egy példa egy LINQ-lekérdezésre, amely megkeresi az Azure Cosmos-tárolóban található összes olyan dokumentumot, amelynek "helye" értéke a megadott pont 30 km-es sugarán belül van a LINQ használatával.

**LINQ-lekérdezés távolsághoz**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Hasonlóképpen, az alábbi lekérdezéssel megtalálhatja az összes olyan dokumentumot, amelynek "helye" a megadott dobozban/sokszögben található. 

**LINQ-lekérdezés a következőn belül:**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Most, hogy megvizsgáljuk, hogyan lehet dokumentumokat lekérdezni a LINQ és az SQL használatával, vessünk egy pillantást a térbeli indexelés Azure Cosmos DB konfigurálására.

## <a name="indexing"></a>Indexelés
Ahogy azt a [séma agnosztikus indexelésének Azure Cosmos db](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papírt is ismertetjük, úgy terveztük, hogy a Azure Cosmos db adatbázis-motorja valóban séma-független legyen, és első osztályú támogatást biztosít a JSON-hoz. A Azure Cosmos DB írásra optimalizált adatbázismotor natív módon megérti a GeoJSON standardban ábrázolt térbeli adatok (pontok, sokszögek és vonalak) ismereteit.

Dióhéjban a geometria a geodéziai Koordinátákból egy 2D-síkra van kialakítva, majd fokozatosan, egy **quadtree**használatával osztva a cellákba. Ezek a cellák az 1D-re vannak leképezve a cella helye alapján a Hilbert belül, amely megőrzi a pontok helyi **kitöltését**. Emellett, ha a helyadatok indexelve lettek, egy **mozaikként**ismert folyamaton halad át, azaz az adott helyet keresztező összes cella azonosítható és kulcsként tárolódik a Azure Cosmos db indexben. A lekérdezés időpontjában az argumentumok, például a pontok és a sokszögek a megfelelő cella-azonosító tartományok kinyerésére szolgálnak, majd az adatok az indexből való lekéréséhez használatosak.

Ha olyan indexelési házirendet ad meg, amely tartalmazza a/* (minden elérési út) térbeli indexét, akkor a gyűjteményben található összes pont indexelve lesz a hatékony térbeli lekérdezésekhez (ST_WITHIN és ST_DISTANCE). A térbeli indexek nem rendelkeznek precíziós értékkel, és mindig az alapértelmezett pontossági értéket használják.

> [!NOTE]
> Azure Cosmos DB támogatja a pontok, sokszögek és Linestring automatikus indexelését
> 
> 

A következő JSON-kódrészlet egy indexelési házirendet mutat be, amelyen engedélyezve van a térbeli indexelés, azaz indexelhető minden GeoJSON pont a dokumentumok között a térbeli lekérdezésekhez. Ha az indexelési házirendet a Azure Portal használatával módosítja, megadhatja a következő JSON-t az indexelési házirend számára, hogy engedélyezze a térbeli indexelést a gyűjteményen.

**A gyűjtemény indexeli a JSON-t a pontok és a sokszögek térbeli beállításával**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Itt található egy kódrészlet a .NET-ben, amely bemutatja, hogyan hozhat létre térbeli indexeléssel rendelkező gyűjteményt a pontokat tartalmazó összes elérési útra. 

**Gyűjtemény létrehozása térbeli indexeléssel**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

A következőképpen módosíthatja egy meglévő gyűjteményt, hogy kihasználhassa a térbeli indexelés előnyeit a dokumentumokban tárolt bármely pontnál.

**Meglévő gyűjtemény módosítása térbeli indexeléssel**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Ha a hely GeoJSON értéke helytelen formátumú vagy érvénytelen, akkor nem lesz indexelve a térbeli lekérdezésekhez. A hely értékeit ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizheti.
> 
> Ha a gyűjtemény definíciója tartalmaz egy partíciós kulcsot, az indexelés átalakításának folyamata nem jelent meg. 
> 
> 

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta, hogyan kezdheti meg a térinformatikai támogatás használatát Azure Cosmos DBban, a következő lehetőségekkel:

* A kódolás megkezdése a [térinformatikai .net-kód mintáinak használatával a githubon](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* A Azure Cosmos DB a térinformatikai lekérdezésekkel kapcsolatban [lekérdezési demókörnyezet](https://www.documentdb.com/sql/demo#geospatial)
* További információ a [Azure Cosmos db lekérdezésről](how-to-sql-query.md)
* További információ az [Azure Cosmos db indexelési házirendekről](index-policy.md)

