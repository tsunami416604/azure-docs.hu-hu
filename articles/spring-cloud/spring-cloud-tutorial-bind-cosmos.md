---
title: Oktatóanyag – Azure Cosmos DB kötése az Azure Spring Cloud-alkalmazáshoz
description: Ebből az oktatóanyagból megtudhatja, hogyan köthető Azure Cosmos DB az Azure Spring Cloud-alkalmazáshoz
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 8eeb67419d2da90ff1e2d2beb8cb59a85c3bbacb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461617"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Azure Cosmos DB-adatbázis kötése az Azure Spring Cloud-alkalmazáshoz

A Spring boot-alkalmazások manuális konfigurálása helyett az Azure Spring Cloud használatával automatikusan köthető az Azure-szolgáltatások kiválasztásához az alkalmazásaihoz. Ez a cikk bemutatja, hogyan köthető az alkalmazás egy Azure Cosmos DB adatbázishoz.

Előfeltételek:

* Egy üzembe helyezett Azure Spring Cloud-példány. Az első lépésekhez kövesse [Az Azure CLI-n keresztül történő üzembe helyezést ismertető](spring-cloud-quickstart-launch-app-cli.md) útmutatót.
* Egy Azure Cosmos DB fiók, amelynek a minimális jogosultsági szintje közreműködő.

## <a name="bind-azure-cosmos-db"></a>Kötés Azure Cosmos DB

Azure Cosmos DB öt különböző API-típust támogat, amelyek támogatják a kötést. A következő eljárás azt szemlélteti, hogyan használhatók:

1. Létrehoz egy Azure Cosmos DB-adatbázist. Az [adatbázis létrehozásával](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) kapcsolatos útmutatót a súgóban találja. 

1. Jegyezze fel az adatbázis nevét. Ehhez az eljáráshoz az adatbázis neve **testdb**.

1. Adja hozzá az alábbi függőségek egyikét az Azure Spring Cloud Application Pom. XML fájljához. Válassza ki az API-típusnak megfelelő függőséget.

    * API típusa: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API típusa: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API-típus: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API-típus: Gremlin (gráf)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API-típus: Azure-tábla

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. A `az spring-cloud app update` használatával frissítse az aktuális telepítést, vagy a `az spring-cloud app deployment create` használatával hozzon létre egy új központi telepítést. Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Nyissa meg az **alkalmazás irányítópultját** , és válassza ki a Azure Cosmos DBhoz kötni kívánt alkalmazást. Ez az alkalmazás ugyanaz, mint amelyet az előző lépésben frissített vagy telepített.

1. Válassza ki a **szolgáltatási kötés**elemet, majd válassza a **szolgáltatás kötésének létrehozása**lehetőséget. Az űrlap kitöltéséhez válassza a következő lehetőséget:
   * A **kötési típus** értéke **Azure Cosmos db**.
   * Az API-típus.
   * Az adatbázis neve.
   * A Azure Cosmos DB fiók.

    > [!NOTE]
    > Ha Cassandra-t használ, az adatbázis nevének egyik kulcsát kell használnia.

1. Indítsa újra az alkalmazást az alkalmazás lapon az **Újraindítás** lehetőség kiválasztásával.

1. A szolgáltatás megfelelő kötésének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek ehhez a példához hasonlónak kell lennie:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás egy Azure Cosmos DB-adatbázishoz. Ha szeretné megtudni, hogyan köthető az alkalmazás egy Azure cache-hez a Redis cache-gyorsítótárhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útmutató az Azure cache Redis cache-hez való kötéséhez](spring-cloud-tutorial-bind-redis.md)
