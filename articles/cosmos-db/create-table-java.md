---
title: "Gyors üzembe helyezés: Tábla API-t a Java - Azure Cosmos DB |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan hozzon létre egy alkalmazást az Azure portál és a Java az Azure Cosmos DB tábla API használatával"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: arramac
ms.openlocfilehash: 90ba10990049cd1fb788d63a143eb1169191cf24
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="quickstart-build-a-table-api-app-with-java-and-azure-cosmos-db"></a>Gyors üzembe helyezés: Egy tábla a Java és Azure Cosmos DB API-alkalmazás létrehozása

A gyors üzembe helyezés bemutatja, hogyan használja a Java és az Azure Cosmos DB [tábla API](table-introduction.md) egy példa a Githubról klónozásával az alkalmazás elkészítésére. A gyors üzembe helyezés is bemutatja, hogyan Azure Cosmos DB-fiók létrehozása és adatok kezelővel létrehozása a táblákat és entitásokat a web-alapú Azure-portálon.

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

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

> [!IMPORTANT] 
> Szeretne együttműködni a általánosan elérhető tábla API SDK-k új tábla API-fiók létrehozása. Tábla API fiókjainak előzetes nem támogatottak az általánosan elérhető az SDK-k.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

Az Adatkezelő segítségével adatokat adhat hozzá az új táblához.

1. Az Adatkezelőben bontsa ki a **minta tábla** pontot, és kattintson az **Entitások**, ezután pedig az **Entitás hozzáadása** lehetőségre.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Most adatok hozzáadása a PartitionKey érték mezőhöz, illetve RowKey értéket, és kattintson a **entitás hozzáadása**.

   ![A partíciókulcs és a sorkulcs beállítása új entitások számára](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Most már az Adatkezelővel további entitásokat is hozzá tud adni a táblához, szerkesztheti őket, és lekérdezéseket is indíthat. Az Adatkezelőben továbbá skálázhatja az átviteli sebességet, és tárolt eljárásokat, felhasználói függvényeket és triggereket adhat hozzá a táblához.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Látni fogja, milyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és használja a `cd` parancs futtatásával módosíthatja a mintaalkalmazás telepítése mappába. 

    ```bash
    cd "C:\git-samples"
    ```

2. Futtassa a következő parancsot a minta tárház klónozásához. Ezzel a paranccsal létrejön egy mintaalkalmazás példányát a számítógépen. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás a szolgáltatott adatbázissal való kommunikációhoz. 

1. Az a [Azure-portálon](http://portal.azure.com/), kattintson a **kapcsolati karakterlánc**. 

   ![Megtekintése és másolása a szükséges kapcsolati karakterlánc adatait a a kapcsolati karakterlánc panelen](./media/create-table-java/connection-string.png)

2. Másolja az elsődleges KAPCSOLATI KARAKTERLÁNCOT a Másolás gombra a jobb oldalon.

3. Nyissa meg a config.properties a C:\git-samples\storage-table-java-getting-started\src\main\resources mappából. 

5. Egy sor megjegyzéssé, és állítsa vissza a két sort. Az első két sort kell most kinéznie.

    ```
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Az elsődleges KAPCSOLATI KARAKTERLÁNCOT a portálról illessze be a StorageConnectionString érték a 2. sor. 

    > [!IMPORTANT]
    > Ha a végpont által használt documents.azure.com, amely azt jelenti, hogy előzetes fiókkal rendelkezik, és kell létrehoznia egy [új tábla API-fiók](#create-a-database-account) működéséhez az általánosan elérhető tábla API SDK-val.
    >

7. Mentse a config.properties fájlt.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A git terminálablakot `cd` a storage-tábla-java--bevezető mappába.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. A git terminálablakot, futtassa a következő parancsok futtatásával kiindulási pont a Java-alkalmazást.

    ```git
    mvn compile exec:java 
    ```

    A konzolablakban a felvenni kívánt Azure Cosmos DB-ben az új táblázat adatbázis adatait jeleníti meg.

    Lépjen vissza az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, valamint dolgozhat azokkal. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével táblát készíteni, és hogyan lehet futtatni az alkalmazást.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [A tábla API tábla adatok importálása](table-import.md)
