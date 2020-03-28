---
title: 'Oktatóanyag: Cassandra API-fiók lekérdezési adatai az Azure Cosmos DB-ben'
description: Ez az oktatóanyag bemutatja, hogyan lehet lekérdezni a felhasználói adatokat egy Azure Cosmos DB Cassandra API-fiókból egy Java alkalmazás használatával.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
Customer intent: As a developer, I want to build a Java application to query data stored in a Cassandra API account of Azure Cosmos DB so that customers can manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 69a9bc912f2cd52e52ca6403187f993413539ecd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "60899899"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Oktatóanyag: Cassandra API-fiók lekérdezési adatai az Azure Cosmos DB-ben

Fejlesztőként előfordulhat, hogy olyan alkalmazásokat használ, amelyek kulcs-érték párokat használnak. Cassandra API-fiók használatával az Azure Cosmos DB tárolja és lekérdezi a kulcs/érték adatokat. Ez az oktatóanyag bemutatja, hogyan lehet lekérdezni a felhasználói adatokat egy Cassandra API-fiókból az Azure Cosmos DB-ben egy Java alkalmazás használatával. A Java alkalmazás a [Java-illesztőprogramot](https://github.com/datastax/java-driver) használja, és lekérdezi a felhasználói adatokat, például a felhasználói azonosítót, a felhasználónevet és a felhasználói várost. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Cassandra-tábla adatainak lekérdezése
> * Az alkalmazás futtatása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt elkezdené, győződjön meg arról, hogy az előző lépéseket a Cassandra API-fiók, kulcstér, tábla és [a minta adatok betöltése a táblába.](cassandra-api-load-data.md) 

## <a name="query-data"></a>Adatok lekérdezése

Az alábbi lépéseket követve kérdezze le a Cassandra API-fiók adatait:

1. Nyissa meg az`src\main\java\com\azure\cosmosdb\cassandra` mappában lévő `UserRepository.java` fájlt. Fűzze hozzá a fájlhoz a következő kódblokkot. Ez a kód a következő három metódust biztosítja: 

   * Az adatbázis összes felhasználójának lekérdezése
   * Felhasználói azonosító alapján szűrt konkrét felhasználó lekérdezése
   * Tábla törlése

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Nyissa meg az`src\main\java\com\azure\cosmosdb\cassandra` mappában lévő `UserProfile.java` fájlt. Ez az osztály tartalmazza a createKeyspace és a createTable parancsokat meghívó fő metódust. Szúrja be a korábban meghatározott adatmetódusokat. Most fűzze hozzá a következő kódot, amely lekérdezi az összes felhasználót vagy egy adott felhasználót:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>A Java-alkalmazás futtatása
1. Nyisson meg egy parancssort vagy terminálablakot. Illessze be az alábbi kódblokkot. 

   Ez a kód megváltoztatja a könyvtárat (cd) arra a mappaelérési útra, ahol a projektet létrehozta. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a felhasználói tábla törlődött-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan lehet adatokat lekérdezni egy Cassandra API-fiókból az Azure Cosmos DB-ben. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok áttelepítése Cassandra API-fiókba](cassandra-import-data.md)


