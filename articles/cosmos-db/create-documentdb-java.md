---
title: "Azure Cosmos DB dokumentum-adatbázis létrehozása Javával | Microsoft Docs | Microsoft Docs'"
description: "Egy Java-kódmintát mutat be, amellyel csatlakozhat az Azure Cosmos DB DocumentDB API-hoz és lekérdezheti azt"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: mimig
ms.openlocfilehash: 5a793abdc24387ae2b758d29b9dfb25f134097d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Dokumentum-adatbázis létrehozása a Java és az Azure Portal használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Azure Cosmos DB használja, gyorsan hozzon létre és lekérdezni a felügyelt dokumentumokban, a táblának és a graph-adatbázisokat.

Ez a rövid útmutató létrehoz egy dokumentum-adatbázist az Azure Cosmos DB-hez készült Azure Portal-eszközök használatával. A rövid útmutató emellett bemutatja, hogyan hozhat létre gyorsan egy Java-konzolalkalmazást a [DocumentDB Java API](documentdb-sdk-java.md) használatával. A rövid útmutatóban lévő utasítások bármilyen, Java-programok futtatására alkalmas operációs rendszeren végrehajthatók. A gyors üzembe helyezés befejezése lesz ismeri a létrehozása és módosítása a dokumentum adatbázis erőforrásainak vagy a felhasználói felület vagy programozott módon, amelyik igény szerint.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Továbbá: 

* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu rendszeren futtassa az `apt-get install default-jdk` parancsot a JDK telepítéséhez.
    * Ügyeljen arra, hogy a JAVA_HOME környezeti változó arra a mappára mutasson, ahová a JDK telepítve lett.
* [Maven](http://maven.apache.org/download.cgi) bináris archívum [letöltése](http://maven.apache.org/install.html) és [telepítése](http://maven.apache.org/)
    * Ubuntu rendszeren futtathatja az `apt-get install maven` parancsot a Maven telepítéséhez.
* [Git](https://www.git-scm.com/)
    * Ubuntu rendszeren futtathatja a `sudo apt-get install git` parancsot a Git telepítéséhez.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy SQL- (DocumentDB-) adatbázisfiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Mintaadatok hozzáadása

Az Adatkezelő segítségével adatokat adhat hozzá az új gyűjteményhez.

1. Bontsa ki a **elemek** gyűjtemény, kattintson a **dokumentumok** > **új dokumentum**.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. Most dokumentum hozzáadása a gyűjteményhez a következő struktúrával, és kattintson a **mentése**.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  Hozzon létre és mentsen módosítja egy további dokumentum `id` 2 és a többi tulajdonság ismertető elférjen módosítása. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.

## <a name="query-your-data"></a>Az adatok lekérdezése

Használhatja lekérdezések most adatkezelő lekérdezéséhez és szűrje az adatokat.

1. Tekintse meg, hogy alapértelmezés szerint a lekérdezés beállításai `SELECT * FROM c`. Az alapértelmezett lekérdezéssel kéri le, és megjeleníti egy gyűjtemény összes dokumentumot. 

    ![Az adatok Explorer alapértelmezett lekérdezés "kiválasztása * c"](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. A lekérdezés módosításához kattintson a **szűrő szerkesztése** gomb, hozzáadása `ORDER BY c._ts DESC` a lekérdezés predikátum mezőben, és majd kattintson a **szűrés**.

    ![Módosítsa az alapértelmezett lekérdezés ORDER BY: c._ts DESC hozzáadása, majd kattintson a szűrő alkalmazása](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

Ez módosítani a lekérdezés listák csökkenő sorrendben az időbélyegzőt, ezért most a második dokumentum alapján a dokumentumokat a lista tetején. Ha ismeri az SQL-szintaxis, megadhatja a támogatott bármelyikét [SQL-lekérdezések](documentdb-sql-query.md) ebben a mezőben. 

Ezzel befejezte a munkahelyi adatok Explorer. Ahhoz, hogy helyezze át kód használata, ügyeljen arra is használható adatkezelő tárolt eljárások, felhasználó által megadott függvények és eseményindítók hajtsa végre a kiszolgálóoldali üzleti logikát, valamint a méretezhető teljesítmény létrehozásához. Az Adatkezelő hozzáférhetővé teszi az API-k összes beépített, programozható adatelérési funkcióját, és az Azure Portalon tárolt adataihoz is egyszerű hozzáférést biztosít.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Klónozunk egy DocumentDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és használja a `cd` parancs futtatásával módosíthatja a mintaalkalmazás telepítése mappába. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ezzel a paranccsal létrejön egy mintaalkalmazás példányát a számítógépen.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező megadni. Ha most szeretné ismerni az a kód létrehozását az adatbázis-erőforrások, az alábbi kódtöredékek tekintheti meg. Kódtöredékek a rendszer az összes átveszi a `Program.java` fájl C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted mappába települ. Egyéb esetben ugorjon előre [frissítse a kapcsolati karakterlánc](#update-your-connection-string). 

* `DocumentClient`az inicializálás. A [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) biztosít az Azure Cosmos-adatbázis adatbázis-szolgáltatás ügyféloldali logikai ábrázolását. Ez az ügyfél konfigurálása és a szolgáltatás kérelmeket szolgál.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* [Adatbázis](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database) létrehozása.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection) létrehozása.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Dokumentum létrehozása használatával a [Documentclient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument) metódust.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* A JSON SQL lekérdezések végrehajtása használatával történik a [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments) metódust.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás a szolgáltatott adatbázissal való kommunikációhoz.

1. Az a [Azure-portálon](http://portal.azure.com/), kattintson a **kulcsok**. 

    A képernyő jobb oldalán lévő másolás gombok segítségével másolja a felső értékét, az URI.

    ![Megtekintése és másolása az Azure portál, kulcsok lapján hívóbetű](./media/create-documentdb-java/keys.png)

2. Nyissa meg a `Program.java` fájl C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted mappából. 

3. Illessze be az URI-azonosítóját, a portálon keresztül `https://FILLME.documents.azure.com` 45 sor.

4. Lépjen vissza a portálra, és másolja az elsődleges kulcs-érték, a képernyőfelvételen látható módon. Illessze be az elsődleges kulcs-érték a portálon keresztül `FILLME` 46 sor.

    A getStartedDemo metódus most ehhez hasonlóan kell kinéznie: 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Mentse a Program.java fájlt.

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen az azure-cosmos-db-documentdb-java-getting-started mappába.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. A git terminálablakába írja be az `mvn package` parancsot a szükséges Java-csomagok telepítéséhez.

3. A git-terminál ablakban futtassa `mvn exec:java -D exec.mainClass=GetStarted.Program` a Java-alkalmazás indításához.

    A terminálablakot, hogy létrejött-e a FamilyDB adatbázis értesítést jelenít meg. A gyűjtemény létrehozása billentyű lenyomásával, majd átváltása az adatkezelő, és látni fogja, hogy most már tartalmaz egy FamilyDB adatbázis.
    
    Továbbra is a dokumentumok létrehozásához, és végezze el a lekérdezés.
    
    A program végén az alkalmazásból az erőforrások a fiókhoz tartozó, a rendszer törli nem számítunk fel díjakat. 

    ![Konzolkimenet](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan hozhat létre Azure Cosmos DB-fiókot, dokumentum-adatbázist és gyűjteményt az Adatkezelő segítségével, valamint hogyan futtathat egy alkalmazást, amely programozottan hajtja végre ugyanezt. További adatok most importálása az Azure Cosmos DB-gyűjteménnyel. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)


