---
title: "Gyors üzembe helyezés: Cassandra API-t a Java - Azure Cosmos DB |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan hozzon létre egy profil alkalmazást az Azure portál és a Java az Azure Cosmos DB Cassandra API használatával"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: ef611081-0195-4ad8-9b54-b313588e5754
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 0aafdade2cbf293cf70f09721102ae8ceaef6303
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-cassandra-app-with-java-and-azure-cosmos-db"></a>Gyors üzembe helyezés: Hozza létre a Cassandra alkalmazását, Java és Azure Cosmos DB

A gyors üzembe helyezés bemutatja, hogyan használja a Java és az Azure Cosmos DB [Cassandra API](cassandra-introduction.md) egy példa a Githubról klónozásával egy profil alkalmazás elkészítésére. A gyors üzembe helyezés is bemutatja, hogyan egy Cosmos-DB Azure-fiók létrehozása a web-alapú Azure portál használatával.

Azure Cosmos-adatbázis egy Microsoft globálisan elosztott több modellre adatbázis szolgáltatás. Gyorsan hozzon létre, és a dokumentum, a tábla, a kulcs-érték és a graph adatbázisok, amelyek kihasználhassa a globális terjesztési és a horizontális skálázhatóságot képességeket Azure Cosmos DB középpontjában lekérdezése. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cosmos DB Cassandra API programot: a hozzáférést. Ha még nem telepítette a hozzáférés még, [feliratkozás most](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Alternatív megoldásként, [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/) díjmentesen és kötelezettségvállalás ingyenes Azure-előfizetéssel, nélkül.

Továbbá: 

* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](http://maven.apache.org/download.cgi) bináris archívum [letöltése](http://maven.apache.org/install.html) és [telepítése](http://maven.apache.org/)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozása előtt kell Cassandra-fiók létrehozása az Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Most klónozza a Githubból, állítsa be a kapcsolati karakterláncot, és futtassa azt egy Cassandra alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és használja a `cd` parancs futtatásával módosíthatja a mintaalkalmazás telepítése mappába. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ezzel a paranccsal létrejön egy mintaalkalmazás példányát a számítógépen.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező megadni. Ha most szeretné ismerni az a kód létrehozását az adatbázis-erőforrások, az alábbi kódtöredékek tekintheti meg. Egyéb esetben ugorjon előre [frissítse a kapcsolati karakterlánc](#update-your-connection-string). Ezek a szövegrészek a src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java a kerül.  

* Cassandra host, port, felhasználónév, jelszó és SSL-beállítások vannak állítva. A kapcsolati karakterlánc adatokat a kapcsolati karakterlánc oldal az Azure portálon származik.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* A `cluster` kapcsolódik, az Azure Cosmos DB Cassandra API és eléréséhez ad vissza.

    ```java
    return cluster.connect();
    ```

Az alábbi kódtöredékek vannak a src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java fájlból.

* Hozzon létre egy új kulcstérértesítések használatával.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Új tábla létrehozása.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Helyezze be a felhasználói entitások egy előkészített utasítás objektum használatával.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* A lekérdezés az összes felhasználói adatok beolvasása.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* A lekérdezés egyetlen felhasználó adatainak megszerzése.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás a szolgáltatott adatbázissal való kommunikációhoz.

1. Az a [Azure-portálon](http://portal.azure.com/), kattintson a **kapcsolati karakterlánc**. 

    ![Megtekintheti, és egy felhasználónév másolása az Azure portál, csatlakozási karakterlánc oldal](./media/create-cassandra-java/keys.png)

2. Használja a ![Másolás gombra](./media/create-cassandra-java/copy.png) az ügyfél pont értéket másol a képernyő jobb szélén gombjára.

3. Nyissa meg a `config.properties` fájl C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources mappából. 

3. Illessze be az ügyfél értéket a portálon keresztül `<Cassandra endpoint host>` sor 2.

    Config.properties 2. sor most hasonlóan kell kinéznie 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Lépjen vissza a portálra, és másolja a USERNAME értékét. A USERNAME értékét a portálon keresztül túli `<cassandra endpoint username>` sor 4.

    Config.properties 4 üzletági most hasonlóan kell kinéznie 

    `cassandra_username=cosmos-db-quickstart`

4. Lépjen vissza a portálra, és másolja a jelszó értékét. Illessze be a jelszó értékét a portálon keresztül `<cassandra endpoint password>` 5. sorban.

    Config.properties 5 üzletági most hasonlóan kell kinéznie 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Sor 6, ha egy adott SSL-tanúsítvány, akkor cserélje le `<SSL key store file location>` az SSL-tanúsítvány helyét. Ha nincs megadva érték, a JDK telepített: < JAVA_HOME >/jre/lib/biztonsági/cacerts tanúsítvánnyal. 

6. Ha módosította a sor 6 adott SSL-tanúsítvány használatára, sor 7. a jelszó használata az adott tanúsítvány frissítése. 

7. Mentse a config.properties fájlt.

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A git terminálablakot `cd` a azure-cosmosdb-cassandra-java-getting-started\java-examples mappába.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. A git terminálablakot az alábbi parancs segítségével hozza létre a cosmosdb-cassandra-examples.jar fájlt.

    ```git
    mvn clean install
    ```

3. A git terminálablakot futtassa a következő parancsot a Java-alkalmazás elindításához.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A terminálablakot jeleníti meg az értesítéseket, hogy a kulcstérértesítések használatával és a tábla létrehozásához. Azután választja ki és minden felhasználó visszaadja a táblázatban található és a kimenet megjelenítése és majd sor kijelölése-azonosító szerint, és megjeleníti azt az értéket.  

    Nyomja le a CTRL + C exection a program és a konzol ablak bezárásához. 
    
    Ezután megnyithatja adatkezelő lekérdezés, módosíthatja, és ezekkel az új adatokkal az Azure portálon. 

    ![Az adatkezelő az adatok megjelenítése](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés hozzon létre egy Azure Cosmos DB fiókot, a Cassandra adatbázis és a gyűjtemény-ban az adatkezelő használatával, és futtathatja az alkalmazást az programozott módon hajtsa végre ugyanezt hogy megismerte. További adatok most importálása az Azure Cosmos DB-gyűjteménnyel. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB Cassandra adatok importálása](cassandra-import-data.md)
