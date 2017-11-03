---
title: "Mongodb-protokolltámogatással az Azure Cosmos DB API mongoimport és mongorestore használata |} Microsoft Docs"
description: "Adatok importálása egy API-t a MongoDB-fiók mongoimport és mongorestore használata"
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Az Azure Cosmos DB: Import MongoDB adatok 

Adatok áttelepítése az MongoDB egy Azure Cosmos DB fiókot használja az API-t a mongodb, a következőket kell tennie:

* Töltse le vagy *mongoimport.exe* vagy *mongorestore.exe* a a [MongoDB letöltőközpontból](https://www.mongodb.com/download-center).
* Beolvasása a [API-t a MongoDB-kapcsolati karakterlánc](connect-mongodb-account.md).

Ha a MongoDB importál adatokat, és szeretne használni, az Azure Cosmos DB, használja a [adatáttelepítési eszközét](import-data.md) adatainak importálásához.

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * A kapcsolati karakterlánc beolvasása
> * MongoDB-adatok importálása mongoimport használatával
> * MongoDB-adatok importálása mongorestore használatával

## <a name="prerequisites"></a>Előfeltételek

* Átviteli sebesség növelése: az adatok áttelepítés időtartama függ átviteli állít be a gyűjteményekben. Győződjön meg arról, növelheti a teljesítményt nagyobb adatok áttelepítésre. Az áttelepítés befejezése után csökkenti az átviteli sebesség költségek csökkentése érdekében. További információ az átviteli sebesség növelése a [Azure-portálon](https://portal.azure.com), lásd: [teljesítményszintet és az Azure Cosmos Adatbázisba tarifacsomagok](performance-levels.md).

* SSL engedélyezése: Azure Cosmos-adatbázis a szigorú biztonsági követelmények és szabványok tartoznak. Ne aktiválja az SSL-fiókja folytatott kommunikáció során. Az a cikk többi részében szereplő eljárások közé tartozik a mongoimport és mongorestore SSL engedélyezése.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Található kapcsolati karakterlánc adatainak (host, port, felhasználónév és jelszó)

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali ablaktáblán kattintson a **Azure Cosmos DB** bejegyzés.
2. Az a **előfizetések** ablaktáblán válassza ki a fiók nevét.
3. Az a **kapcsolati karakterlánc** panelen kattintson a **kapcsolati karakterlánc**.  
A jobb oldali ablaktáblán a fiók sikeresen csatlakozni kell az összes információt tartalmazza.

    ![Kapcsolati karakterlánc panel](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Adatok importálása a mongodb-protokolltámogatással API mongoimport használatával

Adatok importálása az Azure Cosmos DB fiókjába, használja az alábbi sablont. Töltse ki *állomás*, *felhasználónév*, és *jelszó* fiókja jellemző értékekkel.  

Sablon:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Példa:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Adatok importálása a mongodb-protokolltámogatással API mongorestore használatával

Az API-nak a MongoDB-fiók adatainak visszaállításához használja a következő sablon az importálás végrehajtása. Töltse ki *állomás*, *felhasználónév*, és *jelszó* fiókja jellemző értékekkel.

Sablon:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Példa:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>A sikeres áttelepítéshez a útmutatója

1. Előre létrehozni, illetve skálázni a gyűjtemények:
        
    * Alapértelmezés szerint Azure Cosmos DB látja el egy új MongoDB gyűjtemény 1000 kérelem egységek használatával (RUs). Az áttelepítés előtt mongoimport, mongorestore vagy mongomirror használatával, előre létrehozni a összes gyűjteményt a [Azure-portálon](https://portal.azure.com) vagy a MongoDB-illesztőprogramok és eszközök. Ha a gyűjtemény 10 GB-nál nagyobb, ügyeljen arra, hogy hozzon létre egy [szilánkos/particionált gyűjtemény](partition-data.md) megfelelő shard kulccsal.

    * Az a [Azure-portálon](https://portal.azure.com), a gyűjtemények átviteli sebesség 1000 RUs egypartíciós gyűjtemény és az áttelepítés számára egy szilánkos gyűjtemény 2500 RUs növelése. A nagyobb átviteli sebességgel elkerülheti a sávszélesség-szabályozás, és kevesebb idő alatt át. Óránkénti számlázási Azure Cosmos DB, csökkentheti az átviteli sebesség közvetlenül a költségek csökkentése érdekében az áttelepítés után.

2. A költségszámítás hozzávetőleges RU egy egyetlen dokumentum írásra:

    a. A MongoDB-rendszerhéjból csatlakozni az Azure Cosmos DB MongoDB-adatbázist. Utasításait található [a MongoDB alkalmazás az Azure Cosmos Adatbázishoz csatlakozni](connect-mongodb-account.md).
    
    b. A minta insert parancs futtatása a MongoDB-rendszerhéjból minta dokumentumok egyikének használatával:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Futtatás ```db.runCommand({getLastRequestStatistics: 1})``` és a jelen szoftverhez hasonló választ kap:
     
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
        
    d. Jegyezze fel a kérelem ingyenesen elérhető.
    
3. Határozza meg a gép az Azure Cosmos DB felhőalapú szolgáltatás várakozási ideje:
    
    a. A MongoDB-rendszerhéjból részletes naplózás engedélyezése a parancs segítségével:```setVerboseShell(true)```
    
    b. Egy egyszerű lekérdezést futtassa az adatbázison: ```db.coll.find().limit(1)```. Az alábbihoz hasonló választ kap:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Távolítsa el a beszúrt dokumentum célja, hogy nincs duplikált dokumentumok az áttelepítés előtt. Dokumentumok eltávolításához használja a következő parancsot:```db.coll.remove({})```

5. Kiszámítja a hozzávetőleges *batchSize* és *numInsertionWorkers* értékeket:

    * A *batchSize*, az összes RUs a RUs a 3. lépésben az egyetlen dokumentum írása a felhasznált által kiosztott osztási.
    
    * Ha az számított *batchSize* < = 24, használja ezt a számot, mint a *batchSize* érték.
    
    * Ha az számított *batchSize* > 24, állítsa be a *batchSize* – 24 közötti értéket.
    
    * A *numInsertionWorkers*, használja a egyenlet: *numInsertionWorkers = (kiosztott átviteli sebesség * késleltetés másodpercben) / (kötegméret * RUs felhasznált egyetlen írási)*.
        
    |Tulajdonság|Érték|
    |--------|-----|
    |batchSize| 24 |
    |Kiépített RUs | 10000 |
    |Késés | 0.100 s |
    |RU felszámított 1 doc írásra | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0,1 s) / (24 darab 10 RUs) = 4.1666*

6. A végső áttelepítési parancsot:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyagot, és megtudhatja, hogyan kérdezhet le a MongoDB adatokat Azure Cosmos DB használatával. 

> [!div class="nextstepaction"]
>[Hogyan lehet MongoDB adatait?](../cosmos-db/tutorial-query-mongodb.md)
