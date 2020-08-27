---
title: Az Azure Spring Cloud bemutatása
description: Ismerje meg az Azure Spring Cloud funkcióit és előnyeit a Java Spring-alkalmazások üzembe helyezéséhez és kezeléséhez az Azure-ban.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: a8b6535a6e1f6bf083b4a82b9f2ecacbd222cb27
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949762"
---
# <a name="what-is-azure-spring-cloud"></a>Mi az az Azure Spring Cloud?

Az Azure Spring Cloud segítségével egyszerűen üzembe helyezhetők a Spring boot-alapú Service-alkalmazások az Azure-ban nulla kód módosításával.  Az Azure Spring Cloud a Spring Cloud Applications életciklusát kezeli, így a fejlesztők a kódra is koncentrálhat.  A Spring Cloud átfogó monitorozást és diagnosztikát, a konfiguráció felügyeletét, a szolgáltatások felderítését, a CI/CD-integrációt, a kék zöld környezeteket és egyebeket is lehetővé teszi.

Az Azure-ökoszisztéma részeként az Azure Spring Cloud egyszerű kötést tesz lehetővé más Azure-szolgáltatásokhoz, többek között a tároláshoz, az adatbázisokhoz, a figyeléshez és egyebekhez.

Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  Az előzetes verziókra vonatkozó támogatással kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](https://azure.microsoft.com/support/faq/) , vagy a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

Ez az oktatóanyag bemutatja az Azure Spring Cloud konfigurációs kiszolgálóját, a kék/zöld környezetek használatát, az alkalmazások méretezését és az alkalmazás teljesítményének figyelését.

## <a name="spring-cloud-config-server"></a>Spring Cloud config-kiszolgáló

Az Azure Spring Cloud config Server külső konfigurációt biztosít egy elosztott rendszeren a kiszolgáló-és ügyféloldali támogatással.  Az Azure Spring Cloud config Server egy központi hely az alkalmazások tulajdonságainak az összes környezetben való kezeléséhez. További információ: [Spring Cloud config Server Reference](https://spring.io/projects/spring-cloud-config.md). 



## <a name="enable-bluegreen-deployments"></a>Kék/zöld üzembe helyezés engedélyezése

Az Azure Spring Cloud támogatja a kék/zöld üzembe helyezést a kódok éles környezetekben való kiadásához és frissítéséhez.  A változás-kezelési minta kihasználása lehetővé teszi a fejlesztők számára, hogy a funkciók és a kód módosításait az azonnali tartalék biztonságával, ha szükséges.  Az Azure lehetővé teszi a fejlesztők számára, hogy több éles környezet kezelésével, valamint az alkalmazás megszakítása nélkül megkönnyítsék a kódok módosítását.  Ha többet szeretne megtudni az átmeneti környezetekről és a kék/zöld üzembe helyezésekről, tekintse meg ezt a [témakört](spring-cloud-howto-staging-environment.md).

## <a name="automate-cicd-pipelines"></a>CI/CD-folyamatok automatizálása

Az Azure Spring Cloud az Azure CLI használatával biztosítja az Azure DevOps való integrációt.  Az Azure DevOps segítségével automatizálhatja a kód integrálását és üzembe helyezését a Spring-alkalmazásban.  További tudnivalókért tekintse meg ezt a [cikket](spring-cloud-howto-cicd.md).

## <a name="scale-your-application"></a>Az alkalmazás méretezése

Az Azure Spring Cloud segítségével könnyedén méretezheti az Azure Spring Cloud-irányítópulton található Micro-szolgáltatásokat.  A vCPU száma és a mikro-szolgáltatások számára elérhető memória mennyisége egyaránt méretezhető és lekicsinyíthető az igényeinek megfelelően.  A skálázás másodpercek alatt lép érvénybe, és nem igényli a kód módosítását vagy az újratelepítést.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Alkalmazások monitorozása

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Az alkalmazás monitorozása az elosztott nyomkövetéssel és az Azure app bepillantást használva

A Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik a fejlesztők számára, hogy hibakeresést végezzenek, és figyelemmel kísérik az alkalmazásokban található szolgáltatások közötti összetett kapcsolatokat.  A [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) és az Azure [Application Insights](../azure-monitor/insights/insights-overview.md)integrálásával az Azure hatékony elosztott nyomkövetési képességet biztosít közvetlenül a Azure Portal.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Következő lépések
Első lépésként fejezze be a Spring Cloud rövid útmutatót az [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), a [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)vagy a [Maven](spring-cloud-quickstart-launch-app-maven.md)használatával.

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

> [!div class="nextstepaction"]
> [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)
