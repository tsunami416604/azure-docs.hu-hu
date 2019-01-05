---
title: 'Oktatóanyag: Mintaadatok betöltése az Azure Cosmos DB Cassandra API táblába Java-alkalmazás használatával'
description: Ez az oktatóanyag bemutatja az Azure Cosmos DB Cassandra API táblához felhasználói mintaadatokat tölthet be a java-alkalmazás használatával.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 68baffea86ce5c877217ec05d477288b71830a42
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037429"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Oktatóanyag: Mintaadatok betöltése az Azure Cosmos DB Cassandra API táblába

A fejlesztők lehetséges, hogy alkalmazásokat, amelyek használják a kulcs-érték párokat. Az Azure Cosmos DB Cassandra API-fiók használhatja, kulcs/érték-adatok tárolására és kezelésére. Ez az oktatóanyag bemutatja az Azure Cosmos DB Cassandra API-fiók egyik táblájába felhasználói mintaadatokat tölthet be a Java-alkalmazás használatával. A Java-alkalmazás használja a [Java illesztőprogram](https://github.com/datastax/java-driver) , és betölti a felhasználói adatok, például a felhasználói Azonosítóját, a felhasználónév és a felhasználó városa. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok betöltése az egy Cassandra-tábla
> * Az alkalmazás futtatása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt nekilátna, [hozzon létre egy Cassandra API-fiókot, -kulcsteret és -táblát](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába

Az adatok Cassandra API-táblába történő betöltéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a "UserRepository.java" fájlt a "src\main\java\com\azure\cosmosdb\cassandra" mappában, és fűzze hozzá a kódot a USER_ID paraméter értékét, a felhasználónév és a user_bcity mezők beszúrni a táblába:

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
 
2. Nyissa meg a "UserProfile.java" fájlt a "src\main\java\com\azure\cosmosdb\cassandra" mappában. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat. Ezután fűzze hozzá a következő kódot, amely mintaadatokat illeszt a Cassandra API-táblába.

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

Nyisson meg egy parancssort vagy terminálablakot, és a mappa elérési utat módosítsa arra, ahol létrehozta a projektet. Futtassa a "mvn tiszta telepítés" parancsot a célmappában belül a cosmosdb-cassandra-examples.jar fájl létrehozásához, és futtassa az alkalmazást. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Ezután megnyithatja az Adatkezelőt az Azure Portalon, hogy ellenőrizze, hozzá lettek-e adva a felhasználói adatok a táblához.
    
## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban már megismerkedett az Azure Cosmos DB Cassandra API-fiók a mintaadatok betöltését. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok lekérdezése egy Cassandra API-fiókból](cassandra-api-query-data.md)
