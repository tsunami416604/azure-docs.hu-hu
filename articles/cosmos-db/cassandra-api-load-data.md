---
title: 'Oktatóanyag: Java-alkalmazás mintaadatok betöltéséhez cassandra API-táblába az Azure Cosmos DB-ben'
description: Ez az oktatóanyag bemutatja, hogyan töltheti be a minta felhasználói adatokat egy Cassandra API-táblába az Azure Cosmos DB-ben egy java alkalmazás használatával.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 66c292bcb02e3b2b215cabe4968fa30a45422cef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445649"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Oktatóanyag: Mintaadatok betöltése egy Cassandra API-táblába az Azure Cosmos DB-ben

Fejlesztőként előfordulhat, hogy olyan alkalmazásokat használ, amelyek kulcs-érték párokat használnak. Cassandra API-fiók használatával az Azure Cosmos DB kulcs-és értékadatok tárolására és kezelésére. Ez az oktatóanyag bemutatja, hogyan töltheti be a minta felhasználói adatokat egy táblába egy Cassandra API-fiók az Azure Cosmos DB egy Java alkalmazás használatával. A Java alkalmazás a [Java-illesztőprogramot használja,](https://github.com/datastax/java-driver) és betölti a felhasználói adatokat, például a felhasználói azonosítót, a felhasználónevet és a felhasználói várost. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok betöltése Cassandra táblába
> * Az alkalmazás futtatása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt elkezdené ezt a docot, győződjön meg [arról, hogy létrehozza a Cassandra API-fiókot, a kulcsterületet és a táblát.](create-cassandra-api-account-java.md)   

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába

Az adatok Cassandra API-táblába történő betöltéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a "UserRepository.java" fájlt az "src\main\java\com\azure\cosmosdb\cassandra" mappában, és fűzze hozzá a kódot a user_id, user_name és user_bcity mezők táblázatba való beszúrásához:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Nyissa meg a "UserProfile.java" fájlt az "src\main\java\com\azure\cosmosdb\cassandra" mappában. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat. Ezután fűzze hozzá a következő kódot, amely mintaadatokat illeszt a Cassandra API-táblába.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Nyisson meg egy parancssort vagy terminálablakot, és módosítsa a mappa elérési útját oda, ahol létrehozta a projektet. Futtassa az "mvn clean install" parancsot a cosmosdb-cassandra-examples.jar fájl létrehozásához a célmappában, és futtassa az alkalmazást. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Ezután megnyithatja az Adatkezelőt az Azure Portalon, hogy ellenőrizze, hozzá lettek-e adva a felhasználói adatok a táblához.
    
## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan tölthetbe be mintaadatokat egy Cassandra API-fiókba az Azure Cosmos DB-ben. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok lekérdezése egy Cassandra API-fiókból](cassandra-api-query-data.md)
