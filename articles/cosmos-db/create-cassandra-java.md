---
title: Java-alkalmazás létrehozása az Azure Cosmos DB Cassandra API-val
description: Ez a rövid útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Cassandra API profilalkalmazások létrehozására az Azure Portal és a Java használatával
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5a21f36136c6f1d77a2e9cb9108f539c9fb39334
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77134912"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data"></a>Rövid útmutató: Java-alkalmazás létrehozása az Azure Cosmos DB Cassandra API-adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban hozzon létre egy Azure Cosmos DB Cassandra API-fiókot, és a GitHubról klónozott Cassandra Java-alkalmazással hozzon létre egy Cassandra-adatbázist és -tárolót. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül.
- [Java Fejlesztési Készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Irányítsa a `JAVA_HOME` környezeti változót arra a mappára, ahol a JDK telepítve van.
- A [Maven bináris archívum](https://maven.apache.org/download.cgi). Az Ubuntu, `apt-get install maven` fuss telepíteni Maven.
- [Git](https://www.git-scm.com/downloads). Az Ubuntu, `sudo apt-get install git` fuss telepíteni Git.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. A következő lépésekben elvégezheti a Cassandra-alkalmazás klónozását a GitHubról, beállíthatja a kapcsolati sztringet, és futtathatja az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. Ezek a kódrészletek mind az *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java* fájlból származnak.  

* A Cassandra gazdagép-, port-, felhasználónév-, jelszó- és SSL-beállításai meg vannak adva. A kapcsolati sztring adatai az Azure Portal kapcsolati sztring oldaláról származnak.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* A `cluster` csatlakozik az Azure Cosmos DB Cassandra API-hoz, és hozzáférésre visszaad egy munkamenetet.

    ```java
    return cluster.connect();
    ```

A következő kódrészletek az *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java* fájlból származnak.

* Új kulcsterület létrehozása.

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

* Felhasználói entitások beszúrása előkészített utasításobjektum használatával.

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

* Összes felhasználói adat lekérdezése.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Egyetlen felhasználó adatainak lekérdezése.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring részletei lehetővé teszik az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza a **Kapcsolati karakterlánc**lehetőséget. 

    ![Felhasználónév megtekintése és másolás az Azure Portal Kapcsolati sztring oldaláról](./media/create-cassandra-java/copy-username-connection-string-azure-portal.png)

2. Válassza a ![a képernyő jobb oldalán található Másolás gombot](./media/create-cassandra-java/copy-button-azure-portal.png) a CONTACT POINT érték másolásához.

3. Nyissa meg a *config.properties* fájlt a *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources* mappából. 

3. Illessze be a CONTACT POINT értéket a Portalból a `<Cassandra endpoint host>` helyére a 2. sorban.

    Line 2 *config.properties* most meg hasonló 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Lépjen vissza a portálra, és másolja a USERNAME értéket. Illessze be a USERNAME értéket a Portalból a 4. sorban található `<cassandra endpoint username>` érték helyére.

    A *config.properties* 4. 

    `cassandra_username=cosmos-db-quickstart`

4. Lépjen vissza a portálra, és másolja a PASSWORD értéket. Illessze be a PASSWORD értéket a Portalból az 5. sorban található `<cassandra endpoint password>` érték helyére.

    A *config.properties* 5. 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. A 6. sorban, ha egy adott SSL-tanúsítványt kíván használni, cserélje le az `<SSL key store file location>` karakterláncot a SSL-tanúsítvány elérési útvonalára. Ha nem ad meg semmilyen értéket, a rendszer a <JAVA_HOME>/jre/lib/security/cacerts helyen telepített JDK-tanúsítványt használja. 

6. Ha a 6. sort egy adott SSL-tanúsítvány használatára módosította, frissítse a 7. sort a tanúsítvány jelszavával. 

7. Mentse a *config.properties* fájlt.

## <a name="run-the-java-app"></a>A Java-alkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen az `azure-cosmosdb-cassandra-java-getting-started\java-examples` mappába.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. A git terminálablakban használja a következő parancsot a `cosmosdb-cassandra-examples.jar` fájl létrehozásához.

    ```git
    mvn clean install
    ```

3. A git terminálablakban futtassa a következő parancsot a Java-alkalmazás indításához.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A terminálablak értesítéseket jelenít meg a kulcstér és a tábla létrehozásáról. Ezt követően kiválasztja és visszaadja a táblában található összes felhasználót, és megjeleníti a kimenetet, majd azonosító alapján kiválaszt egy sort, és megjeleníti az értéket.  

    Nyomja le a Ctrl+C billentyűkombinációt a program végrehajtásának leállításához és a konzolablak bezárásához.

4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    ![Az adatok megtekintése az Adatkezelőben - Azure Cosmos DB](./media/create-cassandra-java/view-data-explorer-java-app.png)

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot cassandra API-val, és futtathatja a Cassandra Java-alkalmazást, amely cassandra-adatbázist és -tárolót hoz létre. Most már importálhat további adatokat az Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)
