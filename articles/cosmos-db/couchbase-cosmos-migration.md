---
title: Áttelepítés a CouchBase szolgáltatásról az Azure Cosmos DB SQL API-ra
description: Részletes útmutatás a CouchBase szolgáltatásból az Azure Cosmos DB SQL API-ba való áttelepítéshez
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210943"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Áttelepítés a CouchBase szolgáltatásról az Azure Cosmos DB SQL API-ra

Az Azure Cosmos DB egy méretezhető, globálisan terjeszthető, teljes körűen felügyelt adatbázis. Garantáltan alacsony késésű hozzáférést biztosít az adatokhoz. Ha többet szeretne megtudni az Azure Cosmos DB-ről, tekintse meg az [áttekintő](introduction.md) cikket. Ez a cikk utasításokat tartalmaz a Couchbase-hez az Azure Cosmos DB SQL API-fiókjához kapcsolódó Java-alkalmazások áttelepítéséhez.

## <a name="differences-in-nomenclature"></a>A nómenklatúra eltérései

Az alábbiakban azokat a legfontosabb funkciókat, amelyek eltérően működnek az Azure Cosmos DB-ben a Couchbase-hez képest:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase szerver| Fiók       |
|Vödör           | Adatbázis      |
|Vödör           | Konténer/gyűjtemény |
|JSON dokumentum    | Cikk / bizonylat |

## <a name="key-differences"></a>Fő eltérések

* Az Azure Cosmos DB egy "ID" mezőa a dokumentumon belül, míg a Couchbase rendelkezik az azonosítót a gyűjtő részeként. Az "Azonosító" mező egyedi a partíción keresztül.

* Az Azure Cosmos DB skálázható a particionálási vagy horizontális eljárás használatával. Ami azt jelenti, hogy az adatokat több szegmensre/partícióra osztja fel. Ezek a partíciók/szegmensek a megadott partíciókulcs-tulajdonság alapján jönnek létre. Kiválaszthatja a partíció kulcsot, hogy optimalizálja az olvasási műveleteket, valamint az írási/írási műveleteket is. További információ: a [particionálási](./partition-data.md) cikk.

* Az Azure Cosmos DB-ben nem szükséges, hogy a legfelső szintű hierarchia a gyűjteményt jelöljön, mert a gyűjtemény neve már létezik. Ez a funkció sokkal egyszerűbbé teszi a JSON-struktúrát. Az alábbiakban egy példa látható, amely bemutatja a Couchbase és az Azure Cosmos DB közötti adatmodell közötti különbségeket:

   **Kanapé**: Dokumentumazonosító = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: "ID" hivatkozás a dokumentumon belül az alábbiak szerint

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK-támogatás

Az Azure Cosmos DB a következő SDK-k a különböző Java-keretrendszerek támogatásához:

* Aszinkron SDK
* Tavaszi csomagtartó SDK

A következő szakaszok ismertetik, hogy mikor kell használni ezeket az SDK-kat. Vegyünk egy példát, amikor háromféle számítási feladatot látunk el:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase mint dokumentumtár & tavaszi adatalapú egyéni lekérdezések

Ha az áttelepíti az áttelepített munkaterhelés a tavaszi rendszerindítás alapú SDK-n alapul, akkor a következő lépéseket használhatja:

1. Szülő hozzáadása a POM.xml fájlhoz:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Tulajdonságok hozzáadása a POM.xml fájlhoz:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Függőségek hozzáadása a POM.xml fájlhoz:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adja hozzá az alkalmazástulajdonságokat az erőforrások hoz, és adja meg a következőket. Győződjön meg arról, hogy lecseréli az URL-cím, a kulcs és az adatbázis nevének paramétereit:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Adja meg a gyűjtemény nevét a modellben. További jegyzeteket is megadhat. Például, Azonosító, partíciógombot jelöli őket explicit módon:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

A CRUD-műveletek kódkódjai a következők:

### <a name="insert-and-update-operations"></a>Műveletek beszúrása és frissítése

Ahol *_repo* a tárház tárgya, és a *dokumentum* a POJO osztály objektuma. Beszúrhatja `.save` vagy upsert et szúrhat be (ha a dokumentum megadott azonosítóval van elhelyezve). A következő kódrészlet bemutatja, hogyan szúrhat be vagy frissíthető kititasztóobjektum:

