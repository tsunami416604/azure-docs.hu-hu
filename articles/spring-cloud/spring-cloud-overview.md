---
title: Az Azure Spring Cloud bemutatása
description: Ismerje meg az Azure Spring Cloud funkcióit és előnyeit a Java Spring-alkalmazások üzembe helyezéséhez és kezeléséhez az Azure-ban.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 87b7ac1e9de2087de6e0b9d72fb445ca515a2e32
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277131"
---
# <a name="what-is-azure-spring-cloud"></a>Mi az az Azure Spring Cloud?

Az Azure Spring Cloud segítségével egyszerűen üzembe helyezhetők a Spring boot-alapú Service-alkalmazások az Azure-ban nulla kód módosításával.  Az Azure Spring Cloud a Spring Cloud Applications életciklusát kezeli, így a fejlesztők a kódra is koncentrálhat.  A Spring Cloud átfogó monitorozást és diagnosztikát, a konfiguráció felügyeletét, a szolgáltatások felderítését, a CI/CD-integrációt, a kék zöld környezeteket és egyebeket is lehetővé teszi.

Az Azure-ökoszisztéma részeként az Azure Spring Cloud egyszerű kötést tesz lehetővé más Azure-szolgáltatásokhoz, többek között a tároláshoz, az adatbázisokhoz, a figyeléshez és egyebekhez.

Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  Az előzetes verziókra vonatkozó támogatással kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](https://azure.microsoft.com/support/faq/) , vagy a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

Első lépésként fejezze be a Spring Cloud rövid útmutatót az [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), a [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)vagy a [Maven](spring-cloud-quickstart-launch-app-maven.md)használatával.

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Alkalmazás konfigurációja

### <a name="spring-cloud-config-server"></a>Spring Cloud config-kiszolgáló

Az Azure Spring Cloud config Server külső konfigurációt biztosít egy elosztott rendszeren a kiszolgáló-és ügyféloldali támogatással.  A konfigurációs kiszolgáló egy központi helyet biztosít az alkalmazások tulajdonságainak kezeléséhez az összes környezetben.  További információért látogasson el a [Spring Cloud config Server-referenciára](https://spring.io/projects/spring-cloud-config.md) , és fejezze be az oktatóanyagot.

### <a name="enable-bluegreen-deployments"></a>Kék/zöld üzembe helyezés engedélyezése

Az Azure Spring Cloud támogatja a kék/zöld üzembe helyezést a kódok éles környezetekben való kiadásához és frissítéséhez.  A változás-kezelési minta kihasználása lehetővé teszi a fejlesztők számára, hogy a funkciók és a kód módosításait az azonnali tartalék biztonságával, ha szükséges.  Az Azure lehetővé teszi a fejlesztők számára, hogy több éles környezet kezelésével, valamint az alkalmazás megszakítása nélkül megkönnyítsék a kódok módosítását.  Ha többet szeretne megtudni az átmeneti környezetekről és a kék/zöld üzembe helyezésekről, tekintse meg ezt a [témakört](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>CI/CD-folyamatok automatizálása

Az Azure Spring Cloud az Azure CLI használatával biztosítja az Azure DevOps való integrációt.  Az Azure DevOps segítségével automatizálhatja a kód integrálását és üzembe helyezését a Spring-alkalmazásban.  További tudnivalókért tekintse meg ezt a [cikket](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Az alkalmazás méretezése

Az Azure Spring Cloud segítségével könnyedén méretezheti az Azure Spring Cloud-irányítópulton található Micro-szolgáltatásokat.  A vCPU száma és a mikro-szolgáltatások számára elérhető memória mennyisége egyaránt méretezhető és lekicsinyíthető az igényeinek megfelelően.  A skálázás másodpercek alatt lép érvénybe, és nem igényli a kód módosítását vagy az újratelepítést.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Alkalmazásfigyelés

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Az alkalmazás monitorozása az elosztott nyomkövetéssel és az Azure app bepillantást használva

A Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik a fejlesztők számára, hogy hibakeresést végezzenek, és figyelemmel kísérik az alkalmazásban található szolgáltatások közötti összetett kapcsolatokat.  A [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) és az Azure [Application Insights](../azure-monitor/insights/insights-overview.md)integrálásával az Azure hatékony elosztott nyomkövetési képességet biztosít közvetlenül a Azure Portal.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Következő lépések

- [A Spring Cloud-alkalmazás elindítása a parancssori felületről](spring-cloud-quickstart-launch-app-cli.md)
