---
title: Oktatóanyag – elosztott nyomkövetés használata az Azure Spring Cloud használatával
description: Ez az oktatóanyag bemutatja, hogyan használható a Spring Cloud elosztott nyomkövetése az Azure Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708758"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Oktatóanyag: elosztott nyomkövetés használata az Azure Spring Cloud használatával

A Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik az összetett problémák egyszerű hibakeresését és figyelését. Az Azure Spring Cloud integrálja a [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) az Azure [Application Insightsával](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , hogy hatékony, elosztott nyomkövetési képességet biztosítson a Azure Portal.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Elosztott nyomkövetés engedélyezése a Azure Portalban
> * A Spring Cloud Sleuth hozzáadása az alkalmazáshoz
> * A szolgáltatói alkalmazások függőségi térképének megtekintése
> * Nyomkövetési adatkeresés különböző szűrőkkel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Már üzembe lett helyezve, és futtatja az Azure Spring Cloud Service-t.  Fejezze be [ezt](spring-cloud-quickstart-launch-app-cli.md) a rövid útmutatót egy Azure Spring Cloud-szolgáltatás üzembe helyezéséhez és elindításához.
    
## <a name="add-dependencies"></a>Függőségek hozzáadása

Engedélyezze a zipkin feladójának a webes küldést úgy, hogy hozzáadja a következő sort az Application. properties fájlhoz:

```xml
spring.zipkin.sender.type = web
```

A következő lépés kihagyható, ha követte az [Azure Spring Cloud-alkalmazás prepping szóló útmutatót](spring-cloud-tutorial-prepare-app-deployment.md). Ellenkező esetben lépjen a helyi fejlesztési környezetbe, és szerkessze `pom.xml`-fájlját, hogy tartalmazza a Spring Cloud Sleuth függőségét:

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

* Hozza létre és telepítse újra az Azure Spring Cloud Service-t, hogy tükrözze ezeket a módosításokat. 

## <a name="modify-the-sample-rate"></a>A mintavételi sebesség módosítása
A mintavételi sebesség módosításával módosíthatja a telemetria gyűjtésének gyakoriságát. Ha például a felet gyakran szeretné megtekinteni, ugorjon a `application.properties` fájlra, és módosítsa a következő sort:

```xml
spring.sleuth.sampler.probability=0.5
```

Ha már létrehozta és telepítette az alkalmazást, módosíthatja a mintavételezési sebességet úgy, hogy hozzáadja a fenti sort környezeti változóként az Azure CLI-ben vagy a portálon. 

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A figyelés szakaszban válassza az **elosztott nyomkövetés**lehetőséget.
1. Válassza a **beállítás szerkesztése** lehetőséget egy új beállítás szerkesztéséhez vagy hozzáadásához.
1. Hozzon létre egy új alkalmazás-betekintési lekérdezést, vagy válasszon ki egy meglévőt.
1. Válassza ki a figyelni kívánt naplózási kategóriát, és adja meg a megőrzési időt (napokban).
1. Az új nyomkövetés alkalmazásához kattintson az **alkalmaz** gombra.

## <a name="view-application-map"></a>Alkalmazás térképének megtekintése

Térjen vissza az elosztott nyomkövetés lapra, és válassza az **alkalmazás-hozzárendelés megtekintése**lehetőséget. Tekintse át az alkalmazás és a figyelési beállítások vizuális megjelenítését. Az alkalmazás-hozzárendelés használatának megismeréséhez tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Search

Más konkrét telemetria-elemek lekérdezéséhez használja a Search függvényt. Az **elosztott nyomkövetés** lapon válassza a **Keresés**lehetőséget. A Search függvény használatáról további információt [ebben a cikkben](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)talál.

## <a name="application-insights-page"></a>Application Insights lap

Application Insights az alkalmazás térképén és a keresésen kívül is biztosít figyelési képességeket. Keresse meg a Azure Portal az alkalmazás nevét, majd indítson el egy Application Insights lapot, ahol további információ található. Az eszközök használatával kapcsolatos további útmutatásért [tekintse meg a dokumentációt](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Application Insights letiltása

1. Lépjen a Azure Portal Azure Spring Cloud Service oldalára.
1. A figyelés szakaszban kattintson az **elosztott nyomkövetés**elemre.
1. Application Insights letiltásához kattintson a **Letiltás** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és értelmezheti az elosztott nyomkövetést az Azure Spring Cloud-ban. Az alkalmazás Azure-CosmosDB való kötésének megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan köthető az alkalmazás egy Azure-CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
