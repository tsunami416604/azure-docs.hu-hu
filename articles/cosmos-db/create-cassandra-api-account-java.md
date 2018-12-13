---
title: Hozzon létre egy Java-alkalmazás – Azure Cosmos DB Cassandra API-fiókot
description: Ez a cikk bemutatja, hogyan hozhat létre Cassandra API-fiókot, és hogyan adhat hozzá adatbázist (más néven kulcsteret) és táblát egy Java-alkalmazás segítségével.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0bff57d91a777619b825dacef5988dda010c794b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138840"
---
# <a name="tutorial-create-an-azure-cosmos-db-cassandra-api-account-by-using-a-java-application"></a>Oktatóanyag: Egy Azure Cosmos DB Cassandra API-fiók létrehozása a Java-alkalmazás használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Java-alkalmazás segítségével egy Cassandra API-fiókot az Azure Cosmos DB-ben, és hogyan adhat hozzá a fiókhoz egy adatbázist (más néven kulcsteret) és egy táblát. A Java-alkalmazás a [Java illesztőprogramot](https://github.com/datastax/java-driver) használja egy felhasználói adatbázis létrehozásához, amely olyan adatokat tartalmaz, mint a felhasználók azonosítója, neve és városa.  

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Cassandra-adatbázisfiók létrehozása
> * A fiók kapcsolati sztringjének lekérése
> * Maven-projekt és függőségek létrehozása
> * Egy adatbázis és egy tábla hozzáadása
> * Az alkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek 

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy  [ingyenes](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  fiókot. Alternatív lehetőségként  [kipróbálhatja ingyenesen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/)  Azure-előfizetés, díjfizetés és elköteleződés nélkül. 

* Szerezze be a [Java fejlesztői készlet (JDK)](https://aka.ms/azure-jdks) legfrissebb verzióját 

* [Töltse le](https://maven.apache.org/download.cgi) és [telepítse](https://maven.apache.org/install.html) a [Maven](https://maven.apache.org/) bináris archívumot 
  - Ubuntu rendszeren futtathatja az  `apt-get install maven`  parancsot a Maven telepítéséhez. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása 

1. Jelentkezzen be az  [Azure Portalra](https://portal.azure.com/). 

2. Válassza az  **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** lehetőséget. 

3. Az  **Új fiók**  panelen adja meg az új Azure Cosmos DB-fiók beállításait. 

   |Beállítás   |Ajánlott érték  |Leírás  |
   |---------|---------|---------|
   |ID (Azonosító)   |   Adjon meg egy egyedi nevet    | Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. <br/><br/>A rendszer a cassandra.cosmosdb.azure.com utótaggal egészíti ki a megadott azonosítót a kapcsolódási pont létrehozásához, ezért válasszon egyedi, de felismerhető azonosítót.         |
   |API    |  Cassandra   |  A létrehozni kívánt fiók típusát az API határozza meg. <br/> Válassza a **Cassandra** lehetőséget, mivel ebben a cikkben olyan széles oszlopú adatbázist fog létrehozni, amely CQL szintaxis használatával kérdezhető le.  |
   |Előfizetés    |  Az Ön előfizetése        |  Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést.        |
   |Erőforráscsoport   | Név megadása    |  Válassza ki az  **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét. Az egyszerűség kedvéért használhat az azonosítójával megegyező nevet.    |
   |Hely    |  Válassza ki a felhasználóihoz legközelebb eső régiót    |  Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.    |

   ![Fiók létrehozása a portál használatával](./media/create-cassandra-api-account-java/create-account.png)

4. Ezután kattintson a  **Létrehozás** gombra. <br/>A fiók létrehozása eltarthat néhány percig. Az erőforrás létrehozását követően a portál jobb sarkában megjelenik **Az üzembe helyezés sikeres** értesítés.

## <a name="get-the-connection-details-of-your-account"></a>A fiók kapcsolati adatainak lekérése  

Kérje le a kapcsolati sztringet az Azure Portalon, majd másolja be a Java konfigurációs fájlba. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az  [Azure Portalon](https://portal.azure.com/) lépjen a Cosmos DB-fiókra. 

2. Nyissa meg a  **Kapcsolati sztring** panelt.  

3. Másolja ki a **CONTACT POINT**, **PORT**, **USERNAME** és **PRIMARY PASSWORD** értékeket, amelyekre a következő lépések során lesz szüksége.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Maven-projekt, függőségek és segédeszközosztályok létrehozása 

A cikkben használt Java-mintaprojekt a GitHubon érhető el. Az [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) adattárból tölthető le. 

Miután letöltötte a fájlokat, frissítse a kapcsolati sztringet a `java-examples\src\main\resources\config.properties` fájlban, majd futtassa a fájlt.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Másik lehetőségként az alapoktól is létrehozhatja a mintát.  

1. A terminálból vagy parancssorból hozzon létre egy Cassandra-demo nevű új Maven-projektet. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Keresse meg a `cassandra-demo` mappát. Egy szövegszerkesztővel nyissa meg a létrejött `pom.xml` fájlt. 

   Adja hozzá a projekthez szükséges Cassandra-függőségeket és beépülő modulokat, amelyeket a [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) fájl sorol fel.  

3. A `cassandra-demo\src\main` mappában hozzon létre egy új mappát `resources` néven.  Az erőforrások mappájában adja hozzá a config.properties és log4j.properties fájlokat:

   - A [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) fájl tartalmazza az Azure Cosmos DB Cassandra API kapcsolatának végpont és kulcs értékeit. 
   
   - A [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) fájl adja meg, hogy a Cassandra API-val való kommunikációt milyen szinten kell naplózni.  

4. Ezután lépjen a `src/main/java/com/azure/cosmosdb/cassandra/` mappába. A cassandra mappán belül hozzon létre egy másik mappát `utils` néven. Ez az új mappa tartalmazza a Cassandra API-fiókhoz való csatlakozáshoz szükséges segédeszközosztályokat. 

   Adja hozzá a [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) osztályt a fürt létrehozásához és a Cassandra-munkamenetek megnyitásához és bezárásához. A fürt csatlakozik az Azure Cosmos DB Cassandra API-hoz, és hozzáférésre visszaad egy munkamenetet. Használja a [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) osztályt a kapcsolati sztring adatainak olvasásához a config.properties fájlból. 

5. A Java-minta létrehoz egy adatbázist, amely az olyan felhasználói információkat tartalmazza, mint például a felhasználók neve, azonosítója és városa. Meg kell adnia a get és set metódusokat a fő függvényben található felhasználói adatok eléréséhez.
 
   Hozzon létre egy [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) osztályt a `src/main/java/com/azure/cosmosdb/cassandra/` mappában a get és set metódusokkal. 

## <a name="add-a-database-and-a-table"></a>Egy adatbázis és egy tábla hozzáadása  

Ez a szakasz ismerteti, hogyan adhat hozzá adatbázist (kulcsteret) és táblát a Cassandra lekérdezési nyelv (CQL) használatával. E parancsok CQL szintaxisával kapcsolatban lásd a [kulcstér létrehozása](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) és [tábla létrehozása](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable) lekérdezési szintaxisok ismertetését. 

1. Az `src\main\java\com\azure\cosmosdb\cassandra` mappában hozzon létre egy új mappát `repository` néven. 

2. Ezután hozza létre a `UserRepository` Java-osztályt és adja hozzá a következő kódot: 

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

4. Ezután hozza létre a `UserProfile` Java-osztályt. Ez az osztály tartalmazza a fő metódust, amely meghívja a korábban definiált createKeyspace és createTable metódusokat: 

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

   Ez a kód annak a mappának az elérési útjára módosítja a könyvtárat (cd), ahol a projektet létrehozta. Ezután futtatja az `mvn clean install` parancsot a `cosmosdb-cassandra-examples.jar` fájl célmappán belüli létrehozásához. Végül futtatja a Java-alkalmazást.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   A terminálablak értesítéseket jelenít meg a kulcstér és a tábla létrehozásáról. 
   
2. Most nyissa meg az Azure Portalon az **Adatkezelőt**, és ellenőrizze, hogy a kulcstér és a tábla létrejött-e.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre Azure Cosmos DB Cassandra API-fiókot, adatbázist és táblát egy Java-alkalmazással. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [mintaadatok betöltése a Cassandra API-táblába](cassandra-api-load-data.md).
