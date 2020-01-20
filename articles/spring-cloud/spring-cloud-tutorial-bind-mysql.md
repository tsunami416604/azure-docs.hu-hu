---
title: Oktatóanyag – Azure Database for MySQL-példány kötése az Azure Spring Cloud-alkalmazáshoz
description: Ez az oktatóanyag bemutatja, hogyan köthető egy Azure Database for MySQL-példány az Azure Spring Cloud-alkalmazáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277538"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Oktatóanyag: Azure Database for MySQL-példány kötése az Azure Spring Cloud-alkalmazáshoz 

Az Azure Spring Cloud-ban az Azure-szolgáltatások automatikus kiválasztását is elvégezheti az alkalmazásaiban, ahelyett, hogy a Spring boot-alkalmazást manuálisan kellene konfigurálnia. Ez az oktatóanyag bemutatja, hogyan köthető az alkalmazás a Azure Database for MySQL-példányhoz.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Egy Azure Database for MySQL fiók
* Azure parancssori felület (CLI)

Ha nem rendelkezik telepített Azure Spring Cloud-példánnyal, kövesse a gyors útmutató [: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart-launch-app-portal.md) az első rugós felhőalapú alkalmazás üzembe helyezéséhez.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Az alkalmazás kötése az Azure Database for MySQL-példányhoz

1. Jegyezze fel a Azure Database for MySQL fiókjának rendszergazdai felhasználónevét és jelszavát. 

1. Kapcsolódjon a kiszolgálóhoz, hozzon létre egy **testdb** nevű adatbázist egy MySQL-ügyfélről, majd hozzon létre egy új, nem rendszergazdai fiókot.

1. A projekt *Pom. XML* fájljában adja hozzá a következő függőséget:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Az *Application. properties* fájlban távolítsa el a `spring.datasource.*` tulajdonságokat.

1. Frissítse az aktuális telepítést `az spring-cloud app update`futtatásával, vagy hozzon létre egy új központi telepítést a módosításhoz a `az spring-cloud app deployment create`futtatásával.  Ezek a parancsok vagy az új függőséggel frissítik vagy létrehozzák az alkalmazást.

1. A Azure Portal az **Azure Spring Cloud** Service oldalon keresse meg az **alkalmazás irányítópultját**, majd válassza ki az alkalmazást, amelyet a Azure Database for MySQL-példányhoz szeretne kötni.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. 

1. Válassza ki a **szolgáltatási kötés**elemet, majd kattintson a **szolgáltatás kötésének létrehozása** gombra. 

1. Töltse ki az űrlapot, és válassza az **Azure MySQL** -t a **kötés típusaként**, és használja ugyanazt az adatbázisnevet, amelyet korábban használt, és használja ugyanazt a felhasználónevet és jelszót, amelyet az első lépésben feljegyzett.

1. Indítsa újra az alkalmazást, és ez a kötés most már működnie kell.

1. A szolgáltatási kötés helyességének biztosításához jelölje ki a kötés nevét, majd ellenőrizze a részleteit. A `property` mezőnek így kell kinéznie:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan köthető az Azure Spring Cloud-alkalmazás egy Azure Database for MySQL-példányhoz.  Ha többet szeretne megtudni az Azure Spring Cloud-szolgáltatás kezeléséről, tekintse meg a szolgáltatás felderítésével és regisztrálásával foglalkozó cikket.

> [!div class="nextstepaction"]
> [A Service Discovery és a regisztráció engedélyezése a Spring Cloud Service-beállításjegyzék használatával](spring-cloud-service-registration.md)
