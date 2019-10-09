---
title: Az Azure Spring Cloud-alkalmazáshoz való Azure Database for MySQL kötése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan köthető az Azure MySQL az Azure Spring Cloud-alkalmazáshoz
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039020"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Oktatóanyag: Azure-szolgáltatások kötése az Azure Spring Cloud-alkalmazásban: Azure Database for MySQL

Az Azure Spring Cloud lehetővé teszi, hogy az Azure-szolgáltatásokat automatikusan, a Spring boot-alkalmazás manuális konfigurálása helyett az alkalmazásokhoz kösse. Ez az oktatóanyag bemutatja, hogyan köthető az alkalmazás az Azure MySQL-hez.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Egy Azure Database for MySQL fiók
* Azure CLI

Ha szükséges, telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="bind-azure-database-for-mysql"></a>Kötés Azure Database for MySQL

1. Jegyezze fel az Azure MySQL-fiókjának rendszergazdai felhasználónevét és jelszavát. Kapcsolódjon a kiszolgálóhoz, és hozzon létre egy `testdb` nevű adatbázist egy MySQL-ügyfélről. Hozzon létre egy új, nem rendszergazdai fiókot.

1. Adja hozzá a következő függőséget a projekt `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Távolítsa el a `spring.datasource.*` tulajdonságokat, ha van ilyen, a `application.properties` fájlban.

1. Frissítse az aktuális telepítést `az spring-cloud app update` használatával, vagy hozzon létre egy új központi telepítést ehhez a változáshoz a `az spring-cloud app deployment create` használatával.  Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Keresse meg az **alkalmazás irányítópultját** , és válassza ki az alkalmazást, amely az Azure MySQL-hez kötődik.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. Ezután válassza a `Service binding` elemet, és válassza a `Create service binding` gombot. Töltse ki az űrlapot, és ügyeljen arra, hogy válassza ki a `Azure MySQL` **kötési típust** , a korábban használt adatbázisnevet, valamint ugyanazt a felhasználónevet és jelszót, amelyet az első lépésben feljegyzett.

1. Indítsa újra az alkalmazást, és ezt a kötést most már működnie kell.

1. A szolgáltatási kötés helyességének biztosításához válassza ki a kötési nevet, és ellenőrizze annak részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogy az Azure Spring Cloud-alkalmazást egy MySQL-ADATBÁZIShoz kösse.  Ha többet szeretne megtudni az Azure Spring Cloud-szolgáltatás kezeléséről, olvassa el a következő témakört: a szolgáltatások felderítésének és regisztrálásának megismerése.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan engedélyezheti a szolgáltatás-felderítést és-regisztrációkat a Spring Cloud Service-beállításjegyzék használatával](spring-cloud-service-registration.md).

