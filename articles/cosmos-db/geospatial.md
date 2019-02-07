---
title: Térinformatikai adatok az Azure Cosmos DB SQL API-fiók
description: Megtudhatja, hogyan hozhat létre, index és az Azure Cosmos DB és az SQL API térbeli objektum lekérdezése.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: sngun
ms.openlocfilehash: 9c6ea982d9a605696dad0c943aa6dd2ae155d6bd
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770737"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Térinformatikai és GeoJSON helyadatok használata az Azure Cosmos DB SQL API-fiók

Ez a cikk az Azure Cosmos DB a térinformatikai funkciókat bemutató. Csak a Cosmos DB SQL API-fiókok jelenleg tárolásához, és a földrajzi adatok eléréséhez támogatja. Ez a cikk elolvasása után fogja tudni a következő kérdések megválaszolásával:

* Hogyan térbeli adatok tárolása az Azure Cosmos DB?
* Hogyan lekérdezheti az Azure Cosmos DB az SQL és a LINQ térinformatikai adatok?
* Hogyan engedélyezése vagy letiltása az Azure Cosmos DB kifejezéséhez?

Ez a cikk bemutatja, hogyan használható a térbeli adatok az SQL API-val. Ez [GitHub-projekt](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) kódpéldákat.

## <a name="introduction-to-spatial-data"></a>Bevezetés a térbeli adatok
Térbeli adatok pozíció és objektumok terület alakja ismerteti. A legtöbb alkalmazásban ezek felel meg az objektumok a föld és a földrajzi adatok. Térbeli adatok segítségével egy személy, egy helyen a lényeges vagy a város vagy egy lake határait helyét jelölik. Gyakori alkalmazási helyzetek gyakran közelségi lekérdezések, például olyan, "minden kávézóban található a jelenlegi tartózkodási közel." 

