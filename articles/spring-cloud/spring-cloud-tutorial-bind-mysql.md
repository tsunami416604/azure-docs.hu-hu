---
title: Az Azure Spring Cloud-alkalmazáshoz való Azure Database for MySQL kötése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan köthető az Azure MySQL az Azure Spring Cloud-alkalmazáshoz
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607588"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Oktatóanyag: Azure-szolgáltatások kötése Azure Spring Cloud-alkalmazáshoz: Azure Database for MySQL

Az Azure Spring Cloud lehetővé teszi, hogy az Azure-szolgáltatásokat automatikusan, a Spring boot-alkalmazás manuális konfigurálása helyett az alkalmazásokhoz kösse. Ez az oktatóanyag bemutatja, hogyan köthető az alkalmazás az Azure MySQL-hez.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Egy Azure Database for MySQL fiók
* Azure CLI

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse az ebben a rövid útmutatóban ismertetett lépéseket az első Spring [Cloud-alkalmazás](spring-cloud-quickstart-launch-app-portal.md) üzembe helyezéséhez.

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

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára. Keresse meg az **alkalmazás irányítópultját** , és válassza ki az alkalmazást, amely az Azure MySQL-hez kötődik.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. Ezután válassza a `Service binding` elemet, és válassza a `Create service binding` gombot. Töltse ki az űrlapot, és ügyeljen arra, hogy válassza a **kötés típusa** `Azure MySQL`, a korábban használt adatbázis nevét, valamint az első lépésben feljegyzett felhasználónevet és jelszót.

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

