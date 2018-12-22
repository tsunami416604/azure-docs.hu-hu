---
title: 'Oktatóanyag: A mongoimport és a mongorestore eszköz használata az Azure Cosmos DB-hez készült MongoDB API-val | Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja, hogyan mongoimport, mongorestore és, és importálja az adatokat egy MongoDB-fiókhoz tartozó API használatával.
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: sngun
ms.custom: mvc
Customer intent: As a developer, I want to migrate the data from my existing MongoDB workloads to a MongoDB API account in Azure Cosmos DB, so that the overhead to manage resources is handled by Azure Cosmos DB.
ms.openlocfilehash: b40ca7cf62127a95fe277e205e5a5c5d36618828
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753846"
---
# <a name="tutorial-migrate-your-data-to-a-mongodb-api-account-in-azure-cosmos-db"></a>Oktatóanyag: Az adatok áttelepítése az Azure Cosmos DB MongoDB API-fiók

A fejlesztők lehetséges, hogy NoSQL dokumentum-adatokat használó alkalmazások. Segítségével a MongoDB API-fiókot az Azure Cosmos DB-ben Ez a dokumentum adatok tárolása és elérése. A meglévő alkalmazásait, a MongoDB API-hoz is áttelepítheti az adatokat. Ez az oktatóanyag az Azure Cosmos DB MongoDB API-fiók MongoDB-ben tárolt adatok áttelepítése útmutatás. Ha használni szeretné az Azure Cosmos DB SQL API- és adatokat importálhat a MongoDB, használjon a [adatáttelepítési eszközét](import-data.md) az adatok importálásához.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Áttelepítési terv készítése.
> * Adatok áttelepítése mongoimport használatával.
> * Adatok áttelepítése mongorestore és használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Tekintse át, és végezze el az alábbiakat, mielőtt megkezdi az áttelepítést.

### <a name="plan-for-the-migration"></a>Az áttelepítés megtervezése

Ez a szakasz ismerteti az adatok áttelepítésének megtervezése. Azt fogja megbecsülheti a Kérelemegység díjai, a késés, a gép számára a felhőszolgáltatáshoz való meghatározása, és kiszámítja a Köteg mérete és a beszúrási feldolgozók száma.


#### <a name="pre-create-and-scale-your-collections"></a>Hozza létre előre és a gyűjtemények skálázása

