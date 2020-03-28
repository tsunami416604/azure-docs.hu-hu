---
title: Oktatóanyag – Egy Azure Cosmos DB kötése az Azure Spring Cloud-alkalmazáshoz
description: Ebből az oktatóanyagból megtudhatja, hogyan kötheti össze az Azure Cosmos DB-t az Azure Spring Cloud-alkalmazással
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277568"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Azure Cosmos DB-adatbázis kötése az Azure Spring Cloud-alkalmazáshoz

Ahelyett, hogy manuálisan konfigurálna a tavaszi rendszerindítási alkalmazásokat, automatikusan összekötheti a kiválasztott Azure-szolgáltatásokat az alkalmazásokhoz az Azure Spring Cloud használatával. Ez a cikk bemutatja, hogyan kötheti az alkalmazást egy Azure Cosmos DB-adatbázishoz.

Előfeltételek:

* Üzembe helyezett Azure Spring Cloud-példány. Kövesse [az Azure CLI-n keresztül történő üzembe helyezés rövid útmutatóját az](spring-cloud-quickstart-launch-app-cli.md) első lépésekhez.
* Azure Cosmos DB-fiók, amely nek legalább a közreműködői jogosultsági szintje van.

## <a name="bind-azure-cosmos-db"></a>Az Azure Cosmos DB kötése

Az Azure Cosmos DB öt különböző API-típussal rendelkezik, amelyek támogatják a kötést. Az alábbi eljárás bemutatja, hogyan kell használni őket:

1. Létrehoz egy Azure Cosmos DB-adatbázist. A súgóért tekintse meg az [adatbázis létrehozásának](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) rövid útmutatóját. 

1. Rögzítse az adatbázis nevét. Ehhez az eljáráshoz az adatbázis neve **testdb**.

1. Adja hozzá az alábbi függőségek egyikét az Azure Spring Cloud-alkalmazás pom.xml fájljába. Válassza ki az API-típusnak megfelelő függőséget.

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

    * API típusa: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API típusa: Gremlin (grafikon)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API típusa: Azure Table

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Az `az spring-cloud app update` aktuális központi telepítés frissítéséhez vagy új központi telepítés létrehozásához használható. `az spring-cloud app deployment create` Ezek a parancsok vagy frissítik vagy létrehozzák az alkalmazást az új függéssel.

1. Nyissa meg az Azure Spring Cloud szolgáltatáslapját az Azure Portalon. Nyissa meg az **Alkalmazásirányítópultot,** és válassza ki az Azure Cosmos DB-hez kötéshez kívánt alkalmazást. Ez az alkalmazás megegyezik az előző lépésben frissített vagy telepített alkalmazással.

1. Válassza a **Szolgáltatáskötés**lehetőséget, majd a **Szolgáltatáskötés létrehozása**lehetőséget. Az űrlap kitöltéséhez válassza a következőket:
   * A **Kötés típus** értéke Az Azure **Cosmos DB.**
   * Az API típusa.
   * Az adatbázis neve.
   * Az Azure Cosmos DB-fiókja.

    > [!NOTE]
    > Cassandra használata esetén használjon kulcsterületet az adatbázis nevéhez.

1. Indítsa újra az alkalmazást az Alkalmazás lapján az **Újraindítás** gombra kattintva.

1. A szolgáltatás megfelelő kötésének biztosításához jelölje ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek hasonlónak kell lennie ehhez a példához:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kötheti össze az Azure Spring Cloud-alkalmazást egy Azure Cosmos DB-adatbázishoz. Ha meg szeretné tudni, hogyan kötheti össze az alkalmazást egy Azure-gyorsítótár redis-gyorsítótárhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan köthető kontinarás egy Azure-gyorsítótárhoz a Redis-gyorsítótárhoz](spring-cloud-tutorial-bind-redis.md)
