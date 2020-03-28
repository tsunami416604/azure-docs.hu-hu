---
title: Oktatóanyag – Egy Azure-adatbázis MySQL-példány kötése az Azure Spring Cloud-alkalmazáshoz
description: Ez az oktatóanyag bemutatja, hogyan köthető össze egy Azure-adatbázis a MySQL-példányhoz az Azure Spring Cloud-alkalmazáshoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277538"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Oktatóanyag: Azure-adatbázis kötése a MySQL-példányhoz az Azure Spring Cloud-alkalmazáshoz 

Az Azure Spring Cloud segítségével a kiválasztott Azure-szolgáltatásokat automatikusan az alkalmazásokhoz kötheti, ahelyett, hogy manuálisan kellene konfigurálnia a tavaszi rendszerindítási alkalmazást. Ez az oktatóanyag bemutatja, hogyan kötheti össze az alkalmazást az Azure Database for MySQL-példányhoz.

## <a name="prerequisites"></a>Előfeltételek

* Üzembe helyezett Azure Spring Cloud-példány
* Azure-adatbázis a MySQL-fiókhoz
* Azure CLI

Ha nem rendelkezik üzembe helyezett Azure Spring Cloud-példával, kövesse a [rövid útmutató utasításait: Indítson el egy Azure Spring Cloud-alkalmazást az Azure Portalon](spring-cloud-quickstart-launch-app-portal.md) az első Tavaszi Felhőalkalmazás üzembe helyezéséhez.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Az alkalmazás kötése az Azure Database for MySQL-példányhoz

1. Vegye figyelembe az Azure Database for MySQL-fiók rendszergazdai felhasználónevét és jelszavát. 

1. Csatlakozzon a kiszolgálóhoz, hozzon létre egy **testdb** nevű adatbázist egy MySQL-ügyfélből, majd hozzon létre egy új, nem rendszergazdai fiókot.

1. A projekt *pom.xml* fájljában adja hozzá a következő függőséget:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Az *application.properties* fájlban `spring.datasource.*` távolítson el minden tulajdonságot.

1. Frissítse az aktuális `az spring-cloud app update`központi telepítést a futtatásával, `az spring-cloud app deployment create`vagy hozzon létre egy új központi telepítést a módosításhoz a futtatásával.  Ezek a parancsok vagy frissítik vagy létrehozzák az alkalmazást az új függéssel.

1. Az Azure Portalon az **Azure Spring Cloud** szolgáltatáslapján keresse meg az alkalmazás **irányítópultját,** majd válassza ki az azure-adatbázishoz a MySQL-példányhoz kötéshez kívánt alkalmazást.  Ez ugyanaz az alkalmazás, amelyet az előző lépésben frissített vagy telepített. 

1. Válassza **a Szolgáltatáskötés**lehetőséget, majd kattintson a **Szolgáltatáskötés létrehozása** gombra. 

1. Töltse ki az űrlapot, válassza az **Azure MySQL** **a kötés típusa,** ugyanazt az adatbázisnevet használja, amelyet korábban használt, és ugyanazt a felhasználónevet és jelszót, amelyet az első lépésben megjegyezte.

1. Indítsa újra az alkalmazást, és ennek a kötésnek működnie kell.

1. A szolgáltatáskötés helyességének ellenőrzéséhez jelölje ki a kötés nevét, majd ellenőrizze annak részleteit. A `property` mező nek így kell kinéznie:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kötheti össze az Azure Spring Cloud-alkalmazást egy Azure Database for MySQL-példányhoz.  Ha többet szeretne megtudni az Azure Spring Cloud-szolgáltatás kezeléséről, olvassa el a szolgáltatásfelderítésről és -regisztrációról szóló cikket.

> [!div class="nextstepaction"]
> [Szolgáltatásfelderítés és -regisztráció engedélyezése a Spring Cloud Service Registry használatával](spring-cloud-service-registration.md)
