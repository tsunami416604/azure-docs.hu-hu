---
title: Oktatóanyag – Az elosztott nyomkövetés használata az Azure Spring Cloud szolgáltatással
description: Ez az oktatóanyag bemutatja, hogyan használhatja a Spring Cloud elosztott nyomkövetési szolgáltatását az Azure Application Insights on keresztül
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273214"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Elosztott nyomkövetés használata az Azure Spring Cloud szolgáltatással

Az Azure Spring Cloud elosztott nyomkövetési eszközeivel egyszerűen debugolhatja és figyelheti az összetett problémákat. Az Azure Spring Cloud integrálja a [spring cloud sleuth-ot](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insights szolgáltatással.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) Ez az integráció hatékony elosztott nyomkövetési képességet biztosít az Azure Portalról.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Engedélyezze az elosztott nyomkövetést az Azure Portalon.
> * Adja hozzá a Spring Cloud Sleuth-ot az alkalmazáshoz.
> * Tekintse meg a mikroszolgáltatási alkalmazások függőségi leképezéseit.
> * Keresés nyomkövetési adatok különböző szűrőkkel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához olyan Azure Spring Cloud-szolgáltatásra van szüksége, amely már ki van építve és fut. Az Azure Spring Cloud-szolgáltatás kiépítéséhez és futtatásához az Azure Spring Cloud szolgáltatás kiépítéséhez és futtatásához az [Azure CLI-n keresztül](spring-cloud-quickstart-launch-app-cli.md) iüzembe helyezés rövid útmutatót végezhet el.
    
## <a name="add-dependencies"></a>Függőségek hozzáadása

1. Adja hozzá a következő sort az application.properties fájlhoz:

   ```xml
   spring.zipkin.sender.type = web
   ```

   A módosítás után a Zipkin feladója elküldheti az internetre.

1. Hagyja ki ezt a lépést, ha az [Azure Spring Cloud-alkalmazás elkészítéséhez vezető útmutatónkat](spring-cloud-tutorial-prepare-app-deployment.md)követte. Ellenkező esetben lépjen a helyi fejlesztői környezetbe, és szerkesztheti a pom.xml fájlt, hogy tartalmazza a következő Spring Cloud Sleuth függőséget:

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

1. Építsen és telepítse újra az Azure Spring Cloud szolgáltatáshoz, hogy tükrözze ezeket a változásokat.

## <a name="modify-the-sample-rate"></a>A mintavételi sebesség módosítása

Módosíthatja a telemetriai adatok gyűjtésének sebességét a mintasebesség módosításával. Ha például fele olyan gyakran szeretne mintát venni, nyissa meg az application.properties fájlt, és módosítsa a következő sort:

```xml
spring.sleuth.sampler.probability=0.5
```

Ha már készített és telepített egy alkalmazást, módosíthatja a mintavételi sebességet. Ehhez adja hozzá az előző sort környezeti változóként az Azure CLI-ben vagy az Azure Portalon.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Nyissa meg az Azure Spring Cloud szolgáltatáslapját az Azure Portalon.
1. A **Figyelés** lapon válassza az **Elosztott nyomkövetés lehetőséget.**
1. Új beállítás szerkesztéséhez vagy hozzáadásához válassza a **Szerkesztés lehetőséget.**
1. Hozzon létre egy új Application Insights-lekérdezést, vagy válasszon ki egy meglévőt.
1. Válassza ki, hogy melyik naplózási kategóriát szeretné figyelni, és adja meg a megőrzési időt napokban.
1. Az új kontúrozás alkalmazásához válassza az **Alkalmaz** lehetőséget.

## <a name="view-the-application-map"></a>Az alkalmazástérkép megtekintése

Térjen vissza az **Elosztott nyomkövetési** lapra, és válassza **az Alkalmazástérkép megtekintése**lehetőséget. Tekintse át az alkalmazás és a figyelési beállítások vizuális megjelenítését. Az alkalmazástérkép használatáról az [Alkalmazástérkép: Triage elosztott alkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/app-map)című témakörben olvashat.

## <a name="use-search"></a>Keresés használata

A keresési funkció segítségével más konkrét telemetriai elemek lekérdezése. Az **Elosztott nyomkövetés** lapon válassza a **Keresés**lehetőséget. A keresési funkció használatáról a Keresés használata az [Application Insights ban című témakörben](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)talál további információt.

## <a name="use-application-insights"></a>Az Application Insights használata

Az Application Insights az alkalmazástérkép- és keresési funkció mellett figyelési lehetőségeket is biztosít. Keresse meg az Azure Portalon az alkalmazás nevét, majd nyisson meg egy Application Insights-lapot a figyelési információk megkereséséhez. Az eszközök használatával kapcsolatos további útmutatásért tekintse meg az [Azure Monitor naplólekérdezéseit.](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)

## <a name="disable-application-insights"></a>Alkalmazáselemzési adatok letiltása

1. Nyissa meg az Azure Spring Cloud szolgáltatáslapját az Azure Portalon.
1. A **Figyelés kor**válassza **az Elosztott nyomkövetés lehetőséget.**
1. Az Application Insights **letiltása** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és értheti meg az elosztott nyomkövetést az Azure Spring Cloudban. Ha meg szeretné tudni, hogyan kötheti össze az alkalmazást egy Azure Cosmos DB-adatbázishoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan köthető kontúrható egy Azure Cosmos DB-adatbázishoz](spring-cloud-tutorial-bind-cosmos.md)
