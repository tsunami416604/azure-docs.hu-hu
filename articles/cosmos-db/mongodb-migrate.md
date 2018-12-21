---
title: Mongoimport, mongorestore és és használata az Azure Cosmos DB MongoDB API-hoz
description: Útmutató adatok importálásához egy MongoDB-fiók API-jába a mongoimport és a mongorestore eszközök segítségével
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: c0fed311a43677465559ca9ea86b384a39b9fbfd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723416"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-configured-for-mongodb-api"></a>Oktatóanyag: Migrálja adatait az Azure Cosmos DB MongoDB API-hoz konfigurált

Ez az oktatóanyag az Azure Cosmos DB MongoDB API-hoz konfigurált MongoDB-ben tárolt adatok migrálása útmutatás. Ha használni szeretné a és adatokat importál a MongoDB-ből az Azure Cosmos DB SQL API-hoz konfigurált, használjon a [adatáttelepítési eszközét](import-data.md) adatok importálásához.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az áttelepítés megtervezése
> * A migrálás előfeltételei
> * Adatok migrálása a mongoimport használatával
> * Adatok migrálása a mongorestore használatával

Mielőtt adatokat telepít át az Azure Cosmos DB MongoDB API-hoz, akkor ügyeljen arra, hogy rendelkezik mintául szolgáló MongoDB-adatait. Ha nem rendelkezik MongoDB-adatbázissal, letöltheti és telepítheti a [MongoDB közösségi kiszolgálót](https://www.mongodb.com/download-center), létrehozhat egy mintaadatbázist, és a mongoimport.exe vagy a mongorestore.exe használatával feltölthet mintaadatokat. 

## <a name="plan-for-migration"></a>Az áttelepítés megtervezése

1. Hozza létre előzetesen és skálázza a gyűjteményeket:
        
   * Az Azure Cosmos DB alapértelmezés szerint másodpercenként 1000 kérelemegység (RU/s) sebességgel hoz létre egy új MongoDB-gyűjteményt. Még a mongoimport vagy a mongorestore eszköz használatával végzett migrálás indítása előtt hozza létre előre az összes gyűjteményt az [Azure Portalról](https://portal.azure.com) vagy a MongoDB-illesztőprogramokról és -eszközökről. Ha az adatméret nagyobb 10 GB-nál, mindenképpen [particionált gyűjteményt](partition-data.md) hozzon létre egy megfelelő szegmenskulccsal. MongoDB javasolja a gyűjtemények az Entitásadatok tárolásához. Az Azure Cosmos database szintjén összehasonlítható méretét és üzembe helyezése átviteli entites is mellé.

   * Az a [az Azure portal](https://portal.azure.com), növeléséhez a gyűjtemény egyetlen különálló partíciós gyűjteménybe 1000 kérelemegység/s és a egy gyűjteményt, és az áttelepítés időtartama 2500 kérelemegység/s. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. Költségtakarékossági okokból a migrálás után érdemes azonnal csökkentenie az átviteli sebességet.

   * Az RU/s érték gyűjteményszinten való megadásán kívül beállíthat egy RU/s értéket gyűjtemények egy készletéhez is a szülő adatbázis szintjén. Ehhez arra van szükség, hogy előre létrehozza az adatbázist és a gyűjteményeket, valamint minden gyűjteményhez meghatározzon egy szegmenskulcsot.

   * Horizontálisan particionált gyűjtemények létrehozásához használhatja kedvenc eszközét, illesztőprogramját vagy SDK-ját is. Ebben a példában a Mongo-felület segítségével hozunk létre egy horizontálisan particionált gyűjteményt:

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Eredmények:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Számítsa ki az egyetlen dokumentum írására vonatkozó, közelítő kérelemegységenkénti díjat:

   a. Csatlakozás a MongoDB API a MongoDB rendszerhéj konfigurált Cosmos fiókhoz. Ezzel kapcsolatos további útmutatást a [MongoDB-alkalmazások az Azure Cosmos DB-hez való csatlakoztatását ismertető](connect-mongodb-account.md) cikkben találhat.
    
   b. Futtasson egy minta beszúrási parancsot a MongoDB-felület egyik mintadokumentuma használatával:
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. Futtassa a ```db.runCommand({getLastRequestStatistics: 1})``` parancsot. A következőhöz hasonló választ kell kapnia:
     
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
        
    d. Jegyezze fel a kérelem díját.
    
1. Határozza meg a gépe és az Azure Cosmos DB felhőszolgáltatás közötti késleltetést:
    
    a. A MongoDB-felületről engedélyezze a részletes naplózást a következő paranccsal: ```setVerboseShell(true)```
    
    b. Futtasson egy egyszerű lekérdezést a következő adatbázison: ```db.coll.find().limit(1)```. A következőhöz hasonló választ kell kapnia:

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. A migrálás előtt távolítsa el a beszúrt dokumentumot, hogy ne jöhessenek létre ismétlődések. A következő paranccsal távolíthat el dokumentumokat: ```db.coll.remove({})```

1. Számítsa ki a *batchSize* és a *numInsertionWorkers* tulajdonságok közelítő értékeit:

    * A *batchSize* tulajdonság esetében ossza el az összes kiosztott kérelemegységet a 3. lépésben egyetlen dokumentum írásához felhasznált kérelemegységek számával.
    
    * Ha a *batchSize* tulajdonság számított értéke legfeljebb 24, ezt a számot adja meg a *batchSize* tulajdonság értékeként.
    
    * Ha a *batchSize* tulajdonság számított értéke 24-nél nagyobb, a *batchSize* tulajdonság értéke legyen 24.
    
    * A *numInsertionWorkers* tulajdonság esetében használja a következő egyenletet: *numInsertionWorkers = (kiosztott átviteli sebesség * késleltetés másodpercben kifejezve) / (kötegméret * egy írási művelethez felhasznált kérelemegységek száma)*.
        
    |Tulajdonság|Érték|
    |--------|-----|
    |batchSize| 24 |
    |Kiosztott kérelemegység | 10000 |
    |Késés | 0,100 s |
    |1 dokumentum írása esetén számlázott kérelemegységek száma | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

1. Futtassa a migrálási parancsot. Az adatmigrálási lehetőségeket a következő szakaszok ismertetik.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Vagy a mongorestore eszközzel (ügyeljen arra, hogy az átviteli sebességet minden gyűjteménynél az előző számításokban használt RU-értékre vagy annál nagyobbra állítsa):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **Átviteli sebesség növelése:** Az adatok migrálása idejére beállítása egy egyéni gyűjtemény átviteli mennyisége vagy egy gyűjtemény függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információk az átviteli sebesség növeléséről az [Azure Portalon](https://portal.azure.com): [Teljesítményszintek és tarifacsomagok az Azure Cosmos DB-ben](performance-levels.md).

* **SSL engedélyezése:** Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. A cikk további részében ismertetett eljárások azt mutatják be, hogyan engedélyezheti az SSL-t a mongoimport és a mongorestore eszköz számára.

* **Azure Cosmos DB-erőforrások létrehozása:** Adatok áttelepítése előtt előre létrehozni az Azure Portalról a gyűjtemények. Ha egy olyan Azure Cosmos DB-fiókba migrál, amely adatbázisszintű átviteli sebességgel rendelkezik, az Azure Cosmos DB-gyűjtemények létrehozásakor mindenképpen adjon meg egy partíciókulcsot.

## <a name="get-your-connection-string"></a>A kapcsolati sztring beszerzése 

1. Az [Azure Portalon](https://portal.azure.com) a bal oldali panelen kattintson az **Azure Cosmos DB** bejegyzésre.
1. Az **Előfizetések** panelen válassza ki a fióknevét.
1. A **Kapcsolati sztring** panelen kattintson a **Kapcsolati sztring** elemre.

   A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.

   ![Kapcsolati sztring panel](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Adatok migrálása a mongoimport használatával

Az alábbi sablon használatával importálhat adatokat az Azure Cosmos DB-fiókjába. Töltse ki a fiókjához tartozó értékekkel a *gazdagép*, *felhasználónév* és *jelszó* mezőket.  

Sablon:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Példa:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Adatok migrálása a mongorestore használatával

A MongoDB API-hoz konfigurált Cosmos-fiókjába az adatok visszaállításához hajtsa végre az importálás a következő sablon használatával. Töltse ki a fiókjához tartozó értékekkel a *gazdagép*, *felhasználónév* és *jelszó* mezőket.

Sablon:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Példa:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, és megtudhatja, hogyan kérdezhet le adatokat az ügyfelek és eszközök használatával a mongodb-hez Cosmos database. 

> [!div class="nextstepaction"]
>[MongoDB-adatok lekérdezése](../cosmos-db/tutorial-query-mongodb.md)
