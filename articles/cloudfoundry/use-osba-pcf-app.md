---
title: Az Open Service Broker használata egy alkalmazás és a Pivotal Cloud Foundry az Azure-adatbázis
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/18/2019
ms.topic: tutorial
description: Ismerteti az Open Service Broker az Azure-adatbázishoz használt a Pivotal Cloud Foundry-alkalmazás konfigurálása
keywords: A Pivotal Cloud Foundry-hoz, a Cloud Foundry, nyissa meg a Service Brokert, az Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: cbaf1bc65950abb88630e90a62c4c1f5a8d3e7ec
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244086"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Oktatóanyag: Az Open Service Broker használata egy alkalmazás és a Pivotal Cloud Foundry az Azure-adatbázis

Az Open Service Broker for Azure a Pivotal Cloud Foundry példánnyal lehetővé teszi kiépítése szolgáltatásokhoz, például adatbázisok, az Azure-ban közvetlenül a Cloud Foundry parancssori felület és a Pivotal Cloud Foundry-példány. Emellett a Pivotal Cloud Foundry-példányban futó alkalmazásokhoz való hozhasson létre ezeket a szolgáltatásokat. Ha egy alkalmazás egy ilyen módon kötést létrehozni, nem kell frissíteni a kódok vagy a konfiguráció az alkalmazáson belül. Ez a cikk bemutatja, hogyan szeretné használni az Open Service Broker egy adatbázis-alkalmazásokhoz a Pivotal Cloud Foundry az Azure szolgáltatáshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő a Pivotal Cloud Foundry az alkalmazás tárhely.
> * Klónozza a minta alkalmazás forrásának a Githubról.
> * Az alkalmazás előkészítése az üzembe helyezés.
> * Telepítse az alkalmazást.
> * Hozzon létre egy adatbázist az Azure Open Service Broker használatával.
> * Az alkalmazás kötést létrehozni az adatbázist.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, tegye a következőket:

