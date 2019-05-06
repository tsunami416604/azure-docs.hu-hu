---
title: Az Azure Cosmos DB indexelési szabályzatok kezelése
description: Az Azure Cosmos DB indexelési szabályzatok kezelése
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 48d67c765a8a76a6058592f59eb61770e2f23df5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068676"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Az Azure Cosmos DB indexelési szabályzatok kezelése

Azure Cosmos DB, az adatok indexelését követően [indexelési szabályzataihoz](index-policy.md) , amely az egyes tárolók definiált. Alapértelmezett indexelési szabályzat az újonnan létrehozott tárolókhoz érvényesíti a tartomány indexei bármilyen karakterlánc vagy szám, és a térbeli indexek bármely GeoJSON típusú objektumhoz és pont. Ez a szabályzat felülbírálható lesz:

- Az Azure Portalról
- Az Azure CLI használatával
- Az SDK-k használatával

Egy [indexelési házirend frissítése](index-policy.md#modifying-the-indexing-policy) elindít egy index átalakítást. Az átalakítás előrehaladását is követhetők az SDK-k.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Azure Cosmos-tárolókat az indexelési házirendet, amely az Azure portal segítségével közvetlenül szerkesztheti JSON-dokumentumként tárolja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblán, és válassza ki a tárolót, hogy a használni kívánt.

1. Kattintson a **méretezés és beállítások**.

1. Az indexelési szabályzat JSON-dokumentum módosítása (példák [alábbi](#indexing-policy-examples))

1. Kattintson a **mentése** Ha elkészült.

![Az Azure portal segítségével indexelési kezelése](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A [az, cosmos DB gyűjteményfrissítés](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) indexelési házirendet egy tárolót a JSON-definícióját cserélje le az Azure parancssori felületen parancs segítségével:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>A .NET SDK V2 használata

A `DocumentCollection` objektum a [.NET SDK-val v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (lásd: [ebben a rövid útmutatóban](create-sql-api-dotnet.md) a használattal kapcsolatos) tesz közzé egy `IndexingPolicy` tulajdonság, ahol módosíthatja a `IndexingMode` és adja hozzá, vagy távolítsa el a `IncludedPaths` és `ExcludedPaths`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Index átalakítási folyamat nyomon követéséhez át kell adnia egy `RequestOptions` objektum, amely beállítja a `PopulateQuotaInfo` tulajdonságot `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Use the Java SDK

A `DocumentCollection` objektum a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (lásd: [ebben a rövid útmutatóban](create-sql-api-java.md) a használattal kapcsolatos) tesz közzé `getIndexingPolicy()` és `setIndexingPolicy()` módszereket. A `IndexingPolicy` azok kezelése lehetővé teszi, hogy az indexelő üzemmódjának módosítása, és adja hozzá az objektum, vagy távolítsa el és elérési utak nem.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Egy tároló index átalakítási folyamat nyomon követéséhez át kell adnia egy `RequestOptions` objektum, amely kéri a kvótaadatok kell feltöltenie, majd az értéket lekérheti a `x-ms-documentdb-collection-index-transformation-progress` válaszfejléc.

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

## <a name="use-the-nodejs-sdk"></a>A Node.js SDK-val

A `ContainerDefinition` az illesztő [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (lásd: [ebben a rövid útmutatóban](create-sql-api-nodejs.md) a használattal kapcsolatos) tesz közzé egy `indexingPolicy` tulajdonság, ahol módosíthatja a `indexingMode` és adja hozzá, vagy távolítsa el a `includedPaths` és `excludedPaths`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Egy tároló index átalakítási folyamat nyomon követéséhez át kell adnia egy `RequestOptions` objektum, amely beállítja a `populateQuotaInfo` tulajdonságot `true`, majd kérje le az értéket, az a `x-ms-documentdb-collection-index-transformation-progress` válaszfejléc.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>A Python SDK használata

Használatakor a [Python SDK](https://pypi.org/project/azure-cosmos/) (lásd: [ebben a rövid útmutatóban](create-sql-api-python.md) a használattal kapcsolatos), a tárolókonfigurációt felügyelt egy szótárban. Tento slovník származó az indexelési házirendet és az összes attribútum eléréséhez.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Indexelési házirend-példák

Az alábbiakban néhány példa látható a JSON formátumban, azaz, hogy hogyan ki vannak téve az Azure Portalon az indexelő szabályzatokat. Ugyanazokat a paramétereket az Azure CLI vagy bármely SDK segítségével állíthatja be.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Az elutasítás házirendet külön-külön az egyes tulajdonság elérési utak kizárása
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Jóváhagyás házirendet külön-külön néhány tulajdonság elérési utak
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Megjegyzés: Általában javasolt használni egy **lemond** indexelési szabályzat ahhoz, hogy proaktív módon az Azure Cosmos DB indexelése minden új tulajdonság, amely a modell adhatók hozzá.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>A térbeli index használatával csak egy adott tulajdonságra elérési úton
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Az összes tulajdonság elérési utak kizárása de megtartva indexelő aktív

Ez a szabályzat helyzetekben használható, a [idő-az-élettartam (TTL) funkció](time-to-live.md) aktív, de nem a másodlagos index megadása kötelező (Azure Cosmos DB-hez használandó egy tiszta kulcs-érték tároló).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nincs indexelése
```
    {
        "indexingPolicy": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Összetett indexelési szabályzat példák

Mellett tartalmazza az egyes tulajdonságok útvonalai vagy, összetett index is megadhatja. Ha szeretné-e végre egy lekérdezést, amely rendelkezik egy `ORDER BY` záradék több tulajdonságot a egy [összetett index](index-policy.md#composite-indexes) azokat a tulajdonságokat kötelező megadni.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Összetett index (kor desc neve asc) definiálva:
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Ez az összetett index tudná a következő két lekérdezések támogatásához:

#1. lekérdezés:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

#2. lekérdezés:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Összetett index (név asc, kora asc) definiált és (asc nevet, desc kor):

Meghatározhat több különböző összetett indexek belül ugyanazt az indexelési házirendet. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Összetett index (név asc, kora asc) definiálva:

Nem kötelező a sorrend megadásához. Ha nincs megadva, növekvő sorrendben.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

- [Az indexelő áttekintése](index-overview.md)
- [Indexelési házirend](index-policy.md)
