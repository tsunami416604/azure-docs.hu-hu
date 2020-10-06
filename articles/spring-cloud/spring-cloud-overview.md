---
title: Az Azure Spring Cloud bemutatása
description: Ismerje meg az Azure Spring Cloud funkcióit és előnyeit a Java Spring-alkalmazások üzembe helyezéséhez és kezeléséhez az Azure-ban.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b03bedd9abbe59ed3bc1b0ec1439aa27af57e6da
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758897"
---
# <a name="what-is-azure-spring-cloud"></a>Mi az az Azure Spring Cloud?

Az Azure Spring Cloud segítségével egyszerűen üzembe helyezhetők a Spring boot-alapú Service-alkalmazások az Azure-ban nulla kód módosításával.  Az Azure Spring Cloud a Spring Cloud Applications infrastruktúráját kezeli, így a fejlesztők a kódra is koncentrálhat.  A Spring Cloud átfogó monitorozást és diagnosztikát, a konfiguráció felügyeletét, a szolgáltatások felderítését, a CI/CD-integrációt, a kék zöld környezeteket és egyebeket is lehetővé teszi.

Az Azure Spring Cloud a Java [Spring boot](https://spring.io/projects/spring-boot) és a ASP.net Core [Steeltoe](https://steeltoe.io/) -alkalmazásokat egyaránt támogatja. A Steeltoe-támogatás jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verzióban elérhető ajánlatok lehetővé teszik, hogy a hivatalos kiadás előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  További információ: a [Gyakori kérdések](https://azure.microsoft.com/support/faq/) vagy a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)fájl.

Az Azure-ökoszisztéma részeként az Azure Spring Cloud egyszerű kötést tesz lehetővé más Azure-szolgáltatásokhoz, többek között a tároláshoz, az adatbázisokhoz, a figyeléshez és egyebekhez.

Ez a bevezetés az Azure Spring Cloud következő képességeit ismerteti:

* Konfigurációs kiszolgáló
* Kék/zöld üzembe helyezések
* Alkalmazás skálázása
* Integráció az Azure DevOpsszal
* Alkalmazások monitorozása

## <a name="spring-cloud-config-server"></a>Spring Cloud config-kiszolgáló

Az Azure Spring Cloud config Server külső konfigurációt biztosít egy elosztott rendszeren a kiszolgáló-és ügyféloldali támogatással.  Az Azure Spring Cloud config Server egy központi hely az alkalmazások tulajdonságainak az összes környezetben való kezeléséhez. További információ: [Spring Cloud config Server Reference](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Kék/zöld üzembe helyezések

Az Azure Spring Cloud támogatja a kék/zöld üzembe helyezést a kódok éles környezetekben való kiadásához és frissítéséhez.  Ez a módosítási felügyeleti minta lehetővé teszi a fejlesztők számára, hogy a funkciók és a kód módosításait az azonnali tartalék biztonságával, ha szükséges.  A fejlesztők az alkalmazás megszakítása nélkül is koncentrálhat több éles környezettel rendelkező kód írására a kód módosításának módosításához.  Ha többet szeretne megtudni az átmeneti környezetekről és a kék/zöld üzembe helyezésekről, tekintse meg ezt a [témakört](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>CI/CD-folyamat automatizálása

Az Azure Spring Cloud az Azure CLI használatával biztosítja az Azure DevOps való integrációt.  Az Azure DevOps segítségével automatizálhatja a kód integrálását és üzembe helyezését a Spring-alkalmazásban.  További tudnivalókért tekintse meg ezt a [cikket](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Alkalmazás skálázása

Az Azure Spring Cloud segítségével könnyedén méretezheti az Azure Spring Cloud-irányítópulton található Micro-szolgáltatásokat.  A vCPU száma és a mikro-szolgáltatások számára elérhető memória mennyisége egyaránt méretezhető és lekicsinyíthető az igényeinek megfelelően.  A skálázás másodpercek alatt lép érvénybe, és nem igényli a kód módosítását vagy az újratelepítést.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Alkalmazások monitorozása

A Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik a fejlesztők számára, hogy hibakeresést végezzenek, és figyelemmel kísérik az alkalmazásokban található szolgáltatások közötti összetett kapcsolatokat.  A [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) és az Azure [Application Insights](../azure-monitor/insights/insights-overview.md)integrálásával az Azure hatékony elosztott nyomkövetési képességet biztosít közvetlenül a Azure Portal.  További információért olvassa el ezt az [oktatóanyagot](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Következő lépések

Első lépésként fejezze be a [Spring Cloud](spring-cloud-quickstart.md) gyors útmutatóját

A minták a GitHubon érhetők el: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