* [A Pivotal Cloud Foundry telepítve és konfigurálva van](create-cloud-foundry-on-azure.md)
* [Rendelkezik Open Service Broker for Azure telepített és konfigurált](https://network.pivotal.io/products/azure-open-service-broker-pcf) a Cloud Foundry-példánnyal

Íme egy példa az Open Service Broker for Azure csempe telepítette és konfigurálta a Pivotal Cloud Foundry az Ops Manager képernyőjén:

![A Pivotal Cloud Foundry az Open Service Broker for Azure telepítése](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>A Pivotal Cloud Foundry alkalmazás tárhelyének előkészítése

Be kell jelentkeznie a következővel kell üzembe helyezni a Pivotal Cloud Foundry-példány, a `cf` parancssori eszköz. Emellett rendelkeznie kell a kívánt szervezet és a megcélzott terület.

Jelentkezett be, és célozza meg a terület megjelenítése ellenőrzéséhez használja a `cf target`. Az alábbi példa bemutatja a felhasználó már bejelentkezett *rendszergazdai*révén a *myorg* szervezet és a célzás a *fejlesztési* terület:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Jelentkezzen be, használja a `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Hozzon létre egy új szervezetben és a hely, használjon `cf create-org` és `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Célként egy szóközt, használja a `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Ahhoz, hogy az alkalmazás Open Service Broker for Azure az alkalmazást kell használnia, adatforrás, például egy adatbázisba. Ebben a cikkben azt fogja használni a [zene mintaalkalmazás spring Cloud Foundry a](https://github.com/cloudfoundry-samples/spring-music) datasource használó alkalmazás bemutatása.

Klónozza az alkalmazást a Githubról, és lépjen abba a könyvtárba:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> A méretszámítás az alkalmazás megtekintéséhez nyissa meg a [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) fájlt.


## <a name="prepare-the-application-for-deployment"></a>Az alkalmazás előkészítése az üzembe helyezés

A Pivotal Cloud Foundry-példányhoz az alkalmazás telepítése előtt kell építse fel. Demonstrative célra, azt is lehetővé néhány *DEBUG* naplózás, amely az adatforrás kapcsolati információkat.

Ahhoz, hogy a *DEBUG* adatbázis-kapcsolat adatainak naplózása, adja hozzá a végéhez yaml tulajdonság alatt *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

A mintaalkalmazás hozhat létre az alkalmazást egy Spring Boot futtatható jar gradle-t használja. A Pivotal Cloud Foundry-példány telepítve lesz futtatható fájlt. Az alkalmazás buildeléséhez:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Az alkalmazás üzembe helyezése

Használja a `cf push` parancsot az alkalmazás a Pivotal Cloud Foundry-példány üzembe helyezéséhez:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Másolja az értéket a *útvonalak* jelenik meg, a kimenő `cf push`. Az útvonal a futó alkalmazás eléréséhez használandó URL-címe. Példa:

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Az alkalmazás üzembe helyezése után megtekintheti az alkalmazásnaplókat a kapcsolatot az alkalmazás által használt URL-cím. Az alábbi parancs megjeleníti az alkalmazásnaplókat, és használja `grep` kereséséhez a *jdbcUrl* konfigurációja.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Figyelje meg, hogy az alkalmazás által használt *h2:mem:testdb*, azaz a memóriában lévő adatbázishoz. Egy memórián belüli adatbázis használatát, ha az memóriában lévő adatbázishoz függ az osztályútvonal automatikusan konfigurálva a Spring-alkalmazás és [automatikus konfiguráció](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) engedélyezve van. A mintaalkalmazás rendelkezik a [h2 memóriában lévő adatbázishoz függőségi konfigurált](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) és engedélyezve van az automatikus konfiguráció [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Az alkalmazás útvonal használatával navigáljon a böngészőben. Az útvonalat vagy URL-CÍMÉT, megjelenik a kimenete a `cf push` parancsot.

> [!TIP]
> Emellett megjeleníthetők az alkalmazás URL-cím futtatásával `cf apps`.

Keresse meg az alkalmazást a böngésző használatával, miután dolgozzon vele törlésével néhány meglévő albumokat és néhány újakat hozna létre. A mintaalkalmazás a memóriában lévő adatbázist használ a módosítások mentéséhez. Is megfigyelheti az alkalmazás egyes feltöltődtek [alapértelmezett adatok](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Spring Music alkalmazás alapértelmezett adatokkal.](media/music-app.png)

Mivel az alkalmazás egy memórián belüli adatbázist használja, a módosítások nem maradnak, ha az alkalmazás újraindítása vagy újratelepítése. Megjelenítéséhez, hogy módosításokat nem rögzíti, néhány módosítás végrehajtása után, az alkalmazást a restage `cf restage`:

```cmd
cf restage spring-music
```

Miután az alkalmazás rendelkezik lett zített, keresse meg a fájlt az azonos URL-cím használatával. Figyelje meg, hogy a végzett módosítások elvesznek, és az alapértelmezett adatai jelennek meg.

![Spring Music alkalmazás alapértelmezett adatokkal.](media/music-app.png)

## <a name="create-a-database"></a>Adatbázis létrehozása

Az Azure-ban az Open Service Broker állandó adatbázis létrehozásához használja a `cf create-service` parancsot. Az alábbi parancs kiosztja az erőforráscsoporthoz tartozik, az Azure PostgreSQL-adatbázis *MyResourceGroup* a a *eastus* régióban. További információ a *resourceGroup*, *hely*, és más Azure-ra vonatkozó JSON-paraméter elérhető a [PostgreSQL modul dokumentációjában](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

Az adatbázis nevű szolgáltatásként van közzétéve a Pivotal Cloud Foundry-példány *mypgsql*. Az adatbázis befejezését követően kiépítést, ami néhány perc alatt hozhasson létre, az alkalmazás. Annak ellenőrzéséhez, hogy az adatbázis kiépítése befejeződött, használja a `cf services` parancsot:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

A *a legutóbbi művelet* érték, a szolgáltatás *létrehozása sikerült* amikor befejeződött kiépítése.

## <a name="bind-the-database-to-your-application"></a>Az adatbázis kötése az alkalmazáshoz

Használja a `bind-service` kötést létrehozni a szolgáltatást az alkalmazás parancsot.

```cmd
cf bind-service spring-music mypgsql
```

Az alkalmazás a szolgáltatás kötődik, után restage az alkalmazás a módosítások érvénybe léptetéséhez.

```cmd
cf restage spring-music
```

Az alkalmazás [Spring Cloud összekötők használatára konfigurált](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), amely lehetővé teszi a Pivotal Cloud Foundry-példány használata [auto-újrakonfigurálás](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) a az alkalmazás adatforrás. Ebben az esetben a Pivotal Cloud Foundry-példány automatikusan újra fogja az alkalmazás-szolgáltatást egy adatforráshoz tartozó kötve a Ha az alkalmazás zített.

Miután az alkalmazás rendelkezik lett zített, azt fogja használni *mypgsql* adattároláshoz helyett a memóriában lévő adatbázishoz.

Végrehajtott módosítások most újraindul között maradnak, és ismételt üzembe helyezése. Az alkalmazás kapcsolati URL-cím használja újra az alkalmazás naplóinak megtekintésével is megjelenik.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Figyelje meg, hogy két tételt tartalmaz:

* Az eredeti értéket *h2:mem:testdb*.
* A PostgreSQL-adatbázishoz, ha az alkalmazás lett zített frissített értékét.

Ellenőrizze az adatokat tároló a PostgreSQL-adatbázishoz, keresse meg az alkalmazás a böngészőben, hajtson végre néhány módosítást, majd az alkalmazás restage:

```cmd
cf restage spring-music
```

Miután az alkalmazás rendelkezik lett zített, keresse meg a fájlt az azonos URL-cím használatával. Figyelje meg, hogy a módosítások vannak-e továbbra is.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ha azt szeretné, az alkalmazás leválasztása az adatbázist, unbind-használatával a `cf unbind-service` parancsot.

```cmd
cf unbind-service spring-music mypgsql
```

Hasonló kötés egy szolgáltatáshoz az alkalmazásra, az alkalmazás a módosítások érvénybe léptetéséhez kell restage. Ez a művelet egyaránt hagynak *mypgsql* és az alkalmazás változatlan marad, de az alkalmazás elkezdi használata helyett a memóriában lévő adatbázishoz *mypgsql*.

Az adatbázis törléséhez használja a `cf delete-service` parancsot. *Nem vonható vissza a művelet ezért mindenképpen törli az adatbázist, a folytatás előtt.*

```cmd
cf delete-service mypgsql
```

Az alkalmazás eltávolítása a Pivotal Cloud Foundry-példány:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban szereplő a Pivotal Cloud Foundry-alkalmazás üzembe helyezéséhez, valamint a Open Service Broker használatával az Azure-adatbázis létrehozása. Is vonatkozik, az alkalmazás a Pivotal Cloud Foundry-példány belül az adatbázis kötése. Cloud Foundry az Azure-alkalmazások központi telepítésével kapcsolatos további információkért lásd:

* [A cloud Foundry az Azure-ban](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Az első alkalmazás üzembe helyezése a Cloud Foundry-hoz a Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)