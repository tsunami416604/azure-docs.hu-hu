---
title: Azure cache kötése az Azure Spring Cloud-alkalmazáshoz való Redis | Microsoft Docs
description: Ismerje meg, hogyan köthető az Azure cache a Redis Azure Spring Cloud-alkalmazásához
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038943"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Oktatóanyag: Azure-szolgáltatások kötése az Azure Spring Cloud-alkalmazásban: Azure Cache for Redis

Az Azure Spring Cloud lehetővé teszi, hogy az Azure-szolgáltatásokat automatikusan, a Spring boot-alkalmazás manuális konfigurálása helyett az alkalmazásokhoz kösse. Ez a cikk bemutatja, hogyan köthető az alkalmazás az Azure cache-hez a Redis.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure cache a Redis Service-példányhoz
* Azure Spring Cloud-bővítmény az Azure CLI-hez

Ha szükséges, telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="bind-azure-cache-for-redis"></a>Az Azure cache kötése a Redis-hez

1. Adja hozzá a következő függőséget a projekt `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Távolítsa el a `spring.redis.*` tulajdonságokat, ha van ilyen, a `application.properties` fájlban

1. Frissítse az aktuális telepítést `az spring-cloud app update` használatával, vagy hozzon létre egy új központi telepítést a `az spring-cloud app deployment create` használatával.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Keresse meg az **alkalmazás irányítópultját** , és válassza ki azt az alkalmazást, amely a Redis készült Azure cache-hez kötődik.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. Ezután válassza a `Service binding` elemet, és válassza a `Create service binding` gombot. Töltse ki az űrlapot, és ügyeljen arra, hogy a **kötés típusát** `Azure Cache for Redis`, a Redis-kiszolgáló és az elsődleges kulcs beállítást válassza. 

1. Indítsa újra az alkalmazást, és ez a kötés most működik.

1. A szolgáltatási kötés helyességének biztosításához válassza ki a kötés nevét, és ellenőrizze annak részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás egy Azure Redis cache-hez.  Ha többet szeretne megtudni az alkalmazásra vonatkozó kötési szolgáltatásokról, folytassa az alkalmazás egy MySQL-ADATBÁZIShoz való kötésével kapcsolatos oktatóanyagot.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan köthető egy Azure MySQL-szolgáltatás az Azure Spring Cloud Service-](spring-cloud-tutorial-bind-mysql.md)hez.