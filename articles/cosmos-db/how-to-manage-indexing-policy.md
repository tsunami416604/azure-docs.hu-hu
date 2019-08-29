---
title: Az indexelési szabályzatok kezelése a Azure Cosmos DBban
description: Ismerje meg, hogyan kezelheti az indexelési szabályzatokat Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: thweiss
ms.openlocfilehash: 6e935b88c474d28c06b8cdd76d36f5ba64c942f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093208"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Az indexelési szabályzatok kezelése a Azure Cosmos DBban

Azure Cosmos DB a rendszer az egyes tárolók számára definiált [indexelési házirendek](index-policy.md) alapján indexeli az adatkészleteket. Az újonnan létrehozott tárolók alapértelmezett indexelési házirendje kikényszeríti a tartomány indexeit bármilyen sztring vagy szám esetén, valamint térbeli indexeket minden GeoJSON objektumhoz. A szabályzat felülbírálható:

- a Azure Portal
- Az Azure CLI használata
- Az SDK-k egyikének használata

Az [indexelési házirend frissítése](index-policy.md#modifying-the-indexing-policy) elindítja az index átalakítását. Az átalakítás előrehaladása az SDK-k által is nyomon követhető.

> [!NOTE]
> Az SDK és a portál frissítésének részeként a rendszer az index-szabályzatot úgy fejleszti, hogy egy új, a tárolók számára kiépített index-elrendezéssel igazodjanak. Ezzel az új elrendezéssel az összes primitív adattípus a teljes pontosságú (-1) tartományba van indexelve. Ezért az index típusa és pontossága már nem érhető el a felhasználó számára. A jövőben a felhasználóknak egyszerűen hozzá kell adni az elérési utakat a includedPaths szakaszhoz, és figyelmen kívül kell hagyniuk a indexKinds és a pontosságot. Ez a változás nincs hatással a teljesítményre, és továbbra is frissítheti az indexelési házirendet ugyanazzal a szintaxissal. A meglévő dokumentációban lévő összes mintát továbbra is használhatja a tárgymutató-szabályzat frissítéséhez.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure Cosmos-tárolók az indexelési szabályzatot JSON-dokumentumként tárolják, amelyet a Azure Portal közvetlenül szerkeszthet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza ki a használni kívánt tárolót.

1. Kattintson a **méretezési & beállítások**elemre.

1. Az indexelési szabályzat JSON-dokumentumának módosítása (lásd az [alábbi](#indexing-policy-examples)példákat)

1. Ha elkészült, kattintson a **Mentés** gombra.

![Indexelés kezelése Azure Portal használatával](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure CLI-ről származó [cosmosdb Collection Update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) parancs használatával lecserélheti egy tároló indexelési házirendjének JSON-definícióját:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>A .NET SDK v2 használata

A `DocumentCollection` [.net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) -ből származó objektum ( [](create-sql-api-dotnet.md) lásd a használatáról szóló rövid útmutatót) egy `IndexingPolicy` olyan tulajdonságot tesz elérhetővé `IndexingMode` , amely `ExcludedPaths`lehetővé teszi `IncludedPaths` a módosítását, illetve a hozzáadását és eltávolítását.

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

Az index átalakítási folyamatának nyomon követéséhez `RequestOptions` adjon át egy objektumot `PopulateQuotaInfo` , amely `true`a tulajdonságot állítja be.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>A Java SDK használata

A `DocumentCollection` [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) -ból származó objektum ( [](create-sql-api-java.md) lásd a használatról szóló rövid útmutatót) `getIndexingPolicy()` teszi `setIndexingPolicy()` elérhetővé és metódusokat. Az `IndexingPolicy` általuk manipulált objektum lehetővé teszi az indexelési mód módosítását, valamint a felvett és kizárt elérési utak hozzáadását és eltávolítását.

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

Ha nyomon szeretné követni az index átalakításának folyamatát egy tárolón, adjon meg egy olyan `RequestOptions` objektumot, amely kéri a kvóta-információ feltöltését, majd kérje le az értéket a `x-ms-documentdb-collection-index-transformation-progress` válasz fejlécből.

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

## <a name="use-the-nodejs-sdk"></a>A Node. js SDK használata

A `ContainerDefinition` [Node. js SDK](https://www.npmjs.com/package/@azure/cosmos) felülete (lásd [](create-sql-api-nodejs.md) a használattal kapcsolatos rövid útmutatót) egy olyan `indexingPolicy` tulajdonságot tesz elérhetővé, `indexingMode` amely lehetővé teszi a `includedPaths` módosítását, illetve a és `excludedPaths`a hozzáadását, illetve eltávolítását.

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

Ha nyomon szeretné követni az index átalakításának folyamatát egy tárolón `RequestOptions` , adjon meg egy objektumot `true`, amely a `populateQuotaInfo` tulajdonságot állítja be `x-ms-documentdb-collection-index-transformation-progress` , majd kérje le az értéket a válasz fejlécből.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>A Python SDK használata

A [PYTHON SDK](https://pypi.org/project/azure-cosmos/) használatakor (tekintse meg [ezt](create-sql-api-python.md) a rövid útmutatót a használatáról) a tároló konfigurációja szótárként van kezelve. Ebből a szótárból elérhető az indexelési házirend és annak összes attribútuma.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [
    {"path": "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Példák indexelési házirendre

Az alábbiakban néhány példát láthat a JSON-formátumában látható indexelési házirendekre, így azok elérhetővé válnak a Azure Portal. Ugyanezen paraméterek állíthatók be az Azure CLI-n vagy bármely SDK-n keresztül.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Letiltási szabályzat a tulajdonságok egyes elérési útjai szelektív kizárásához
```
    {
        "indexingMode": "consistent",
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Bizonyos tulajdonságok elérési útjainak szelektív kiválasztására szolgáló szabályzat
```
    {
        "indexingMode": "consistent",
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

Megjegyzés: Általában ajánlott egy kijelentkezési indexelési házirend használata, amely lehetővé teszi, hogy Azure Cosmos DB proaktív módon indexelje a modellhez esetleg hozzáadott új tulajdonságokat.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Térbeli index használata csak adott tulajdonság elérési útján
```
    {
        "indexingMode": "consistent",
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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Az összes tulajdonság elérési útjának kizárása, de az indexelés aktív marad

Ez a szabályzat olyan helyzetekben használható, ahol az élettartam [(TTL) funkció](time-to-live.md) aktív, de nincs szükség másodlagos indexre (Azure Cosmos db tiszta kulcs-érték tárolóként való használatára).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nincs indexelés
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Összetett indexelési házirend – példák

Az egyéni tulajdonságok elérési útjának belefoglalása vagy kizárása mellett összetett index is megadható. Ha olyan lekérdezést szeretne végrehajtani, amely több tulajdonságra vonatkozó `ORDER BY` záradékot tartalmaz, az ezen tulajdonságok [összetett indexét](index-policy.md#composite-indexes) kell megadni.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Összetett index definiálva (név ASC, Age desc):
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

Ez az összetett index a következő két lekérdezést képes támogatni:

Lekérdezés #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Lekérdezés #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Összetett index definiálva (név ASC, Age ASC) és (név ASC, Age desc):

Több különböző összetett index is definiálható ugyanabban az indexelési házirendben. 
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Összetett index definiálva (név ASC, Age ASC):

A sorrend megadása nem kötelező. Ha nincs megadva, a sorrend növekvő.
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

Az indexeléssel kapcsolatban az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Indexelési házirend](index-policy.md)
