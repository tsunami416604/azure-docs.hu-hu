---
title: "Azure Cosmos DB Java-alkalmazás létrehozása a Graph API-val | Microsoft Docs"
description: "Egy Java-kódmintát mutat be, amellyel az Azure Cosmos DB-hez csatlakozhat, és a Gremlin használatával gráfadatokat kérdezhet le."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/27/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: d9619bd9a012a347634282788b3a318886967a3f
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="azure-cosmos-db-build-a-java-application-using-the-graph-api" class="xliff"></a>

# Azure Cosmos DB: Java-alkalmazás létrehozása a Graph API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével egy Graph API (előzetes verzió) alkalmazást használó Azure Cosmos DB-fiókot, dokumentum-adatbázist és gyűjteményt. Majd megtudhatja, hogyan hozhat létre és futtathat konzolalkalmazásokat az OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)-illesztőprogram használatával.  

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

* Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
   * JDK 1.7+ (futtassa az `apt-get install default-jdk` parancsot, ha nem rendelkezik a JDK-val), és állítsa be a környezeti változókat, például a `JAVA_HOME` változót
   * Maven (ha nem rendelkezik Maven-nel, futtassa az `apt-get install maven` parancsot)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

<a id="add-a-graph" class="xliff"></a>

## Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

Klónozzunk egy Graph API (előzetes verzió) alkalmazást a GitHub-ról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a minta tárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a `Program.java` fájlt és tekintse meg a kódsorokat. 

* A rendszer a Gremlin `Client` alkalmazást az `src/remote.yaml` fájlban megadott konfiguráció szerint inicializálja.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* A `client.submit` metódus használatával a program Gremlinnel kapcsolatos lépések sorozatát hajtja végre.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiók bal oldali oldalsávján kattintson a **Kulcsok** elemre, majd kattintson az **Írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot az `Program.java` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén](./media/create-graph-java/keys.png)

2. Nyissa meg az `src/remote.yaml` fájlt. 

3. Az `src/remote.yaml` fájlban adja meg a *host* (gazdagép), *Port*, *username* (felhasználónév), *password* (jelszó), *ConnectionPool* (kapcsolatkészlet), és *serializer* (szerializáló) beállításokat:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Hosts|***.graphs.azure.com|A gráfszolgáltatás URI-címe, amely az Azure Portalról kérhető le
    Port|443|Állítsa be a 443-as portot.
    Felhasználónév|*Az Ön felhasználóneve*|`/dbs/<db>/colls/<coll>` formátumú erőforrás
    Jelszó|*Az Ön elsődleges főkulcsa*|Az Ön elsődleges főkulcsa az Azure Cosmos DB rendszerhez
    ConnectionPool|{enableSsl: true}|Kapcsolatkészletének beállítása SSL használatához
    Serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Ezt az értéket adja meg.

<a id="run-the-console-app" class="xliff"></a>

## A konzolalkalmazás futtatása

1. Futtassa az `mvn package` parancsot egy terminálban a szükséges Java-csomagok telepítéséhez.

2. Futtassa a `mvn exec:java -D exec.mainClass=GetStarted.Program` parancsot egy terminálban a Java-alkalmazás elindításához.

Lépjen vissza az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, valamint dolgozhat azokkal. 

<a id="browse-using-the-data-explorer" class="xliff"></a>

## Tallózás az Adatkezelővel

Ezután visszaléphet az Adatkezelőbe az Azure Portalon, ahol tallózhatja és lekérdezheti az új gráfadatokat.

* Az új adatbázis az Adatkezelőben a Gyűjtemények ablaktáblán jelenik meg. Bontsa ki a **graphdb**, **graphcoll** pontokat, és kattintson a **Gráf** lehetőségre.

    A mintaalkalmazás által létrehozott adatokat a Gráfok ablaktáblán találja.

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon: 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gráfot készíteni, és hogyan lehet futtatni az alkalmazást. Most már készen áll arra, hogy a Gremlin használatával összetettebb lekérdezéseket hozzon létre és hatékony gráfbejárási logikákat implementáljon. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)


