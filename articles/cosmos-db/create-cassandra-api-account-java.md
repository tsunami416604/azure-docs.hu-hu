---
title: 'Oktatóanyag: Cassandra API-fiók létrehozása a Java-alkalmazás – Azure Cosmos DB használatával'
description: Ez az oktatóanyag bemutatja, hogyan Cassandra API-fiók létrehozása, adjon hozzá egy (más néven a kulcstér) adatbázis és tábla hozzáadása a fiókhoz a Java-alkalmazás használatával.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b6876bf8210d47729ad8e765ccffe709a0fccacc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958694"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Oktatóanyag: Cassandra API-fiók létrehozása az Azure Cosmos DB Java-alkalmazások a kulcs/érték típusú adatok tárolására

A fejlesztők lehetséges, hogy alkalmazásokat, amelyek használják a kulcs-érték párokat. Az Azure Cosmos DB Cassandra API-fiók használatával a kulcs/érték típusú adatok tárolására. Ez az oktatóanyag leírja, hogyan használható a Java-alkalmazás az Azure Cosmos DB Cassandra API-fiók létrehozása, adjon hozzá egy (más néven a kulcstér) adatbázis és tábla hozzáadása. A Java-alkalmazás használja a [Java illesztőprogram](https://github.com/datastax/java-driver) hozhat létre, amely tartalmazza a részleteket, például a felhasználói Azonosítóját, a felhasználónév és a felhasználó városa felhasználói adatbázist.  

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Cassandra-adatbázisfiók létrehozása
> * A fiók kapcsolati sztringjének lekérése
> * Hozzon létre egy Maven-projekt és függőségek
> * Egy adatbázis és egy tábla hozzáadása
> * Az alkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek 

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy  [ingyenes](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  fiókot. 

* A legújabb verziójának beszerzéséhez [Java fejlesztői készlet (JDK)](https://aka.ms/azure-jdks). 

* [Töltse le](https://maven.apache.org/download.cgi) és [telepítése](https://maven.apache.org/install.html) a [Maven](https://maven.apache.org/) bináris archívum. 
  - Ubuntu rendszeren futtathatja az  `apt-get install maven`  parancsot a Maven telepítéséhez. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása 

1. Jelentkezzen be az  [Azure Portalra](https://portal.azure.com/). 

2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre. 

3. Az a **új fiók** panelen adja meg az új Azure Cosmos-fiók beállításait. 

   |Beállítás   |Ajánlott érték  |Leírás  |
   |---------|---------|---------|
   |ID (Azonosító)   |   Adjon meg egy egyedi nevet    | Adjon meg egy egyedi nevet az Azure Cosmos-fiók azonosításához. <br/><br/>A rendszer a cassandra.cosmosdb.azure.com utótaggal egészíti ki a megadott azonosítót a kapcsolódási pont létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.         |
   |API    |  Cassandra   |  A létrehozni kívánt fiók típusát az API határozza meg. <br/> Válassza ki **Cassandra**, mert ebben a cikkben létrehoz egy széles oszlopú adatbázist, amely a Cassandra Query Language (CQL)-szintaxissal lekérdezhetők.  |
   |Előfizetés    |  Az Ön előfizetése        |  Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést.        |
   |Erőforráscsoport   | Név megadása    |  Válassza ki **hozzon létre új**, majd adja meg a fiók új erőforráscsoport nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet.    |
   |Hely    |  Válassza ki a felhasználóihoz legközelebb eső régiót    |  Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos-fiókot üzemeltetni szeretné. Tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz a felhasználók legközelebb eső helyet használja.    |

   ![Fiók létrehozása a portál használatával](./media/create-cassandra-api-account-java/create-account.png)

4. Kattintson a **Létrehozás** gombra. <br/>A fiók létrehozása eltarthat néhány percig. Az erőforrás létrehozása után láthatja a **üzembe helyezés sikeres** értesítést a portál jobb oldalán.

## <a name="get-the-connection-details-of-your-account"></a>A fiók kapcsolati adatainak lekérése  

A kapcsolati sztringek adatainak lekérése az Azure Portalról, és másolja be a Java-konfigurációs fájl azokat. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az a [az Azure portal](https://portal.azure.com/), lépjen az Azure Cosmos-fiókjába. 

2. Nyissa meg a  **Kapcsolati sztring** panelt.  

3. Másolja ki a **CONTACT POINT**, **PORT**, **USERNAME** és **PRIMARY PASSWORD** értékeket, amelyekre a következő lépések során lesz szüksége.

## <a name="create-the-project-and-the-dependencies"></a>A projekt és függőségek létrehozása 

A Java mintaprojektet, amelyet használhat ez a cikk a githubon üzemel. Ez a dokumentum lépéseinek futtatása, vagy töltse le a mintát a a [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) tárház. 

Miután letöltötte a fájlokat, frissítse a kapcsolati sztring adatait belül a `java-examples\src\main\resources\config.properties` fájlt, és futtassa azt.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Használja az alábbi lépéseket a minta teljesen új létrehozása: 

1. A terminálból vagy parancssorból hozzon létre egy Cassandra-demo nevű új Maven-projektet. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Keresse meg a `cassandra-demo` mappát. Egy szövegszerkesztővel nyissa meg a létrejött `pom.xml` fájlt. 

   Adja hozzá a Cassandra-függőségeket, és beépülő modulokat a projekt által igényelt hozhat létre, ahogyan az a [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) fájlt.  

3. A `cassandra-demo\src\main` mappában hozzon létre egy új mappát `resources` néven.  Az erőforrások mappájában adja hozzá a config.properties és log4j.properties fájlokat:

   - A [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) fájl tárolja a Cassandra API-fiók kapcsolati végpont és a kulcs értékét. 
   
   - A [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) fájl szükséges a Cassandra API-val folytatott interakcióra szolgáló naplózási szintet határozza meg.  

4. Keresse meg a `src/main/java/com/azure/cosmosdb/cassandra/` mappát. A cassandra mappán belül hozzon létre egy másik mappát `utils` néven. Ez az új mappa tartalmazza a Cassandra API-fiókhoz való csatlakozáshoz szükséges segédeszközosztályokat. 

   Adja hozzá a [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) osztályt a fürt létrehozásához és a Cassandra-munkamenetek megnyitásához és bezárásához. A fürt az Azure Cosmos DB Cassandra API-fiókhoz kapcsolódik, és visszaad egy munkamenetet eléréséhez. Használja a [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) osztályt a kapcsolati sztring adatainak olvasásához a config.properties fájlból. 

5. A Java-minta felhasználói információkat, például felhasználónév, felhasználói azonosító és felhasználó város egy adatbázist hoz létre. Meg kell adnia a get és set metódusokat a fő függvényben található felhasználói adatok eléréséhez.
 
   Hozzon létre egy [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) osztály alatt a `src/main/java/com/azure/cosmosdb/cassandra/` get mappa és a metódusok beállítása. 

## <a name="add-a-database-and-a-table"></a>Egy adatbázis és egy tábla hozzáadása  

Ez a szakasz ismerteti az adatbázis (kulcstér) és a egy táblázat hozzáadása CQL használatával.

1. A `src\main\java\com\azure\cosmosdb\cassandra` mappában hozzon létre egy új mappát `repository` néven. 

2. Hozzon létre a `UserRepository` Java osztályt, és adja hozzá a következő kód azt: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Lépjen az `src\main\java\com\azure\cosmosdb\cassandra` mappába, és hozzon létre egy új almappát `examples` néven.

4. Hozzon létre a `UserProfile` Java-osztály. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Az alkalmazás futtatása 

1. Nyisson meg egy parancssort vagy terminálablakot. Illessze be az alábbi kódblokkot. 

   Ez a kód a mappa elérési útja, ahol létrehozta a projektet a könyvtárat (cd) változik. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A terminálablak értesítéseket jelenít meg a kulcstér és a tábla létrehozásáról. 
   
2. Most nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a kulcstér és a tábla létrejött-e.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban bemutattuk Cassandra API-fiók létrehozása az Azure Cosmos DB adatbázis és tábla a Java-alkalmazás használatával. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [mintaadatok betöltése a Cassandra API-táblába](cassandra-api-load-data.md).
