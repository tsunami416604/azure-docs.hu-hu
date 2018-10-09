---
title: Mintaadatok betöltése egy Azure Cosmos DB Cassandra API-táblába egy Java-alkalmazással | Microsoft Docs
description: Ez a cikk azt mutatja be, hogyan tölthet be mintául kapott felhasználói adatokat egy táblába az Azure Cosmos DB Cassandra API-fiókban egy Java-alkalmazással.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 662d4b8812ca4b92c1130b9c2c38771e7ec30a06
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393995"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Mintaadatok betöltése egy Azure Cosmos DB Cassandra API-táblába

Ez az oktatóanyag azt mutatja be, hogyan tölthet be mintául kapott felhasználói adatokat egy táblába az Azure Cosmos DB Cassandra API-fiókban egy Java-alkalmazással. A Java-alkalmazás a [Java-illesztőt](https://github.com/datastax/java-driver) használja, és betölti a felhasználói adatokat, például a felhasználói azonosítót, a felhasználónevet és a felhasználó városát. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok betöltése a Cassandra-táblába
> * Az alkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt nekilátna, [hozzon létre egy Cassandra API-fiókot, -kulcsteret és -táblát](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába

Nyissa meg a „UserRepository.java” fájlt az „src\main\java\com\azure\cosmosdb\cassandra” mappában, és adja hozzá a kódot, amely beilleszti a user_id, user_name és user_bcity mezőket a táblába:

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
 
Nyissa meg a „UserProfile.java” fájlt az „src\main\java\com\azure\cosmosdb\cassandra” mappában. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat. Ezután fűzze hozzá a következő kódot, amely mintaadatokat illeszt a Cassandra API-táblába.

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

Nyissa meg a parancssort vagy a terminálablakot, és adja meg a mappa elérési útját, ahol létrehozta a projektet. Futtassa az „mvn clean install” parancsot, hozza létre a cosmosdb-cassandra-examples.jar fájlt a célmappában, majd futtassa az alkalmazást. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Ezután megnyithatja az Adatkezelőt az Azure Portalon, hogy ellenőrizze, hozzá lettek-e adva a felhasználói adatok a táblához.
    
## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan tölthet be adatokat az Azure Cosmos DB Cassandra API-fiókba. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok lekérdezése egy Cassandra API-fiókból](cassandra-api-query-data.md)
