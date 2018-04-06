---
title: A földrajzi adatok az Azure Cosmos DB |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, index és az Azure Cosmos DB és az SQL API térbeli objektumok lekérdezése.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e778f4a9b7ec4935d53eb335462f3c414ff99cd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>A földrajzi és GeoJSON helyre adatokat az Adatbázisba az Azure Cosmos használata
Ez a cikk az a földrajzi funkcióinak bemutatása [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Ez elolvasása, után lesz a következő kérdések megválaszolásához:

* Térbeli adatok tárolása az Azure Cosmos-Adatbázisba
* Hogyan tudja lekérdezni az SQL és a LINQ Azure Cosmos Adatbázisba földrajzi adatok?
* Hogyan engedélyezése vagy letiltása, az Azure Cosmos Adatbázisba térbeli indexelő?

Ez a cikk bemutatja, hogyan térbeli adatok SQL API-val. Ez [GitHub-projekt](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) kód minták.

## <a name="introduction-to-spatial-data"></a>Térbeli adatok bemutatása
Térbeli adatok pozíció és objektumok terület alakja ismerteti. A legtöbb alkalmazásban ezek megegyeznek a föld és földrajzi adatok objektumok. Térbeli adatok segítségével határoz meg a személy, a hely egyik fontos vagy a város, vagy egy lake határ helyét. Gyakori alkalmazási esetei gyakran közelségi kapcsolat lekérdezések, például tartalmaz, amely, a "minden kávézóban található az aktuális hely közelében." 

### <a name="geojson"></a>GeoJSON
Azure Cosmos-adatbázis indexelő, és lekérdezi-e, amely rendelkezik értékét földrajzi pont adatok támogatja a [GeoJSON specification](https://tools.ietf.org/html/rfc7946). GeoJSON adatstruktúrák mindig érvényes JSON-objektumok, hogy tárolhatók és lekérdezett Azure Cosmos DB használatával speciális eszközöket és szalagtárak nélkül. Az Azure Cosmos DB SDK-k biztosítanak a segítőosztályok és módszereket, amelyek megkönnyítik a térbeli adatok használata. 

### <a name="points-linestrings-and-polygons"></a>Pontok, Linestring és sokszögek
A **pont** helyre egyetlen pozíció jelöli. A földrajzi adatok a pont a pontos helyet, amely lehet egy címe élelmiszerboltban áruházbeli, a teljes képernyős, egy autó vagy a város jelöli.  A pont a GeoJSON (és Azure Cosmos DB) használata a koordináta pár vagy szélességi és hosszúsági jelzi. Íme egy példa JSON pontnál.

**Az Azure Cosmos DB pontjai**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A GeoJSON megadását a földrajzi hosszúság értéke határozza meg az első és az a földrajzi hosszúság második. Például a többi leképezési alkalmazások szélességi és hosszúsági szögek és fok formájában jelennek meg. Hosszúsági értékeknek mért vannak a szélességi és-180 fok és 180.0 fok közé esnek és a szélességi értékeknek az Egyenlítőtől vannak mért és-90.0 fok és 90.0 fok közé esnek. 
> 
> Azure Cosmos-adatbázis értelmezi a koordináták a WGS-84 referenciarendszer / határozzák. Ütemezze referenciarendszert kapcsolatos további részletekért lásd az alábbi.
> 
> 

Ez lehet beágyazni egy Azure Cosmos DB dokumentumban helyre adatokat tartalmazó felhasználói profil e példában látható módon:

**Az Azure Cosmos Adatbázisba tárolási helye a profil használata**

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

Pontokon, felül GeoJSON is támogatja a Linestring és sokszögek. **Linestring** jelentenek két vagy több pont és a sor szegmensekből álló csatlakoztassa őket. A földrajzi adatok Linestring általában baromfiszállítás vagy folyókat használhatók. A **sokszög** összekapcsolja az egy lezárt LineString csatlakoztatott pontok-határ. Sokszögek általában használhatók például tavakat természetes kialakulásához vagy például városokat és állapotok politikai joghatóság alá tartozó területeken. Íme egy példa az Azure Cosmos Adatbázisba sokszög. 

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
> A GeoJSON specifikációja megköveteli, hogy érvényes sokszögek, a megadott utolsó koordináta pár kell ugyanaz, mint az első zárt alakzat létrehozásához.
> 
> Egy Sokszögön belül pontok balra érdekében meg kell adni. A sokszög jobbra sorrendben megadva a régióját inverzét jelöli.
> 
> 

Mellett pont, LineString, Polygon, GeoJSON is meghatározza a több földrajzi helyek csoportosítása, valamint tetszőleges tulajdonságok társítása földrajzi hely meghatározásának, ábrázolását egy **szolgáltatás**. Mivel ezek az objektumok érvényes JSON-adatokat, akkor is összes tárolása és feldolgozása történhet az Azure Cosmos-Adatbázisba. Azonban Azure Cosmos DB csak akkor támogatja a pontok automatikus indexeléshez.

### <a name="coordinate-reference-systems"></a>Ütemezze referenciarendszert
Mivel a föld alakját szabálytalan, hány koordináta referenciarendszert (CRS), mindegyiket a saját keretek a referencia- és mértékegységek koordinátáját földrajzi adatok jelennek meg. Például a "nemzeti rács a Britannia" egy referenciarendszert nagyon pontos az Egyesült Királyságban, de nem kívül. 

Használja a legnépszerűbb CRS ma a globális geodéziai rendszer [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). GPS-eszközöket, és számos leképezési szolgáltatás, beleértve a Google térképeket és a Bing térképek API-k használata WGS-84. Azure Cosmos-adatbázis indexelő, és csak a WGS-84 CRS használatával földrajzi adatok lekérdezését támogatja. 

## <a name="creating-documents-with-spatial-data"></a>Térbeli adatokat tartalmazó dokumentumok létrehozása
GeoJSON értékeket tartalmazó dokumentumok létrehozásakor azok automatikusan indexelt a térbeli index a gyűjtemény az indexelési házirendet összhangban. Ha egy Azure Cosmos DB SDK dinamikusan gépelt Python vagy Node.js nyelven dolgozik, érvényes GeoJSON kell létrehoznia.

**A földrajzi adatok node.js dokumentum létrehozása**

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

Az SQL API-k használata, ha a `Point` és `Polygon` belül osztályokat a `Microsoft.Azure.Documents.Spatial` névtér beágyazható helyére vonatkozó információkat az alkalmazás objektumának belül. Ezeket az osztályokat a szerializálás és a térbeli adatok deszerializálása be GeoJSON leegyszerűsíti.

**A .NET földrajzi adatok dokumentum létrehozása**

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

Ha nem rendelkezik a szélességi és hosszúsági adatokat, de a fizikai címeket vagy a hely neve, mint a város vagy ország, például a Bing Maps REST szolgáltatások geokódolás szolgáltatás használatával megjeleníthetők a tényleges koordinátái. További információ a Bing Maps geokódolás [Itt](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>A térbeli típusok lekérdezése
Most, hogy azt már hozott földrajzi adatok beszúrása egy pillantást, vessen egy pillantást SQL és a LINQ használatával Azure Cosmos DB használatával az adatok lekérdezése.

### <a name="spatial-sql-built-in-functions"></a>Térbeli SQL beépített funkciók
Azure Cosmos DB támogatja a következő nyissa meg a földrajzi konzorcium (OGC) beépített függvények földrajzi lekérdezése. Az SQL-nyelv beépített függvények teljes készletének a további részletekért lásd: [lekérdezés Azure Cosmos DB](sql-api-sql-query.md).

<table>
<tr>
  <td><strong>Használat</strong></td>
  <td><strong>Leírás</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Csoport távolságát adja vissza a két GeoJSON-pont, sokszög vagy LineString kifejezések között.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Egy logikai kifejezés jelző az első GeoJSON-objektum (pont, sokszög vagy LineString) objektumban a második GeoJSON (pont, sokszög vagy LineString) adja vissza.</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Egy logikai kifejezés, amely azt jelzi, hogy a két megadott GeoJSON objektumokat (pont, Polygon, vagy LineString) intersect adja vissza.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Azt jelzi, hogy, hogy a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes logikai érték beolvasása.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Egy olyan logikai érték tartalmazó JSON-érték. Ha a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes, és ha érvénytelen értéket adja vissza, továbbá karakterláncként okát.</td>
</tr>
</table>

Térbeli funkciók térbeli adatok közelségi kapcsolat lekérdezések végrehajtásához használható. Például ez visszaadó 30 km-ST_DISTANCE beépített funkcióval a megadott helyen belüli összes termékcsalád dokumentumot. 

**Lekérdezés**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Ha megadja a térbeli indexelő az indexelési házirendet, majd "távolság lekérdezések" szolgáltató hatékonyan keresztül az index. A térbeli indexelő további részletekért tekintse meg a következő szakaszban. Ha nem rendelkezik a megadott elérési út egy térbeli index, akkor is elvégezhetők térbeli lekérdezéseket megadásával `x-ms-documentdb-query-enable-scan` kérelem fejlécben értéke "true"értékre. A .NET, ezt megteheti úgy, hogy a nem kötelező **FeedOptions** tartalmazó lekérdezések argumentumának [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) igaz értékre állítva. 

ST_WITHIN segítségével ellenőrizze, hogy ha egy pont egy Sokszögön belül helyezkedik el. Gyakran sokszögek használatosak irányítószámot, állapot határok vagy természetes kialakulásához határokat. Újra Ha megadja a térbeli indexelő az indexelési házirendet, majd "belül" lekérdezések szolgáltató hatékonyan keresztül az index. 

ST_WITHIN sokszög argumentumainak tartalmazhat csak csengetés, ez azt jelenti, hogy a sokszög nem tartalmazhat lyuk rajtuk. 

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
> Hogyan nem egyező típusok munkahelyi Azure Cosmos DB lekérdezésben hasonló, ha a hely érték meghatározva vagy argumentuma helytelen formátumú vagy érvénytelen, akkor az eredmény **nem definiált** és a kiértékelt dokumentum figyelmen kívül hagyja a lekérdezési eredmények. Ha a lekérdezés eredménytelen, futtassa a ST_ISVALIDDETAILED a hibakeresési miért a térbeli típusa érvénytelen.     
> 
> 

Azure Cosmos-adatbázis is támogat más néven inverz lekérdezések végrehajtásához, ez azt jelenti, hogy sokszögek vagy Azure Cosmos DB sorainak index, majd a megadott pontot tartalmazó területekre lekérdezése. Ezt a mintát gyakran a logisztikai azonosítására szolgál például amikor egy teherautó be- vagy egy meghatározott terület. 

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

ST_ISVALID és ST_ISVALIDDETAILED segítségével ellenőrizze, hogy egy térbeli objektum érvénytelen. Például a következő lekérdezés ellenőrzi a tartomány a földrajzi hosszúság értéke (-132.8) kívüli pont érvényességét. ST_ISVALID csak egy logikai értéket ad vissza, és ST_ISVALIDDETAILED a logikai és a miért akkor tekinthető érvénytelen OK tartalmazó karakterláncot adja vissza.

** Lekérdezése **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Ezeket a funkciókat is sokszögek érvényesítéséhez használható. Például itt használjuk ST_ISVALIDDETAILED nem lezárt sokszög érvényesítéséhez. 

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

### <a name="linq-querying-in-the-net-sdk"></a>A .NET SDK lekérdezése LINQ
Az SQL .NET SDK-t is szolgáltatók helyettes módszerek `Distance()` és `Within()` LINQ kifejezés belüli használatra. Az SQL LINQ szolgáltatónál fordítja le a megfelelő SQL beépített függvényhívásokat módszert hívásokat a (ST_DISTANCE és ST_WITHIN rendre). 

Íme egy példa a LINQ lekérdezés, amely megkeresi az összes dokumentum a Azure Cosmos DB gyűjtemény, amelynek "hely" érték van belül egy megadott 30 km radius pont LINQ használatával.

**Távolság a LINQ lekérdezés**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Ehhez hasonlóan az ide a lekérdezés összes dokumentumot, amelynek "hely" megfelel a megadott mező sokszög kereséséhez. 

**LINQ lekérdezése a belül**

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


Most, hogy azt már tett dokumentumok LINQ és SQL lekérdezése egy pillantást, vessen konfigurálása Azure Cosmos DB térbeli indexelő egy pillantást.

## <a name="indexing"></a>Indexelés
Leírtak azt a [séma Azure Cosmos DB indexelés független](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papír, azt készült Azure Cosmos adatbázis adatbázis-kezelő kell valóban sémát független és első osztályú támogatást nyújtanak a JSON-NÁ. Az optimalizált írási adatbázismotor Azure Cosmos-adatbázis natív módon megértette a GeoJSON standard képviselt térbeli adatok (pontok sokszögek és sorok).

A geometriai alakzatot 2D vezérlősík geodéziai koordinátái a tervezett akkor cellák fokozatosan osztva a ez már a vég, egy **quadtree**. E cellák 1D belüli mátrixcellában helye alapján vannak leképezve a **Hilbert terület töltés görbe**, amely megőrzi az pontok helység szerint. Továbbá ha helyadatok indexelt, mert végig kell vinnie néven ismert folyamat **tesszellációs**, ez azt jelenti, hogy a hely metsző cellák azonosított és az Azure Cosmos DB index kulcsként tárolt. Lekérdezések során például pontok és a sokszög argumentumok is Csempézett kinyerni a megfelelő cella azonosító tartományokat, akkor kiolvassa az adatokat az indexből.

Ha meg van-e az indexelési házirendet, amely tartalmazza a térbeli index / * (összes elérési utat), majd a gyűjteményben található összes pontok indexelt hatékony térbeli lekérdezésekhez (ST_WITHIN és ST_DISTANCE). A térbeli indexek nem pontosság értéket, és mindig az alapértelmezett pontosság értéket használjon.

> [!NOTE]
> Azure Cosmos DB támogatja az automatikus indexeléshez pontok, sokszögek és Linestring
> 
> 

Az alábbi JSON kódrészletben láthatja az indexelési házirendet a térbeli indexelő engedélyezve van, ez azt jelenti, minden olyan térbeli lekérdezése dokumentumok belül található GeoJSON-pont index. Ha módosítja az indexelési házirendet, az Azure portál használatával, térbeli a gyűjteményen indexelő engedélyezéséhez a következő JSON az indexelési házirendet is megadhat.

**Gyűjtemény indexelő házirend JSON-t Spatial pontokhoz, illetve sokszögek engedélyezve**

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

Íme egy kódrészletet a .NET, amely bemutatja, hogyan hozzon létre gyűjteményt térbeli indexelő engedélyezve van a pontokat tartalmazó összes elérési utat. 

**Hozzon létre gyűjteményt térbeli indexelő**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

És ez hogyan módosíthatja egy meglévő gyűjteményt térbeli indexelő felülírja a dokumentumok tárolt pontokat előnyeit.

**Módosítsa a térbeli indexelő egy meglevő gyűjteményhez**

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
> Ha a helyen belül a dokumentum GeoJSON érték helytelen formátumú vagy érvénytelen, majd azt nem get indexelve térbeli lekérdezése. ST_ISVALID és ST_ISVALIDDETAILED értékei ellenőrzéséhez.
> 
> Ha a gyűjtemény definíciója tartalmaz egy partíciókulcsot, átalakítási folyamat indexelő nem jelzett. 
> 
> 

## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Cosmos Adatbázisba földrajzi támogatásával kapcsolatos korábban szerzett tapasztalatok Nolearned, a következőket teheti:

* A elkezdésére a [földrajzi .NET mintakódjainak megtekintése a Githubon](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Beavatkozás nélküli lekérdezni földrajzi kérdez le a [Azure Cosmos DB Tesztlekérdezéseket](http://www.documentdb.com/sql/demo#geospatial)
* További információ [Azure Cosmos adatbázis-lekérdezés](sql-api-sql-query.md)
* További információ [Azure Cosmos DB indexelő házirendek](indexing-policies.md)