Az Azure Cosmos DB alapértelmezés szerint másodpercenként 1000 kérelemegység (RU/s) sebességgel hoz létre egy új MongoDB-gyűjteményt. Mongoimport, mongorestore és vagy az áttelepítés előtt az összes gyűjtemény létrehozása előre a [az Azure portal](https://portal.azure.com) vagy MongoDB-illesztőprogramok és az eszközök. Ha az adatok mérete meghaladja a 10 GB-ot, hozzon létre egy megfelelő szegmenskulcs particionált gyűjteményt.

Az a [az Azure portal](https://portal.azure.com), az áttelepítés egyetlen különálló partíciós gyűjteménybe 1000 kérelemegység/s és a egy gyűjteményt 2500 kérelemegység/s a gyűjtemények átviteli sebesség növelése. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. Költségtakarékossági okokból a migrálás után érdemes azonnal csökkentenie az átviteli sebességet.

Kiépítési kérelemegység/s, a gyűjtemény szintjén, mellett is kiépíthetők kérelemegység/s gyűjtemények szülő adatbázisszintű készletének. A szülő szintjén kell előre létrehozni az adatbázist és gyűjteményt, és olyan szegmenskulcsot az egyes gyűjtemények megadása.

Horizontálisan skálázott gyűjteményekben az előnyben részesített eszközzel, az illesztőprogram vagy az SDK használatával hozhat létre. Ebben a példában a Mongo-felület segítségével hozunk létre egy horizontálisan particionált gyűjteményt:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
A parancs az alábbi eredményeket adja vissza:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Egyetlen dokumentum írási a hozzávetőleges RU díj kiszámítása

A MongoDB rendszerhéj csatlakozás Azure Cosmos DB MongoDB API-fiókját. Ezzel kapcsolatos további útmutatást a [MongoDB-alkalmazások az Azure Cosmos DB-hez való csatlakoztatását ismertető](connect-mongodb-account.md) cikkben találhat.

Ezután futtassa a mintául szolgáló insert parancs a Mintadokumentumokat egyikének használatával:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Futtassa a parancsot `db.runCommand({getLastRequestStatistics: 1})`.

A következő kimenet hasonló választ kap:
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
Jegyezze fel a kérelem díját.
    
#### <a name="determine-the-latency-from-your-machine-to-the-azure-cosmos-db-cloud-service"></a>Az Azure Cosmos DB felhőszolgáltatáshoz a gépről a késés meghatározása
    
Engedélyezi a részletes naplózást, a paranccsal az MongoDB rendszerhéj `setVerboseShell(true)`.
    
Alapszintű lekérdezés futtatása paranccsal adatbázison `db.coll.find().limit(1)`.

A következő kimenet hasonló választ kap:

```bash
Fetched 1 record(s) in 100(ms)
```
        
A migráció futtatása előtt távolítsa el a beszúrt dokumentum nincsenek ismétlődő dokumentumok biztosítása érdekében. Dokumentumok a paranccsal eltávolíthatja `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>A hozzávetőleges a batchSize és numInsertionWorkers tulajdonságok értékeinek kiszámítása

Az a **batchSize** tulajdonság, osztása a teljes kiosztott kérelemegység alapján a fogyasztott, az egyetlen dokumentum írási befejezettként szakaszában "Határozza meg, a késés, a gép számára, az Azure Cosmos DB felhőszolgáltatásról." Ha a kiszámított érték legfeljebb 24, használja ezt a számot tulajdonság értékeként. Ha a kiszámított érték nagyobb, mint 24, állítsa be a tulajdonság értéke 24-gyel.
    
Értékeként az **numInsertionWorkers** tulajdonságot használja, ez egyenlőségi használja:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

A következő értékek használatával értékének kiszámítása az **numInsertionWorkers** tulajdonság:

| Tulajdonság | Érték |
|--------|-----|
| **batchSize** | 24 |
| Fenntartott egységek | 10,000 |
| Késés | 0,100 s |
| Felhasznált Kérelemegységek | 10 RU |
| **numInsertionWorkers** | (10 000 kérelemegység x 0.100 s) / (24 x 10 RU-k) = **4.1666** |

Futtassa a **monogoimport** áttelepítési parancsot. A parancs paraméterei a cikk későbbi részében ismertetett.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Is használhatja a **monogorestore** parancsot. Ellenőrizze, hogy minden gyűjteményeknél az átviteli sebességet, állítsa be, vagy annál magasabb a korábbi számításokhoz használt fenntartott egységek számát.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Teljesítse az előfeltételeket

Az áttelepítés megtervezése után a következő lépéseket: 

* **Mintaadatok első**: Ellenőrizze, hogy van néhány minta, MongoDB-adatait a migrálás megkezdése előtt. Ha nem rendelkezik MongoDB-adatbázissal, letöltheti és telepítheti a [MongoDB közösségi kiszolgálót](https://www.mongodb.com/download-center), létrehozhat egy mintaadatbázist, és a mongoimport.exe vagy a mongorestore.exe használatával feltölthet mintaadatokat. 

* **Az átviteli sebesség növelése**: Az adatok migrálása idejére beállítása egy egyéni gyűjtemény átviteli mennyisége vagy egy gyűjtemény függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. Miután befejezte az áttelepítést, csökkentheti a költségek csökkentése érdekében az átviteli sebességet. 

* **SSL engedélyezése**: Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. Ebben a cikkben ismertetett SSL engedélyezése a mongoimport, mongorestore és és parancsokat tartalmaznak.

* **Azure Cosmos DB-erőforrások létrehozása**: Mielőtt megkezdi az áttelepítést, előzetes létrehozása az Azure Portalról a gyűjtemények. Ha telepít át az Azure Cosmos DB-fiókot, amelynek adatbázis-szintű teljesítményt, ügyeljen arra, hogy az Azure Cosmos DB-gyűjtemények létrehozásakor adja meg a partíciókulcsot.

* **A kapcsolati sztring lekérése**: Az a [az Azure portal](https://portal.azure.com), jelölje be a **Azure Cosmos DB** bejegyzést a bal oldalon. A **előfizetések**, válassza ki a fiók nevét. A **kapcsolati karakterlánc**válassza **kapcsolati karakterlánc**. A portál jobb oldalán információit jeleníti meg a fiókhoz való csatlakozáshoz szüksége:

    ![Kapcsolati sztring információi](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Mongoimport használata

Az alábbi sablon használatával importálhat adatokat az Azure Cosmos DB-fiókjába.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Cserélje le a \<your_hostname >, \<your_username >, és \<your_password > Paraméterek fiókja jellemző egyedi értékekkel. A következő példában használunk **sampleDB** értékeként \<your_database >, és **sampleColl** értékeként \<your_collection >:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Mongorestore és használata

Az alábbi sablon használatával importálhat adatokat a MongoDB-fiók API-ja adatainak visszaállításához.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Cserélje le a \<your_hostname >, \<your_username >, és \<your_password > Paraméterek fiókja jellemző egyedi értékekkel. A következő példában használunk **./dumps/dump-2016-12-07** értékeként \<path_to_backup >:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiók és minden kapcsolódó erőforrás. A következő lépések használatával törölje az erőforráscsoportot:

1. Nyissa meg az erőforráscsoport, amelyben létrehozta az Azure Cosmos DB-fiókot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Erősítse meg az erőforráscsoport törléséhez, és válassza ki a nevét **törlése**.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan MongoDB-adatok lekérdezése az Azure Cosmos DB használatával. 

> [!div class="nextstepaction"]
> [Hogyan MongoDB-adatok lekérdezése](../cosmos-db/tutorial-query-mongodb.md)
