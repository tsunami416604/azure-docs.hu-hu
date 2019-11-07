---
title: Azure Cosmos DB kötése az Azure Spring Cloud-alkalmazáshoz | Microsoft Docs
description: Ismerje meg, hogyan köthető Azure Cosmos DB az Azure Spring Cloud-alkalmazáshoz
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607135"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Oktatóanyag: Azure Cosmos DB kötése az Azure Spring Cloud-alkalmazáshoz

Az Azure Spring Cloud lehetővé teszi, hogy az Azure-szolgáltatásokat automatikusan, a Spring boot-alkalmazás manuális konfigurálása helyett az alkalmazásokhoz kösse. Ez a cikk bemutatja, hogyan köthető az alkalmazás egy Azure Cosmos DBhoz.

Előfeltételek:
* Egy üzembe helyezett Azure Spring Cloud-példány.  [A kezdéshez kövesse a gyors](spring-cloud-quickstart-launch-app-cli.md) üzembe helyezési útmutatót.
* Egy Azure Cosmos DB fiók, amelynek a minimális jogosultsági szintje közreműködő.

## <a name="bind-azure-cosmos-db"></a>Kötés Azure Cosmos DB

Azure Cosmos DB öt különböző API-típussal rendelkezik, amelyek támogatják a kötést:

1. Létrehoz egy Azure Cosmos DB-adatbázist. Az adatbázis létrehozásával kapcsolatos segítségért [tekintse meg ezt a cikket](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . Jegyezze fel az adatbázis nevét. A miénk neve `testdb`.

1. Adja hozzá a következő függőségek egyikét a Spring Cloud Application `pom.xml` értékhez az API-típusnak megfelelően.
    
    #### <a name="api-type-core-sql"></a>API típusa: Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API típusa: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API-típus: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API-típus: Gremlin (gráf)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API-típus: Azure-tábla

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Frissítse az aktuális telepítést `az spring-cloud app update` használatával, vagy hozzon létre egy új központi telepítést ehhez a változáshoz a `az spring-cloud app deployment create` használatával.  Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. Keresse meg az **alkalmazás irányítópultját** , és válassza ki a Cosmos DBhoz kötni kívánt alkalmazást. Ezután válassza a `Service binding` elemet, és válassza a `Create service binding` gombot. Töltse ki az űrlapot, és válassza a **kötési típus** `Azure Cosmos DB`, az API-típus, az adatbázis neve és a Azure Cosmos db-fiók lehetőséget.

    > [!NOTE]
    > Ha Cassandra-t használ, használjon egy kulcsot az adatbázis neveként.

1. Indítsa újra az alkalmazást az alkalmazás oldalán található **Újraindítás** gombra kattintva.

1. A szolgáltatás megfelelő kötésének biztosításához válassza ki a kötési nevet, és ellenőrizze annak részleteit. A `property` mezőben a következőhöz hasonlónak kell lennie:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás egy CosmosDB.  Ha meg szeretné tudni, hogyan köthető az alkalmazás egy Azure Redis Cachehoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Azure Spring Cloud-alkalmazás kötése egy Azure Redis Cachehoz](spring-cloud-tutorial-bind-redis.md).
