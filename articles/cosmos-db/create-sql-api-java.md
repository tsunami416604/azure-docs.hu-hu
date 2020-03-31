---
title: Rövid útmutató – A Java használatával dokumentum-adatbázist hozhat létre az Azure Cosmos DB használatával
description: Ez a rövid útmutató bemutatja a Java-kód minta segítségével csatlakozni, és lekérdezi az Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240176"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rövid útmutató: Java-alkalmazás létrehozása az Azure Cosmos DB SQL API-adatok kezeléséhez


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban hozzon létre és kezeljen egy Azure Cosmos DB SQL API-fiókot az Azure Portalon, és a GitHubról klónozott Java-alkalmazás használatával. Először hozzon létre egy Azure Cosmos DB SQL API-fiókot az Azure Portalon, majd hozzon létre egy Java-alkalmazást az SQL Java SDK használatával, majd adja hozzá az erőforrásokat a Cosmos DB-fiókhoz a Java alkalmazás használatával. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Az [Azure Cosmos DB emulátort](https://aka.ms/cosmosdb-emulator) is `https://localhost:8081` használhatja `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`a kulcs URI-jával és a kulccsal.
- [Java Fejlesztési Készlet (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Irányítsa a `JAVA_HOME` környezeti változót arra a mappára, ahol a JDK telepítve van.
- A [Maven bináris archívum](https://maven.apache.org/download.cgi). Az Ubuntu, `apt-get install maven` fuss telepíteni Maven.
- [Git](https://www.git-scm.com/downloads). Az Ubuntu, `sudo apt-get install git` fuss telepíteni Git.

## <a name="introductory-notes"></a>Bevezető megjegyzések

*A Cosmos DB-fiók struktúrája.* Az API-tól vagy a programozási nyelvtől függetlenül a Cosmos *DB-fiók* nulla vagy több *adatbázist*tartalmaz, az *adatbázis* (DB) nulla vagy több *tárolót,* a *tároló* pedig nulla vagy több elemet tartalmaz, amint az az alábbi ábrán látható:

![Azure Cosmos-fiókentitások](./media/databases-containers-items/cosmos-entities.png)

Az adatbázisokról, tárolókról és elemekről itt olvashat [bővebben.](databases-containers-items.md) Néhány fontos tulajdonság a tároló szintjén van definiálva, köztük a *kiosztott átviteli és* *partíciókulcs.* 

A kiosztott átviteli értéket olyan kérelemegységekben *(RT)mérik,* amelyek pénzbeli árral rendelkeznek, és jelentős meghatározó tényezőt jelentenek a számla működési költségében. A kiosztott átviteli sebességgel tárolónkénti részletességgel vagy adatbázisonkénti részletességgel választható ki, azonban általában a tárolószintű átviteli specifikáció ajánlott. Az átviteli forgalom kiépítéséről [itt](set-throughput.md) olvashat bővebben.

Az elemek egy Cosmos DB-tárolóba kerülnek, az adatbázis horizontálisan növekszik további tároló és számítási kérések kezeléséhez. A tárolási és számítási kapacitás különálló egységekben, más néven *partíciókban van hozzáadva,* és a dokumentumokban egy mezőt kell választania, hogy az legyen a partíciókulcs, amely minden dokumentumot partícióhoz rendel. A partíciók kezelésének módja az, hogy minden partícióhoz nagyjából egyenlő szelet et rendelnek a partíciókulcs-értékek tartományából; ezért azt tanácsoljuk, hogy válasszon egy partíciókulcsot, amely viszonylag véletlenszerű vagy egyenletesen elosztott. Ellenkező esetben egyes partíciók lényegesen több kérést *(forró partíciót)* fognak látni, míg más partíciók lényegesen kevesebb kérést *(hideg partíciót)* látnak, és ezt el kell kerülni. Lehet, hogy többet megtudni particionálás [itt](partitioning-overview.md).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy SQL API-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Tároló hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig váltsunk át kódok használatára. Klónozunk egy SQL API-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, és futtatjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben folytathatja [Az alkalmazás futtatása](#run-the-app) szakasszal. 

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Adatbázis-erőforrások kezelése a szinkron (szinkronizálási) API használatával

* `CosmosClient` inicializálás. Az `CosmosClient` ügyféloldali logikai ábrázolása az Azure Cosmos adatbázis-szolgáltatás. Ezzel az ügyféllel a szolgáltatásra irányuló kérések konfigurálhatók és hajthatók végre.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Elem létrehozása a `createItem` módszer rel.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* A pontolvasások `readItem` metódussal történnek.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* A JSON-on keresztüli `queryItems` SQL-lekérdezések a metódus használatával történnek.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Adatbázis-erőforrások kezelése az aszinkron (aszinkron) API-val

* Az Async API-hívások azonnal visszatérnek, anélkül, hogy megvárnák a kiszolgáló válaszát. Ennek fényében a következő kódrészletek megfelelő tervezési mintákat mutatnak az összes korábbi felügyeleti feladat elvégzéséhez az async API használatával.

* `CosmosAsyncClient` inicializálás. Az `CosmosAsyncClient` ügyféloldali logikai ábrázolása az Azure Cosmos adatbázis-szolgáltatás. Ez az ügyfél a szolgáltatással szembeni aszinkron kérelmek konfigurálására és végrehajtására szolgál.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`Létrehozása.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* A szinkronizálási API-hoz ugyanúgy, `createItem` mint a szinkronizálási API,elem létrehozása a módszer rel. Ez a példa bemutatja, hogyan `createItem` lehet hatékonyan kiadni számos aszinkron kérelmet a reaktív adatfolyamra való feliratkozással, amely kiadja a kéréseket, és kinyomtatja az értesítéseket. Mivel ez az egyszerű példa a `CountDownLatch` befejezésig fut és leáll, a program példányokkal biztosítja, hogy a program ne fejeződjön le a cikk létrehozása során. **A megfelelő aszinkron programozási gyakorlat nem blokkolja az aszinkron hívásokat - a reális használati esetekben kérelmek jönnek létre a main() hurok, amely végrehajtja a végtelenségig, így nincs szükség a retesz a async hívások.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* A szinkronizálási API-hoz ugyanúgy, mint a szinkronizálási API-hoz, a pontolvasások metódussal `readItem` történnek.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* A szinkronizálási API-hoz ugyanúgy, mint a `queryItems` szinkronizálási API,SQL-lekérdezések JSON-on keresztül a metódus használatával történik.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd indítsa el az alkalmazást a végpontadatokkal. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A git terminálablakában a `cd` paranccsal lépjen a mintakód mappájába.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. A git terminálablakában futtassa a következő parancsot a szükséges Java-csomagok telepítéséhez.

    ```bash
    mvn package
    ```

3. A git terminálablakban a következő paranccsal indítsa el a `sync` Java `async` alkalmazást (cserélje le a SYNCASYNCMODE alkalmazást a futtatni kívánt mintakódra, cserélje le YOUR_COSMOS_DB_HOSTNAME a portálon idézett URI-értékre, és cserélje le YOUR_COSMOS_DB_MASTER_KEY a portálon idézett elsődleges kulcsra)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A terminálablakban értesítést kap a FamilyDB adatbázis létrejöttéről. 
    
4. Az alkalmazás létrehoz egy adatbázist névvel`AzureSampleFamilyDB`
5. Az alkalmazás névvel rendelkező tárolót hoz létre`FamilyContainer`
6. Az alkalmazás végrehajtja pont olvasás oka objektumazonosítók és partíciókulcs értéke (amely lastName a mintában). 
7. Az alkalmazás lekérdezi az elemeket, hogy lekérje az összes vezetéknévvel rendelkező családot ('Andersen', 'Wakefield', 'Johnson')

7. Az alkalmazás nem törli a létrehozott erőforrásokat. A portálra visszaváltva [törölje az erőforrásokat](#clean-up-resources)  a fiókból a felmerülő költségek elkerüléséhez.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB SQL API-fiókot, hogyan hozhat létre egy dokumentum-adatbázist és -tárolót az Adatkezelő vel, és hogyan futtathatja a Java-alkalmazást, hogy programozott módon végezze el ugyanezt. Most már importálhat további adatokat az Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