### <a name="geojson"></a>A GeoJSON
Az Azure Cosmos DB támogatja az indexelést és a használatával van megjeleníthető földrajzi pont adatok lekérdezése a [GeoJSON specifikációnak](https://tools.ietf.org/html/rfc7946). GeoJSON datové struktury mindig érvényes JSON-objektumok, így azok is tárolhatók, és az Azure Cosmos DB által kibocsátott speciális eszközök és kódtárak nélkül megkérdezi. Az Azure Cosmos DB SDK segítőosztályokkal és a módszereket, amelyek megkönnyítik a térbeli adatok biztosítanak. 

### <a name="points-linestrings-and-polygons"></a>Pontok, Linestring, és poligonok
A **pont** azt jelzi, hogy a hely egyetlen pozíciója. A térinformatikai adatok egy pont, amely egy címe pékséglánc áruházbeli, a teljes képernyős, egy autó vagy város lehet pontos helyét jelöli.  A GeoJSON (és az Azure Cosmos DB) használatával a koordináta pár vagy szélességi és hosszúsági egy pont jelzi. Íme egy példa JSON pont.

**Az Azure Cosmos DB pontok**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A GeoJSON specifikációnak megadja a hosszúsági első és szélességi második. Például a többi leképezési alkalmazások, a szélességi és hosszúsági szögek és fok formájában jelennek meg. Hosszúsági értékeket mért vannak a szélességi és-180 fok és 180.0 fok közé, és szélességi értékek vannak az Egyenlítőtől mért-90.0 fok és 90.0 fok közé. 
> 
> Az Azure Cosmos DB értelmezi a koordináták a WGS-84. referenciarendszer egy határozzák meg. Koordináta referencia-rendszerekkel kapcsolatos további részletekért lásd az alábbi.
> 
> 

Ez úgy szúrhat be egy Azure Cosmos DB-dokumentumot tartalmazó adatok a felhasználói profil ebben a példában látható módon:

**Profil használata az Azure Cosmos DB tárolási helye**

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

Pontok mellett GeoJSON is támogatja a Linestring és poligonok. **Linestring** jelentenek a hely és a sor szegmenset, amely a csatlakoztathatja őket a két vagy több pontok. A térinformatikai adatok Linestring gyakran használják autópályák vagy folyókat ábrázolásához. A **sokszög** egy határ csatlakoztatott pontok zárt LineString alkotó. Sokszög Lake például természetes kialakításokat vagy politikai, mint például a város és állam bíróságainak gyakran használják. Íme egy példa az Azure Cosmos DB egy sokszög. 

**A GeoJSON sokszögek**

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
> A GeoJSON specifikációnak megköveteli, hogy érvényes sokszögek, a megadott utolsó koordináta pár legyen ugyanaz, mint az első zárt alakzatot hozhat létre.
> 
> Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.
> 
> 

Pont, LineString, és sokszög kívül GeoJSON is meghatározza a ábrázolását több földrajzi helyek csoportosítása, valamint tetszőleges tulajdonságok társítása földrajzi hely szerint egy **funkció**. Mivel ezek az objektumok érvényes JSON, azokat is az összes tárolhatók és feldolgozhatók, az Azure Cosmos DB-ben. Azonban az Azure Cosmos DB csak támogatja pontok automatikus indexelés.

### <a name="coordinate-reference-systems"></a>Koordináta referencia rendszerek
Mivel a föld alakját szabálytalan, számos rendszerben koordináta hivatkozás (CRS), amelyek mindegyike saját keretek hivatkozási és a mértékegységet koordinátáját földrajzi adatok jelennek meg. Ha például a "nemzeti rács-Britannia" hivatkozást a rendszer pontos-e az Egyesült Királyság, de nem azon kívül jelenik meg. 

A legnépszerűbb CRS használatban jelenleg a világ geodéziai rendszer szolgáltatás [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). A GPS-eszközök és a sok leképezés-szolgáltatással, például a Google térkép alkalmazásban és a Bing térképek API-t használja a WGS-84. Az Azure Cosmos DB támogatja az indexelést és a térinformatikai adatok használata csak a WGS-84 CRS lekérdezését. 

## <a name="creating-documents-with-spatial-data"></a>Térbeli adatokat tartalmazó dokumentumok létrehozása
Amikor GeoJSON értékeket tartalmazó dokumentumokat hoz létre, azok automatikusan indexelt térbeli indexszel rendelkező nevezhetnek, a tároló az indexelési házirendet. Ha egy Azure Cosmos DB SDK dinamikusan gépelt például a Python- vagy Node.js nyelven dolgozik, érvényes GeoJSON kell létrehoznia.

**Térinformatikai adatok használata a Node.js dokumentum létrehozása**

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

Az SQL API-k használata, ha a `Point` és `Polygon` belül osztályokat a `Microsoft.Azure.Documents.Spatial` névtér ágyazhat be az alkalmazás az objektumok helyadatokhoz. Ezeket az osztályokat segítségével egyszerűsítheti a szerializálási és a térbeli adatok deszerializálása GeoJSON be.

**Dokumentum létrehozása a .NET-ben a földrajzi adatokkal**

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

Ha nem rendelkezik a szélességi és hosszúsági adatokat, de a fizikai címeket vagy a hely neve, mint például a városok vagy országok, kereshet a tényleges koordináták egy geokódolási szolgáltatásokat, például a Bing Maps REST-szolgáltatások használatával. További tudnivalók a Bing térképek geokódolás [Itt](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Térbeli típusok lekérdezése
Most, hogy készített egy pillantást a földrajzi adatok beszúrása, vessünk egy pillantást az Azure Cosmos DB SQL és a LINQ használatával az adatok lekérdezésére.

### <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített függvények
Az Azure Cosmos DB a következő nyissa meg a földrajzi Consortium (OGC) beépített függvények támogatja a térinformatikai lekérdezéséhez. A beépített függvény az SQL-nyelv, minden további információkért lásd: [Azure Cosmos DB lekérdezése](how-to-sql-query.md).

|**Használat**|**Leírás**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.|
|ST_WITHIN (spatial_expr, spatial_expr) | Egy logikai kifejezés, amely azt jelzi, hogy a második GeoJSON-objektum (pont, Polygon vagy LineString) belül van-e az első GeoJSON-objektumot (pont, Polygon vagy LineString) adja vissza.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Egy logikai kifejezés jelzi, hogy a két megadott GeoJSON objektum (pont, Polygon vagy LineString) átfedésben adja vissza.|
|ST_ISVALID| Jelzi, hogy a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen egy logikai értéket ad vissza.|
| ST_ISVALIDDETAILED| Egy JSON-értéket tartalmazó logikai értéket, ha a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen, és ha érvénytelen értéket ad vissza, továbbá egy olyan karakterláncértéket, az oka.|

Térbeli funkciók térbeli adatokon közelségi lekérdezések végrehajtásához használható. Ha például Íme egy lekérdezést, amely visszaadja az összes családi dokumentumot, amelyek 30 km-re, a ST_DISTANCE beépített függvény használatával a megadott helyen belül. 

**Lekérdezés**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Ha is kifejezéséhez az indexelési házirendet, majd "distance lekérdezések" fog hatékonyan kiszolgálható keresztül az index. Kifejezéséhez szóló további információkért lásd a lenti. Ha nem rendelkezik a megadott elérési utak a egy térbeli index, is továbbra is végezhet térinformatikai lekérdezéseket megadásával `x-ms-documentdb-query-enable-scan` kérelemfejlécet a beállított érték "true". A .NET-ben, ezt megteheti a választható átadásával **FeedOptions** lekérdezések argumentumának [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) igaz értékre kell állítani. 

Ellenőrizze, hogy ha egy pont esik-e a sokszög ST_WITHIN használható. Sokszög gyakran határokat, például az irányítószámok, állam határok vagy természetes kialakításokat képviselő szolgálnak. Újra Ha is kifejezéséhez az indexelési házirendet, majd "belül" lekérdezések fog hatékonyan kiszolgálható keresztül az index. 

Sokszög argumentumainak ST_WITHIN tartalmazhat csak egyetlen kör, azt jelenti, a poligonok nem tartalmazhat lyuk bennük. 

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
> Ha a hely értéket megadva a vagy argumentum helytelen formátumú vagy érvénytelen, akkor az eredmény az Azure Cosmos DB lekérdezéssel, hogyan nem egyező típusok munkahelyi hasonló **nem definiált** és a lekérdezés eredményeit a rendszer kihagyja a kiértékelt dokumentumot. Ha a lekérdezés eredménytelen, futtassa a ST_ISVALIDDETAILED a hibakeresési miért a térbeli típusa érvénytelen.     
> 
> 

Az Azure Cosmos DB is támogatja a más néven inverz lekérdezések végrehajtásához, index poligonok vagy az Azure Cosmos DB sorokat, majd lekérdezni a területeken, amelyek tartalmaznak egy adott pont a. Ez a minta általában arra használják, a logisztika azonosítását, például amikor egy teherautó be- vagy egy erre kijelölt területen. 

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

ST_ISVALID és ST_ISVALIDDETAILED használható annak ellenőrzésére, ha egy térbeli objektum érvénytelen. A következő lekérdezés például kívüli tartományértéke szélesség (-132.8) rendelkező pontok érvényességét ellenőrzi. ST_ISVALID csak egy logikai értéket ad vissza, és ST_ISVALIDDETAILED adja vissza, a logikai és az az oka, hogy miért minősül érvénytelen tartalmazó karakterlánc.

**Lekérdezés**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Ezek a függvények is használható poligonok ellenőrzése. Például itt használjuk ST_ISVALIDDETAILED egy nem lezárt sokszög ellenőrzése. 

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

### <a name="linq-querying-in-the-net-sdk"></a>A LINQ lekérdezése a .NET SDK-ban
Az SQL .NET SDK-t is a szolgáltatók helyettes módszerek `Distance()` és `Within()` LINQ kifejezés belüli használathoz. Az SQL LINQ-szolgáltató fordítja le ezt a módszert a megfelelő SQL beépített függvényhívások-hívások (ST_DISTANCE és ST_WITHIN jelölik). 

Íme egy példa egy LINQ-lekérdezést, amely megkeresi az összes dokumentum a Azure Cosmos DB-gyűjtemény, amelynek "hely" érték van 30 km-re, a megadott sugarú körön belül pont LINQ használatával.

**Távolság a LINQ-lekérdezésekre**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Ehhez hasonlóan az itt a lekérdezés összes dokumentumot, amelynek "hely" megfelel a megadott box sokszög kereséséhez. 

**LINQ kérdezheti le az**

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


Most, hogy hogyan kérdezhet le "LINQ to" és az SQL-dokumentumok tekintse meg a Microsoft készített, vessünk egy pillantást az Azure Cosmos DB konfigurálása kifejezéséhez.

## <a name="indexing"></a>Indexelés
Hogy leírtak szerint a [séma független indexelése az Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) a papír alakítottuk ki az Azure Cosmos DB-adatbázismotor kell valódi sémafüggetlen JSON első osztályú támogatást nyújthassunk és. Az Azure Cosmos DB írásra optimalizált adatbázismotort a GeoJSON szabványos jelölt térbeli adatok (pontok poligonok és vonalak) natív módon megért.

Legnépszerűbb, a geometry geodéziai koordináták 2D adatsík az alakzatot a tervezett, akkor cellák fokozatosan osztva egy **quadtree**. E cellák 1D belül cella helye alapján vannak leképezve egy **Hilbert terület töltési görbe**, amely megőrzi a pontok helye. Emellett a helyadatok indexelésekor, halad végig néven ismert folyamat **tesszellációs**, azt jelenti, egy helyen metsző összes cellát és azonosított tárolt és kulcsok az Azure Cosmos DB-index. Lekérdezéskor például a pontok és poligonok argumentumok is Csempézett, bontsa ki a megfelelő azonosító cellatartományokról, akkor az indexből adatok lekérésére használt.

Ha megad egy indexelési házirendet, amely tartalmazza a térbeli index / * (minden elérési út), majd a hatékony térinformatikai lekérdezéseket (ST_WITHIN és ST_DISTANCE) található a gyűjteményben lévő összes pontok vannak indexelve. A térbeli indexek nem értéke pontosságú, és mindig használja az alapértelmezett értéke pontosságú.

> [!NOTE]
> Az Azure Cosmos DB támogatja az automatikus indexelését, Linestring, pontok és poligonok
> 
> 

A következő JSON-kódrészletben látható, az indexelési házirendet kifejezéséhez engedélyezve van, vagyis, index bármely GeoJSON-pont térbeli lekérdezése a dokumentumokon belül található. Ha módosítja az indexelési házirendet az Azure portal használatával, a következő JSON indexelési szabályzat engedélyezéséhez térbeli a gyűjtemény az indexelő is megadhat.

**Gyűjtemény indexelési szabályzat JSON-t Spatial pontok és poligonok engedélyezve**

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

Itt láthat egy kódrészletet a .NET-ben, amely bemutatja, hogyan hozzon létre gyűjteményt kifejezéséhez engedélyezve van a pontokat tartalmazó összes elérési utat. 

**Hozzon létre gyűjteményt kifejezéséhez**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

És Íme, hogyan módosíthatja egy meglévő gyűjtemény felülírja a dokumentumokon belül tárolt pontokat kifejezéséhez előnyeinek kihasználása érdekében.

**Egy meglévő gyűjtemény kifejezéséhez módosítása**

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
> Ha a hely a dokumentum GeoJSON érték helytelen formátumú vagy érvénytelen, majd, nem get indexelve a térbeli lekérdezéséhez. Hely értékek ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizheti.
> 
> Ha a gyűjtemény definíciót tartalmaz egy partíciókulcsot, az indexelés átalakítási folyamat nem készül jelentés. 
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy megtanulhatta, hogyan kezdheti el a földrajzi támogatásával az Azure Cosmos DB, ezután is:

* A kódírás a [térinformatikai .NET platformra írt kódmintái a Githubon](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Ismerkedés a térinformatikai lekérdezések a [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo#geospatial)
* Tudjon meg többet [Azure Cosmos DB-lekérdezés](how-to-sql-query.md)
* Tudjon meg többet [Azure Cosmos DB-indexelő házirendek](index-policy.md)

