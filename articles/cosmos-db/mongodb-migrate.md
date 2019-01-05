---
title: Az Azure Cosmos DB használatával mongoimport, mongorestore és és a MongoDB-adatok áttelepítése
description: Megtudhatja, hogyan mongoimport, mongorestore és és adatok importálása a Cosmos DB használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039050"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>A MongoDB-adatok áttelepítése az Azure Cosmos DB-hez

 Ebben az oktatóanyagban a Cosmos DB API a mongodb-hez használandó konfigurált való migrálása az Azure Cosmos DB MongoDB-ben tárolt adatokat biztosít. Ha használni szeretné az Azure Cosmos DB SQL API- és adatokat importálhat a MongoDB, használjon a [adatáttelepítési eszközét](import-data.md) az adatok importálásához.

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

Mongoimport, mongorestore és vagy az áttelepítés előtt az összes gyűjtemény létrehozása előre a [az Azure portal](https://portal.azure.com) vagy MongoDB-illesztőprogramok és az eszközök. 

Az a [az Azure portal](https://portal.azure.com), az áttelepítés a gyűjtemények átviteli sebesség növelése. Egy nagyobb átviteli sebességet elkerülheti, sebessége korlátozott, és kevesebb idő alatt át. Költségtakarékossági okokból a migrálás után érdemes azonnal csökkentenie az átviteli sebességet.

Kiépítési átviteli sebesség, a gyűjtemény szintjén, valamint meg is oszthatnak ki átviteli kapacitásokat azon gyűjtemények a kiosztott átviteli sebesség megosztani az adatbázis szintjén. Az adatbázis és a gyűjtemények előre létrehozni, és olyan szegmenskulcsot az egyes gyűjtemények megadása a megosztott átviteli adatbázisban kell.

Az elsődleges eszköz, az illesztőprogram vagy az SDK-val horizontálisan skálázott gyűjtemények is létrehozhat. Ebben a példában a Mongo-felület segítségével hozunk létre egy horizontálisan particionált gyűjteményt:

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

A MongoDB rendszerhéj csatlakozás a Cosmos-fiók használatára a Cosmos DB API a mongodb-hez konfigurált. Talál útmutatást a [egy Cosmos DB MongoDB-alkalmazás csatlakoztatása](connect-mongodb-account.md).

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
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>A Cosmos DB-hez a gép késés meghatározása
    
Engedélyezi a részletes naplózást, a paranccsal az MongoDB rendszerhéj `setVerboseShell(true)`.
    
Alapszintű lekérdezés futtatása paranccsal adatbázison `db.coll.find().limit(1)`.

A következő kimenet hasonló választ kap:

```bash
Fetched 1 record(s) in 100(ms)
```
        
A migráció futtatása előtt távolítsa el a beszúrt dokumentum nincsenek ismétlődő dokumentumok biztosítása érdekében. Dokumentumok a paranccsal eltávolíthatja `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>A hozzávetőleges a batchSize és numInsertionWorkers tulajdonságok értékeinek kiszámítása

Az a **batchSize** tulajdonság, a teljes kiosztott átviteli sebesség (kérelemegység/s) által a befejezésekor a szakaszban a "Határozza meg, a késés a Cosmos DB a gép számára." egyetlen dokumentum írási, felhasznált Kérelemegységek osztása Ha a kiszámított érték legfeljebb 24, használja ezt a számot tulajdonság értékeként. Ha a kiszámított érték nagyobb, mint 24, állítsa be a tulajdonság értéke 24-gyel.
    
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

* **Mintaadatok első**: Győződjön meg arról, hogy néhány adatot a migrálás megkezdése előtt. 

* **Az átviteli sebesség növelése**: Az adatok migrálása idejére a kapacitásegységek egy adott gyűjtemény és adatbázisok kiépítése függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. Miután befejezte az áttelepítést, csökkentheti a költségek csökkentése érdekében az átviteli sebességet. 

* **SSL engedélyezése**:  A cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Győződjön meg arról, amikor Cosmos-fiókját az SSL engedélyezéséhez. Ebben a cikkben ismertetett SSL engedélyezése a mongoimport, mongorestore és és parancsokat tartalmaznak.

* **Hozzon létre a Cosmos DB-erőforrások**: Mielőtt megkezdi az áttelepítést, előzetes létrehozása az Azure Portalról a gyűjtemények. Ha telepít át egy Cosmos-fiók, amely rendelkezik az adatbázisszintű kiosztott átviteli sebesség, ügyeljen arra, hogy adjon meg egy partíciókulcsot, a gyűjtemény létrehozásakor.

* **A kapcsolati sztring lekérése**: Az a [az Azure portal](https://portal.azure.com), jelölje be a **Azure Cosmos DB** bejegyzést a bal oldalon. A **előfizetések**, válassza ki a fiók nevét. A **kapcsolati karakterlánc**válassza **kapcsolati karakterlánc**. A portál jobb oldalán információit jeleníti meg a fiókhoz való csatlakozáshoz szüksége:

    ![Kapcsolati sztring információi](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Mongoimport használata

Adatok importálása a Cosmos-fiók, használja az alábbi sablont.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Cserélje le a \<your_hostname >, \<your_username >, és \<your_password > Paraméterek fiókja jellemző egyedi értékekkel. A következő példában használunk **sampleDB** értékeként \<your_database >, és **sampleColl** értékeként \<your_collection >:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Mongorestore és használata

Állítja vissza a Cosmos DB API a mongodb-hez konfigurált Cosmos fiók adatait, hajtsa végre az importálás az alábbi sablon használatával.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Cserélje le a \<your_hostname >, \<your_username >, és \<your_password > Paraméterek fiókja jellemző egyedi értékekkel. A következő példában használunk **./dumps/dump-2016-12-07** értékeként \<path_to_backup >:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat, törölheti az erőforráscsoportot, Cosmos-fiók és minden kapcsolódó erőforrás. A következő lépések használatával törölje az erőforráscsoportot:

1. Nyissa meg az erőforráscsoport, amelyben létrehozta a Cosmos-fiók.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Erősítse meg az erőforráscsoport törléséhez, és válassza ki a nevét **törlése**.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan kérdezhet le adatokat az Azure Cosmos DB API használatával a mongodb-hez. 

> [!div class="nextstepaction"]
> [Hogyan MongoDB-adatok lekérdezése](../cosmos-db/tutorial-query-mongodb.md)