```_repo.save(doc);```

### <a name="delete-operation"></a>Törlési művelet

Vegye figyelembe a következő kódrészletet, ahol a doc objektum azonosítója és partíciókulcsa kötelező az objektum megkereséséhez és törléséhez:

```_repo.delete(doc);```

### <a name="read-operation"></a>Olvasási művelet

A dokumentum a partíciókulcs megadásával vagy anélkül is elolvasható. Ha nem adja meg a partíciókulcsot, akkor a rendszer partícióközi lekérdezésként kezeli. Vegye figyelembe a következő kódmintákat, az első az azonosító és a partíciókulcs mező használatával hajtja végre a műveletet. A második példa egy normál mezőt használ & a partíciókulcs mező megadása nélkül.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Ennyi, most már használhatja az alkalmazást az Azure Cosmos DB-vel. A dokumentumban ismertetett példa teljes kódmintája a [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub tárházban érhető el.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase dokumentumtárként & N1QL-lekérdezések használatával

N1QL lekérdezések a módja annak, hogy a lekérdezések a Couchbase.

|N1QL lekérdezés | Azure CosmosDB-lekérdezés|
|-------------------|-------------------|
|SELECT`TravelDocument`META( .id AS `TravelDocument`id, .* HONNAN `TravelDocument` `_type` = "com.xx.xx.xxx.xxx.xxxx" és ország = "India" és bármely m a vízumok megfelel m.type == "Multi-Entry" és m.Country IN ['India', Bhután'] MEGRENDELÉSE ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xxx.xxx.xxxx" és c.country = "India" és m.type = "Multi-Entry" és m.Country IN ('India', 'Bhután') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Az N1QL lekérdezésekben a következő változásokat láthatja:

* Nem kell használni a META kulcsszót, vagy nézze meg az első szintű dokumentumot. Ehelyett létrehozhatja a saját hivatkozást a tárolóra. Ebben a példában úgy tekintettük, hogy "c" (bármi lehet). Ez a hivatkozás az összes első szintű mező előtagjaként használatos. Fr például c.id, c.ország stb.

* Ahelyett, hogy "ANY" most meg tudod csinálni egy illesztésaldokumentum, és utalja azt egy dedikált alias, mint az "m". Miután létrehozta az aldokumentum aliasát, aliast kell használnia. Például, m.Country.

* Az ELTOLÁS sorrendje eltér az Azure Cosmos DB lekérdezésben, először meg kell adnia az ELTOLÁS, majd a LIMIT értéket. Javasoljuk, hogy ne használja a tavaszi adatok SDK-t, ha maximális egyéni definiált lekérdezéseket használ, mivel felesleges többletterhelést okozhat az ügyféloldalon, miközben átadja a lekérdezést az Azure Cosmos DB-nek. Ehelyett van egy közvetlen Async Java SDK, amely lehet használni sokkal hatékonyan ebben az esetben.

### <a name="read-operation"></a>Olvasási művelet

Használja az Async Java SDK-t a következő lépésekkel:

1. Konfigurálja a következő függőséget a POM.xml fájlra:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Hozzon létre egy kapcsolatobjektumot az `ConnectionBuilder` Azure Cosmos DB számára a következő példában látható módszer használatával. Győződjön meg róla, hogy ezt a nyilatkozatot a bab úgy, hogy a következő kódot kell kapni végre csak egyszer:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. A lekérdezés végrehajtásához a következő kódrészletet kell futtatnia:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Most a fenti módszer segítségével több lekérdezést adhat át, és gond nélkül végrehajthatja. Abban az esetben, ha egy nagy lekérdezés végrehajtására van szükség, amely több lekérdezésre osztható, próbálkozzon a következő kódrészletkel az előző helyett:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Az előző kóddal párhuzamosan futtathat lekérdezéseket, és optimalizálhatja az elosztott végrehajtásokat. Továbbá futtathatja a beszúrási és frissítési műveleteket is:

### <a name="insert-operation"></a>Beszúrási művelet

A dokumentum beszúrásához futtassa a következő kódot:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Akkor iratkozz fel Mono, mint:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsert művelet

A upsert művelet megköveteli, hogy megadja a frissítendő dokumentumot. A teljes dokumentum beolvasásához használhatja az olvasási művelet alatt említett kódrészletet, majd módosíthatja a szükséges mező(ke)t. A következő kódrészlet upserts a dokumentumot:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Akkor iratkozz fel a mono. Tekintse meg a monó előfizetési kódrészletet a beszúrási műveletben.

### <a name="delete-operation"></a>Törlési művelet

A következő kódrészlet törlési műveletet tesz:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Ezután iratkozzon fel a monóra, olvassa el a mono előfizetési kódrészletet a beszúrási műveletben. A teljes kódminta a [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub tárházban érhető el.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase billentyű/értékpárként

Ez egy egyszerű típusú számítási feladatok, amelyben lekérdezések helyett lekérdezéseket hajthat végre. Kulcs-/értékpárok esetében kövesse az alábbi lépéseket:

1. Fontolja meg a "/ID" elsődleges kulcsként való megsegítést, amely biztosítja, hogy a kérdésműveletet közvetlenül az adott partíción hajthassa végre. Hozzon létre egy gyűjteményt, és adja meg a "/ID" partíciókulcsot.

1. Kapcsolja ki teljesen az indexelést. Mivel a kiírási műveleteket végre fogja hajtani, nincs értelme indexelési többletterhelést végezni. Az indexelés kikapcsolásához jelentkezzen be az Azure Portalra, és nyissa meg az Azure Cosmos DB-fiókját. Nyissa meg az **Adatkezelőt**, jelölje ki az **adatbázist** és a **tárolót**. Nyissa meg **a Méretezés & beállítások** lapot, és válassza az **Indexelési házirend et**. Az indexelési házirend jelenleg a következőképpen néz ki:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
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
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Cserélje le a fenti indexelési házirendet a következő házirendre:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. A kapcsolatobjektum létrehozásához használja a következő kódrészletet. Kapcsolatobjektum (behelyezés @Bean vagy statikussá tenni):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Most már a CRUD műveleteket a következőképpen hajthatja végre:

### <a name="read-operation"></a>Olvasási művelet

Az elem elolvasásához használja a következő kódrészletet:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Beszúrási művelet

Elem beszúrásához a következő kódot hajthatja végre:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Akkor iratkozz fel a mono, mint:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert művelet

Egy elem értékének frissítéséhez olvassa el az alábbi kódrészletet:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Ezután iratkozzon fel a monóra, olvassa el a mono előfizetési kódrészletet a beszúrási műveletben.

### <a name="delete-operation"></a>Törlési művelet

A törlési művelet végrehajtásához használja a következő kódrészletet:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Ezután iratkozzon fel a monóra, olvassa el a mono előfizetési kódrészletet a beszúrási műveletben. A teljes kódminta a [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub tárházban érhető el.

## <a name="data-migration"></a>Adatok áttelepítése

Az adatok áttelepítése kétféleképpen történik.

* **Használja az Azure Data Factory:** Ez az adatok áttelepítésének legajánlottabb módszere. Konfigurálja a forrást Couchbase és a fogadó azure Cosmos DB SQL API-ként, tekintse meg az Azure [Cosmos DB Data Factory összekötő](../data-factory/connector-azure-cosmos-db.md) cikket a részletes lépéseket.

* **Használja az Azure Cosmos DB adatimportálási eszközét:** Ez a beállítás kisebb mennyiségű adattal rendelkező virtuális gépek használatával történő áttelepítése ajánlott. A részletes lépéseket az [Adatimportőr](./import-data.md) cikkben olvashatja.

## <a name="next-steps"></a>Következő lépések

* A teljesítménytesztelés elvégzéséhez tekintse [teljesítmény- és méretezési tesztelés az Azure Cosmos DB cikkében.](./performance-testing.md)
* A kód optimalizálása, [lásd: Teljesítmény tippek az Azure Cosmos DB](./performance-tips-async-java.md) cikket.
* Fedezze fel a Java Async V3 SDK, [SDK referencia](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub tárhét.
