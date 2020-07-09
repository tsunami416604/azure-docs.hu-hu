---
title: Elosztott nyomkövetés használata az Azure Spring Cloud használatával
description: Ismerje meg, hogyan használhatja a Spring Cloud elosztott nyomkövetését az Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: ccaf58465c1ade0228daea2b535d06fb6168d64f
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142121"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Elosztott nyomkövetés használata az Azure Spring Cloud használatával

Az Azure Spring Cloud elosztott nyomkövetési eszközeivel könnyedén hibakeresést végezhet, és figyelheti az összetett problémákat. Az Azure Spring Cloud egyesíti a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insightsával](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Ez az integráció hatékony elosztott nyomkövetési képességet biztosít a Azure Portal.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az elosztott nyomkövetés engedélyezése a Azure Portalban.
> * A Spring Cloud Sleuth hozzáadása az alkalmazáshoz.
> * Tekintse meg a szolgáltatói alkalmazások függőségi térképeit.
> * Keresési nyomkövetési adatkeresés különböző szűrőkkel.

## <a name="prerequisites"></a>Előfeltételek

A fenti eljárások követéséhez olyan Azure Spring Cloud Service-szolgáltatásra van szükség, amely már üzembe van és fut. Fejezze be a gyors [üzembe helyezési útmutatót az Azure CLI-n keresztül](spring-cloud-quickstart-launch-app-cli.md) az Azure Spring Cloud Service kiépítéséhez és futtatásához.
    
## <a name="add-dependencies"></a>Függőségek hozzáadása

1. Adja hozzá a következő sort az Application. properties fájlhoz:

   ```xml
   spring.zipkin.sender.type = web
   ```

   A módosítás után a Zipkin küldő küldhet a weben.

1. Ugorja át ezt a lépést, ha követte az [Azure Spring Cloud-alkalmazás előkészítését ismertető útmutatót](spring-cloud-tutorial-prepare-app-deployment.md). Ellenkező esetben lépjen a helyi fejlesztési környezetbe, és szerkessze pom.xml-fájlját, hogy tartalmazza a következő Spring Cloud Sleuth függőséget:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Hozza létre és telepítse újra az Azure Spring Cloud Service-t, hogy tükrözze ezeket a módosításokat.

## <a name="modify-the-sample-rate"></a>A mintavételi sebesség módosítása

A mintavételi sebesség módosításával módosíthatja a telemetria gyűjtésének gyakoriságát. Ha például a felet gyakran szeretné felvenni, nyissa meg az Application. properties fájlt, és módosítsa a következő sort:

```xml
spring.sleuth.sampler.probability=0.5
```

Ha már létrehozott és telepített egy alkalmazást, módosíthatja a mintavételezési arányt. Ezt úgy teheti meg, ha hozzáadja az előző sort környezeti változóként az Azure CLI-ben vagy a Azure Portal.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A **figyelés** lapon válassza az **elosztott nyomkövetés**lehetőséget.
1. Válassza a **beállítás szerkesztése** lehetőséget egy új beállítás szerkesztéséhez vagy hozzáadásához.
1. Hozzon létre egy új Application Insights lekérdezést, vagy válasszon ki egy meglévőt.
1. Válassza ki a figyelni kívánt naplózási kategóriát, és adja meg a megőrzési időt napokban.
1. Az új nyomkövetés alkalmazásához kattintson az **alkalmaz** gombra.

## <a name="view-the-application-map"></a>Az alkalmazás-hozzárendelés megtekintése

Térjen vissza az **elosztott nyomkövetés** lapra, és válassza az **alkalmazás-hozzárendelés megtekintése**lehetőséget. Tekintse át az alkalmazás és a figyelési beállítások vizuális megjelenítését. Az alkalmazás-hozzárendelés használatának megismeréséhez tekintse meg az [Application Map: elosztott alkalmazások osztályozása](https://docs.microsoft.com/azure/azure-monitor/app/app-map)című témakört.

## <a name="use-search"></a>Keresés használata

Más konkrét telemetria-elemek lekérdezéséhez használja a Search függvényt. Az **elosztott nyomkövetés** lapon válassza a **Keresés**lehetőséget. További információ a keresési funkció használatáról: [Keresés használata Application Insightsban](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Application Insights használata

A Application Insights az Application Map és a Search függvény mellett biztosít figyelési képességeket. Keresse meg az alkalmazás neve Azure Portal, majd nyisson meg egy Application Insights lapot a figyelési információk megkereséséhez. Az eszközök használatáról további útmutatásért tekintse meg Azure Monitor a [naplók lekérdezéseit](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Application Insights letiltása

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A **figyelés**lapon válassza az **elosztott nyomkövetés**lehetőséget.
1. Application Insights letiltásához válassza a **Letiltás** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és értelmezheti az elosztott nyomkövetést az Azure Spring Cloud-ban. Az alkalmazásokhoz való kötési szolgáltatásokkal kapcsolatos további tudnivalókért lásd: [Azure Cosmos db adatbázis kötése egy Azure Spring Cloud-alkalmazáshoz](spring-cloud-tutorial-bind-cosmos.md).
