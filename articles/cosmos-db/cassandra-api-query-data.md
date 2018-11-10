---
title: Adatok lekérdezése Azure Cosmos DB Cassandra API-fiókból
description: Ez a cikk azt mutatja be, hogyan lehet felhasználói adatokat lekérni az Azure Cosmos DB Cassandra API-fiókból egy Java-alkalmazással.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739209"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Oktatóanyag: Adatok lekérdezése Azure Cosmos DB Cassandra API-fiókból

Ez az oktatóanyag azt mutatja be, hogyan lehet felhasználói adatokat lekérni az Azure Cosmos DB Cassandra API-fiókból egy Java-alkalmazással. A Java-alkalmazás a [Java illesztőprogramot](https://github.com/datastax/java-driver) használja, és olyan felhasználói adatokat kérdez le, mint például a felhasználói azonosító, a felhasználónév és a felhasználó városa. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok lekérdezése Cassandra-táblából
> * Az alkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

* Ez a cikk egy többrészes oktatóanyag része. Mielőtt hozzáfogna, győződjön meg arról, hogy elvégezte az előzőekben ismertetett lépéseket, vagyis a Cassandra API-fiók, -kulcstér és -tábla létrehozását, valamint [a mintaadatok betöltését a táblába](cassandra-api-load-data.md). 

## <a name="query-data"></a>Adatok lekérdezése

Az alábbi lépéseket követve kérdezze le a Cassandra API-fiók adatait:

1. Nyissa meg az`src\main\java\com\azure\cosmosdb\cassandra` mappában lévő `UserRepository.java` fájlt. Fűzze hozzá a fájlhoz a következő kódblokkot. Ez a kód a következő három metódust biztosítja: 

   * Az adatbázis összes felhasználójának lekérdezése
   * Felhasználói azonosító alapján szűrt konkrét felhasználó lekérdezése
   * Tábla törlése.

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

   Ez a kód annak a mappának az elérési útjára módosítja a könyvtárat (cd), ahol a projektet létrehozta. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a felhasználói tábla törlődött-e.

## <a name="next-steps"></a>További lépések

* Ez az oktatóanyag bemutatta, hogyan kérdezhet le adatokat egy Azure Cosmos DB Cassandra API-fiókból. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Adatok áttelepítése Cassandra API-fiókba](cassandra-import-data.md)


