---
title: A mongoimport és a mongorestore eszköz használata az Azure Cosmos DB-hez készült MongoDB API-val | Microsoft Docs
description: Útmutató adatok importálásához egy MongoDB-fiók API-jába a mongoimport és a mongorestore eszközök segítségével
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: bdaead6fe739d62340ca225aa1a6d8adf9e86cb9
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100296"
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: MongoDB-adatok importálása 

Ha a MongoDB-ből a MongoDB API-val rendelkező Azure Cosmos DB-fiókba kívánja migrálni az adatokat, tegye a következőket:

* Töltse le a közösségi kiszolgálót a [MongoDB letöltési központból](https://www.mongodb.com/download-center), és telepítse.
* Ehhez használja a „telepítési mappa/bin” könyvtárba telepített mongoimport.exe vagy mongorestore.exe fájlt. 
* Kérje le a [MongoDB API kapcsolati sztringjét](connect-mongodb-account.md).

Ha a MongoDB-ből importál adatokat, és az Azure Cosmos DB SQL API-val tervezi azokat használni, az adatok importálásához használja az [adatmigrálási eszközt](import-data.md).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A kapcsolati sztring lekérése
> * MongoDB-adatok importálása a mongoimport eszközzel
> * MongoDB-adatok importálása a mongorestore eszközzel

## <a name="prerequisites"></a>Előfeltételek

* Az átviteli sebesség növelése: az adatok migrálásának időtartama az egyes gyűjteményekhez vagy egy gyűjteménykészlethez beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információk az átviteli sebesség növeléséről az [Azure Portalon](https://portal.azure.com): [Teljesítményszintek és tarifacsomagok az Azure Cosmos DB-ben](performance-levels.md).

* Az SSL engedélyezése: az Azure Cosmos DB szigorú biztonsági feltételekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. A cikk további részében ismertetett eljárások azt mutatják be, hogyan engedélyezheti az SSL-t a mongoimport és a mongorestore eszköz számára.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>A kapcsolati sztringre vonatkozó információk (gazdagép, port, felhasználónév és jelszó) megkeresése

1. Az [Azure Portalon](https://portal.azure.com) a bal oldali panelen kattintson az **Azure Cosmos DB** bejegyzésre.
2. Az **Előfizetések** panelen válassza ki a fióknevét.
3. A **Kapcsolati sztring** panelen kattintson a **Kapcsolati sztring** elemre.

   A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.

   ![Kapcsolati sztring panel](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Adatok importálása a MongoDB API-ba a mongoimport eszköz használatával

Az alábbi sablon használatával importálhat adatokat az Azure Cosmos DB-fiókjába. Töltse ki a fiókjához tartozó értékekkel a *gazdagép*, *felhasználónév* és *jelszó* mezőket.  

Sablon:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Példa:  

    mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\admin\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Adatok importálása a MongoDB API-ba a mongorestore eszköz használatával

Az alábbi sablon használatával importálhat adatokat a MongoDB-fiók API-ja adatainak visszaállításához. Töltse ki a fiókjához tartozó értékekkel a *gazdagép*, *felhasználónév* és *jelszó* mezőket.

Sablon:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Példa:

    mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Útmutató a sikeres migráláshoz

1. Hozza létre előzetesen és skálázza a gyűjteményeket:
        
    * Az Azure Cosmos DB alapértelmezés szerint másodpercenként 1000 kérelemegység (RU/s) sebességgel hoz létre egy új MongoDB-gyűjteményt. Még a mongoimport, mongorestore vagy mongomirror eszköz használatával végzett migrálás indítása előtt hozza létre előre az összes gyűjteményt az [Azure Portalról](https://portal.azure.com) vagy a MongoDB-illesztőprogramokról és -eszközökről. Ha a gyűjteménye nagyobb 10 GB-nál, mindenképpen [horizontálisan particionált/particionált gyűjteményt](partition-data.md) hozzon létre egy megfelelő szegmenskulccsal.

    * Az [Azure Portalon](https://portal.azure.com) a migrálás idejére növelje a gyűjtemény átviteli sebességét 1000 RU/s értékről egypartíciós gyűjtemény és 2500 RU/s értékről horizontálisan particionált gyűjtemény esetében. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. Mivel az Azure Cosmos DB-ben órás egységekben történik a számlázás, költségtakarékossági okokból a migrálás után érdemes azonnal csökkentenie az átviteli sebességet.

    * Az RU/s érték gyűjteményszinten való megadásán kívül beállíthat egy RU/s értéket gyűjtemények egy készletéhez is a szülő adatbázis szintjén. Ehhez arra van szükség, hogy előre létrehozza az adatbázist és a gyűjteményeket, valamint minden gyűjteményhez meghatározzon egy szegmenskulcsot.

    * Horizontálisan particionált gyűjtemények létrehozásához használhatja kedvenc eszközét, illesztőprogramját vagy SDK-ját is. Ebben a példában a Mongo-felület segítségével hozunk létre egy horizontálisan particionált gyűjteményt:

        ```
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

2. Számítsa ki az egyetlen dokumentum írására vonatkozó, közelítő kérelemegységenkénti díjat:

    a. Csatlakozzon az Azure-Cosmos DB MongoDB-adatbázisához a MongoDB-felületről. Ezzel kapcsolatos további útmutatást a [MongoDB-alkalmazások az Azure Cosmos DB-hez való csatlakoztatását ismertető](connect-mongodb-account.md) cikkben találhat.
    
    b. Futtasson egy minta beszúrási parancsot a MongoDB-felület egyik mintadokumentuma használatával:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Futtassa a ```db.runCommand({getLastRequestStatistics: 1})``` parancsot. A következőhöz hasonló választ kell kapnia:
     
        ```
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
    
3. Határozza meg a gépe és az Azure Cosmos DB felhőszolgáltatás közötti késleltetést:
    
    a. A MongoDB-felületről engedélyezze a részletes naplózást a következő paranccsal: ```setVerboseShell(true)```
    
    b. Futtasson egy egyszerű lekérdezést a következő adatbázison: ```db.coll.find().limit(1)```. A következőhöz hasonló választ kell kapnia:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. A migrálás előtt távolítsa el a beszúrt dokumentumot, hogy ne jöhessenek létre ismétlődések. A következő paranccsal távolíthat el dokumentumokat: ```db.coll.remove({})```

5. Számítsa ki a *batchSize* és a *numInsertionWorkers* tulajdonságok közelítő értékeit:

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

6. Futtassa a végső migrálási parancsot:

   ```
   mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Vagy a mongorestore eszközzel (ügyeljen arra, hogy az átviteli sebességet minden gyűjteménynél az előző számításokban használt RU értékre vagy annál nagyobbra állítsa):
   
   ```
   mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="next-steps"></a>További lépések

Továbbléphet a következő oktatóanyagra, amelyben megismerheti a MongoDB-adatok az Azure Cosmos DB-vel való lekérdezésének módját. 

> [!div class="nextstepaction"]
>[MongoDB-adatok lekérdezése](../cosmos-db/tutorial-query-mongodb.md)
