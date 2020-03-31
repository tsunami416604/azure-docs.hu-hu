---
title: Bevezetés az Azure Spring Cloud szolgáltatásba
description: Ismerje meg az Azure Spring Cloud funkcióit és előnyeit a Java Spring alkalmazások Azure-beli üzembe helyezéséhez és kezeléséhez.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273259"
---
# <a name="what-is-azure-spring-cloud"></a>Mi az az Azure Spring Cloud?

Az Azure Spring Cloud segítségével könnyedén telepítheti a tavaszi rendszerindításon alapuló mikroszolgáltatási alkalmazásokat az Azure-ba, nulla kódmódosítással.  Az Azure Spring Cloud kezeli a Spring Cloud-alkalmazások életciklusát, így a fejlesztők a kódjukra koncentrálhatnak.  A Spring Cloud átfogó figyelési és diagnosztikai, konfigurációs felügyelettel, szolgáltatásfelderítéssel, CI/CD-integrációval, kék-zöld telepítésekkel és egyebek használatával biztosítja az életciklus-kezelést.

Az Azure-ökoszisztéma részeként az Azure Spring Cloud egyszerű kötést tesz lehetővé más Azure-szolgáltatásokhoz, például a tároláshoz, az adatbázisokhoz, a figyeléshez és egyebekhez.

Az Azure Spring Cloud jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verziólehetővé teszi az ügyfelek számára, hogy a hivatalos kiadás előtt kísérletezzenek az új funkciókkal.  A nyilvános előzetes verziójú funkciók és szolgáltatások nem éles környezetben való használatra valók.  Az előzetes verziók során nyújtott támogatással kapcsolatos további információkért tekintse át [a gyakori kérdéseket,](https://azure.microsoft.com/support/faq/) vagy nyújtson be [támogatási kérelmet](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) további információkért.

Első lépésekhez végezze el a Spring Cloud rövid útmutatóját az [Azure CLI,](spring-cloud-quickstart-launch-app-cli.md)az [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)vagy a [Maven](spring-cloud-quickstart-launch-app-maven.md)használatával.

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)

## <a name="application-configuration"></a>Alkalmazás konfigurációja

### <a name="spring-cloud-config-server"></a>Tavaszi felhőalapú konfigurációs kiszolgáló

Az Azure Spring Cloud Config Server külső konfigurációt biztosít egy elosztott rendszerben, kiszolgálói és ügyféloldali támogatással.  A Config Server központi helyet biztosít az alkalmazástulajdonságok kezeléséhez az összes környezetben.  További információért látogasson el a [Spring Cloud Config Server referencia-](https://spring.io/projects/spring-cloud-config.md) és az oktatóanyag befejezéséhez.

### <a name="enable-bluegreen-deployments"></a>Kék/zöld telepítések engedélyezése

Az Azure Spring Cloud támogatja a kék/zöld központi telepítéseket a kód éles környezetekkiadásához és frissítéséhez.  Kihasználva ez a változáskezelési minta lehetővé teszi a fejlesztők számára, hogy végre funkciók és kód változások a biztonság azonnali tartalék, ha szükséges.  Az Azure lehetővé teszi a fejlesztők számára, hogy több éles környezet kezelésével összpontosítsanak a kódírásra, és megkönnyítsék a kódmódosítások frissítését vagy visszaállítását az alkalmazás megszakítása nélkül.  Ha többet szeretne megtudni az átmeneti környezetekről és a kék/zöld üzemelő példányokról, olvassa el ezt az [útmutató cikket.](spring-cloud-howto-staging-environment.md)

### <a name="automate-cicd-pipelines"></a>CI/CD-folyamatok automatizálása

Az Azure Spring Cloud integrációt biztosít az Azure DevOps-szal az Azure CLI használatával.  Az Azure DevOps használatával automatizálhatja a kódintegrációt és a tavaszi alkalmazásba való üzembe helyezést.  Ha többet szeretne megtudni, látogasson el ezt a [cikket](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Az alkalmazás méretezése

Az Azure Spring Cloud lehetővé teszi a mikroszolgáltatások egyszerű méretezését az Azure Spring Cloud irányítópultján.  A vCPU-k száma és a mikroszolgáltatások számára rendelkezésre álló memória mennyisége is az igényeinek megfelelően fel- vagy leskálázható.  A méretezés másodpercek alatt lép érvénybe, és nem igényel kódmódosításokat vagy újratelepítést.  Ha többet szeretne megtudni, töltse ki ezt az [oktatóanyagot.](spring-cloud-tutorial-scale-manual.md)

## <a name="application-monitoring"></a>Alkalmazásfigyelés

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Az alkalmazás figyelése az elosztott nyomkövetés és az Azure App Insights használatával

A Spring Cloud elosztott nyomkövetési eszközei lehetővé teszik a fejlesztők számára, hogy hibakeresést kövessenek el és figyeljék az alkalmazások mikroszolgáltatásai közötti összetett összekapcsolódásokat.  A [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) és az Azure [Application Insights](../azure-monitor/insights/insights-overview.md)integrálásával az Azure hatékony elosztott nyomkövetési képességet biztosít közvetlenül az Azure Portalról.  Ha többet szeretne megtudni, töltse ki ezt az [oktatóanyagot.](spring-cloud-tutorial-distributed-tracing.md)

## <a name="next-steps"></a>További lépések

- [A Spring Cloud alkalmazás elindítása a CLI-ből](spring-cloud-quickstart-launch-app-cli.md)
