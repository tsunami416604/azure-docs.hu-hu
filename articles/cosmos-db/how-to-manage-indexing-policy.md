---
title: Indexelési szabályzatok kezelése az Azure Cosmos DB-ben
description: Megtudhatja, hogyan kezelheti az indexelési szabályzatokat, a tulajdonság indexelésből való belefoglalása vagy kizárása, az indexelés definiálása különböző Azure Cosmos DB SDK-k használatával
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252076"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Indexelési szabályzatok kezelése az Azure Cosmos DB-ben

Az Azure Cosmos DB-ben az adatok indexelése az egyes tárolókhoz definiált [indexelési szabályzatok](index-policy.md) alapján történik. Az újonnan létrehozott tárolók alapértelmezett indexelési szabályzata minden sztring vagy szám esetében tartományindexeket kényszerít. Ez a szabályzat saját egyéni indexelési szabályzattal felülbírálható.

## <a name="indexing-policy-examples"></a>Indexelési szabályzat példái

Íme néhány példa a [JSON formátumban](index-policy.md#include-exclude-paths)megjelenő indexelési szabályzatokra, amelyek az Azure Portalon való elérhetővé teszik őket. Ugyanazokat a paramétereket lehet beállítani az Azure CLI vagy bármely SDK keresztül.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Leiratkozási házirend egyes tulajdonságútvonalak szelektív kizárásához

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Ez az indexelési házirend megegyezik ```kind``` ```dataType```azzal, ```precision``` amely alatt a manuálisan állítja be a , és az alapértelmezett értékeiknek. Ezek a tulajdonságok már nem szükségesek explicit módon beállítani, és kihagyhatja őket az indexelési házirend teljes egészében (amint az a fenti példában).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Engedélyezési házirend egyes tulajdonságútvonalak szelektív felvételéhez

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Ez az indexelési házirend megegyezik ```kind``` ```dataType```azzal, ```precision``` amely alatt a manuálisan állítja be a , és az alapértelmezett értékeiknek. Ezek a tulajdonságok már nem szükségesek explicit módon beállítani, és kihagyhatja őket az indexelési házirend teljes egészében (amint az a fenti példában).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Általában ajánlott egy **leiratkozási** indexelési szabályzat használata, amely lehetővé teszi az Azure Cosmos DB proaktív indexelését a modellhez esetlegesen hozzáadott új tulajdonok indexeléséhez.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Térbeli index használata csak egy adott tulajdonságútvonalon

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Példák összetett indexelési házirendre

Az egyes tulajdonságok elérési útjainak kivétele mellett összetett indexet is megadhat. Ha olyan lekérdezést szeretne végrehajtani, `ORDER BY` amely több tulajdonsághoz rendelkezik záradékkal, ezeken a tulajdonságokon [összetett index](index-policy.md#composite-indexes) szükséges. Emellett az összetett indexek teljesítménybeli előnnyel járnak a szűrővel és a különböző tulajdonságokrendelés by záradékkal rendelkező lekérdezések esetében.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Összetett index definiálva (asc név, age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

A fenti összetett index a név és az életkor szükséges a Query #1 és a Query #2:

Lekérdezési #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Lekérdezési #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Ez az összetett index a Query #3 és a Query #4 számára előnyös, és optimalizálja a szűrőket:

Lekérdezési #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Lekérdezési #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Összetett index definiálva (NÉV ASC, AGE ASC) és (név ASC, age DESC):

Ugyanazon indexelési házirenden belül több különböző összetett indexet is definiálhat.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Összetett index definiálva (név ASC, age ASC):

A sorrend megadása nem kötelező. Ha nincs megadva, a sorrend növekvő.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Az összes tulajdonságelérési út kizárása, de az indexelés aktív állapotának megőrzése

Ez a szabályzat olyan helyzetekben használható, ahol az [élő (TTL) szolgáltatás](time-to-live.md) aktív, de nincs szükség másodlagos indexre (az Azure Cosmos DB tiszta kulcs-érték tárolóként való használatához).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nincs indexelés

Ez a házirend kikapcsolja az indexelést. Ha `indexingMode` a `none`beállítása a , akkor nem állíthat be TTL-t a tárolón.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Indexelési házirend frissítése

Az Azure Cosmos DB-ben az indexelési szabályzat az alábbi módszerek bármelyikével frissíthető:

- az Azure portalról
- az Azure CLI használatával
- powershell használata
- az egyik SDK-t használ

Az [indexelési házirend frissítése](index-policy.md#modifying-the-indexing-policy) indexátalakítást vált ki. Ennek az átalakításnak az előrehaladása az SDK-kból is nyomon követhető.

> [!NOTE]
> Indexelési szabályzat frissítésekor az Azure Cosmos DB-nek írt megszakítás nélküli lesz. Az újraindexelés során a lekérdezések részleges eredményeket adhatnak vissza az index frissítése során.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure Cosmos-tárolók az indexelési szabályzatukat json-dokumentumként tárolják, amelyet az Azure Portal lehetővé teszi közvetlenül szerkesztésre.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és jelölje ki azt a tárolót, amelyen dolgozni szeretne.

1. Kattintson **a Méretezés & beállítások gombra.**

1. Módosítsa az indexelési házirend JSON-dokumentumát (lásd [az alábbi](#indexing-policy-examples)példákat)

1. Amikor elkészült, kattintson a **Mentés** elemre.

![Indexelés kezelése az Azure Portalon](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Ha egyéni indexelési házirenddel rendelkező tárolót szeretne létrehozni, [lásd: Hozzon létre egy tárolót egyéni indexházirenddel a CLI használatával](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>A PowerShell használata

Ha egyéni indexelési házirenddel rendelkező tárolót szeretne létrehozni, [lásd: Hozzon létre egy tárolót egyéni indexházirenddel a Powershell használatával](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>A .NET SDK V2 használata

A `DocumentCollection` [.NET SDK v2 objektuma](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `IndexingPolicy` olyan tulajdonságot `IndexingMode` tetszikel et tetszésszerint módosíthatja a és hozzáadhatja vagy eltávolíthatja a és a programot. `IncludedPaths` `ExcludedPaths`

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Az indexátalakítási folyamat nyomon `RequestOptions` követéséhez adja `PopulateQuotaInfo` át `true`az objektumot, amely a tulajdonságot a számára állítja be.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>A .NET SDK V3 használata

A `ContainerProperties` [.NET SDK v3 objektum](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) [(lásd ezt a rövid útmutatót](create-sql-api-dotnet.md) a használatról) olyan tulajdonságot `IndexingPolicy` tetszikel et tetszikel, amely lehetővé teszi a tulajdonság módosítását, valamint `IndexingMode` hozzáadását vagy eltávolítását, `IncludedPaths` valamint `ExcludedPaths`a .

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Az indexátalakítási folyamat nyomon `RequestOptions` követéséhez adjon `PopulateQuotaInfo` át `true`egy objektumot, `x-ms-documentdb-collection-index-transformation-progress` amely a tulajdonságot a programra állítja, majd olvassa be az értéket a válaszfejlécből.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Amikor egy egyéni indexelési házirendet definiál egy új tároló létrehozása közben, az SDK V3 gördülékeny API-ja lehetővé teszi, hogy ezt a definíciót tömören és hatékonyan írja:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>A Java SDK használata

Az `DocumentCollection` objektum a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (lásd ezt a `getIndexingPolicy()` rövid `setIndexingPolicy()` [útmutatót](create-sql-api-java.md) a használat) kiteszi és módszerek. Az `IndexingPolicy` általuk manipulált objektum lehetővé teszi az indexelési mód módosítását, valamint a belefoglalt és kizárt görbék hozzáadását vagy eltávolítását.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Az indexátalakítási folyamat nyomon követéséhez `RequestOptions` adjon át egy objektumot, amely a kvótaadatok `x-ms-documentdb-collection-index-transformation-progress` feltöltését kéri, majd olvassa be az értéket a válaszfejlécből.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>A Node.js SDK használata

A `ContainerDefinition` [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) kapcsolata [(lásd ezt a rövid útmutatót](create-sql-api-nodejs.md) a használattal kapcsolatban) olyan `indexingPolicy` tulajdonságot tetszikel, amely lehetővé teszi a tulajdonság módosítását, valamint hozzáadását `indexingMode` vagy eltávolítását, `includedPaths` valamint `excludedPaths`a .

A tároló adatainak beolvasása

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Az indexelési mód beállítása konzisztensre

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Belelefoglalt görbe hozzáadása térbeli indexszel

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Kizárt elérési út hozzáadása

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

A tároló frissítése módosításokkal

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Az indexátalakítási folyamat nyomon követéséhez `RequestOptions` adjon át `populateQuotaInfo` egy `true`objektumot, amely a `x-ms-documentdb-collection-index-transformation-progress` tulajdonságot a parancsra állítja, majd olvassa be az értéket a válaszfejlécből.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>A Python SDK V3 használata

A [Python SDK V3](https://pypi.org/project/azure-cosmos/) használatakor (lásd ezt a [rövid útmutatót](create-sql-api-python.md) a használatról), a tároló konfigurációja szótárként lesz kezelve. Ebből a szótárból lehetőség van az indexelési házirend és annak összes attribútumának elérésére.

A tároló adatainak beolvasása

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Az indexelési mód beállítása konzisztensre

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Indexelési házirend definiálása egy belefoglalt görbével és térbeli indexszel

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Indexelési házirend definiálása kizárt elérési úttal

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Összetett index hozzáadása

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

A tároló frissítése módosításokkal

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="use-the-python-sdk-v4"></a>A Python SDK V4 használata

A [Python SDK V4](https://pypi.org/project/azure-cosmos/)használata esetén a tároló konfigurációja szótárként lesz kezelve. Ebből a szótárból lehetőség van az indexelési házirend és annak összes attribútumának elérésére.

A tároló adatainak beolvasása

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Az indexelési mód beállítása konzisztensre

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Indexelési házirend definiálása egy belefoglalt görbével és térbeli indexszel

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Indexelési házirend definiálása kizárt elérési úttal

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Összetett index hozzáadása

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

A tároló frissítése módosításokkal

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>További lépések

Az indexelésről az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Indexelési házirend](index-policy.md)
