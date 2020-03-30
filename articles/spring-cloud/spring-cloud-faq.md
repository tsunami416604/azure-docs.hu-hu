---
title: Gyakori kérdések az Azure Spring Cloudszolgáltatásról | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Spring Cloudszolgáltatással kapcsolatos gyakori kérdésekre.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298762"
---
# <a name="azure-spring-cloud-faq"></a>Azure spring cloud – gyakori kérdések

Ez a cikk választ ad az Azure Spring Cloudszolgáltatással kapcsolatos gyakori kérdésekre. 

## <a name="general"></a>Általános kérdések

### <a name="why-azure-spring-cloud"></a>Miért az Azure Spring Cloud?

Az Azure Spring Cloud platformot (PaaS) biztosít a Spring Cloud-fejlesztők számára. Az Azure Spring Cloud kezeli az alkalmazás-infrastruktúrát, így az alkalmazáskódra és az üzleti logikára összpontosíthat. Az Azure Spring Cloud beépített alapvető funkciói közé tartozik az Eureka, a Config Server, a Service Registry Server, a Pivotal Build Service, a Blue-green telepítések és egyebek. Ez a szolgáltatás azt is lehetővé teszi a fejlesztők számára, hogy más Azure-szolgáltatásokkal, például az Azure Cosmos DB-vel, a MySQL-adatbázissal és a Redis-gyorsítótárral kössék össze alkalmazásaikat.

Az Azure Spring Cloud az Azure Monitor, az Application Insights és a Log Analytics integrálásával javítja az alkalmazásdiagnosztika élményét a fejlesztők és az operátorok számára.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Milyen szolgáltatási csomagokat kínál az Azure Spring Cloud?

Az Azure Spring Cloud egy szolgáltatási csomagot kínál az előzetes verzióban.  A Spring Cloud központi telepítése 16 vCPU-magot és 32 gigabájt (GB) memóriát tartalmaz.  A központi telepítésen belüli egyes mikroszolgáltatás-példányok felső határa 4 vCPU-malista 8 GB memóriával.

Erőforrás | Mennyiség
------- | -------
Alkalmazáspéldányok tavaszi alkalmazásonként | 20
Alkalmazáspéldányok összesen Azure Spring Cloud szolgáltatáspéldányonként | 500
Azure Spring Cloud szolgáltatáspéldányok régiónként és előfizetésenként | 10
Tartós kötetek | 10 x 50 GByte

\*_A limit emeléséhez nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/faq/)_

További információ: [Azure-támogatás – gyakori kérdések.](https://azure.microsoft.com/support/faq/)

### <a name="how-secure-is-azure-spring-cloud"></a>Mennyire biztonságos az Azure Spring Cloud?

A biztonság és az adatvédelem az Azure és az Azure Spring Cloud-ügyfelek legfontosabb prioritásai közé tartoznak. Az Azure az összes adat biztonságos titkosításával biztosíthatja, hogy csak az ügyfelek férhessenek hozzá az alkalmazásadatokhoz, naplókhoz vagy konfigurációkhoz. Az Azure Spring Cloud összes szolgáltatáspéldánya elkülönül egymástól.

Az Azure Spring Cloud teljes TLS/SSL-t és tanúsítványkezelést biztosít.

Az OpenJDK és a Spring Cloud futási ideje kritikus biztonsági javításai a lehető leghamarabb érvénybe lépnek az Azure Spring Cloud szolgáltatásban.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Mely régiókban érhető el az Azure Spring Cloud?

USA, USA nyugati része, 2, Nyugat-Európa és Délkelet-Ázsia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Melyek az Azure Spring Cloud ismert korlátai?

Az előzetes verzióban az Azure Spring Cloud a következő ismert korlátozásokkal rendelkezik:

* `spring.application.name`felülírja az egyes alkalmazások létrehozásához használt alkalmazásnév.
* `server.port`nem engedélyezett a git-tárházban lévő konfigurációs fájlban. Ha hozzáadja a konfigurációs fájlhoz, akkor az alkalmazás valószínűleg nem érhető el más alkalmazásokból vagy az internetről.
* Az Azure Portal és az Azure Resource Manager-sablonok nem támogatják az alkalmazáscsomagok feltöltését. Az alkalmazáscsomagok at csak az Azure CLI-n keresztül telepíti az alkalmazás üzembe helyezésével.
* A kvótakorlátozásokról a [Milyen szolgáltatási csomagokat kínál](#what-service-plans-does-azure-spring-cloud-offer)az Azure Spring Cloud?

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hogyan küldhetek visszajelzést, és jelenthetem a problémákat?

Ha bármilyen problémát tapasztal az Azure Spring Cloud szolgáltatással kapcsolatban, hozzon létre egy [Azure-támogatási kérelmet.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Ha szolgáltatáskérelmet szeretne küldeni vagy visszajelzést szeretne küldeni, nyissa meg az Azure Feedback című [területet.](https://feedback.azure.com/forums/34192--general-feedback)

## <a name="development"></a>Fejlesztés

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Spring Cloud-fejlesztő vagyok, de még új az Azure-ban. Mi a leggyorsabb módja annak, hogy megtanulják, hogyan kell fejleszteni egy Azure Spring Cloud-alkalmazás?

Az Azure Spring Cloud használatának leggyorsabb megkezdéséhez kövesse a [rövid útmutató utasításait: Indítson el egy Azure Spring Cloud-alkalmazást az Azure Portal használatával.](spring-cloud-quickstart-launch-app-portal.md)

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Milyen Java-futásidejű tazlon az Azure Spring Cloud?

Az Azure Spring Cloud támogatja a Java 8-at és 11-et.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Hol tekinthetem meg a Spring Cloud alkalmazásnaplókat és -mutatókat?

Metrikák keresése az Alkalmazás áttekintése lapon és az [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) lapon.

Az Azure Spring Cloud támogatja a tavaszi felhőalkalmazás-naplók és metrikák exportálását az Azure Storage, az EventHub és a [Log Analytics szolgáltatásba.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) A Tábla neve a Log Analytics-ben *AppPlatformLogsforSpring*. Az engedélyezésről a [Diagnosztikai szolgáltatások című](diagnostic-services.md)témakörben olvashat.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Támogatja az Azure Spring Cloud az elosztott nyomkövetést?

Igen. További információ: [Oktatóanyag: Az elosztott nyomkövetés használata az Azure Spring Cloud szolgáltatással](spring-cloud-tutorial-distributed-tracing.md)című témakörben talál.

### <a name="what-resource-types-does-service-binding-support"></a>Milyen erőforrástípusokat támogat a Service Binding?

Jelenleg három szolgáltatás támogatott: az Azure Cosmos DB, az Azure Database for MySQL és az Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Megtekinthetem, hozzáadhatom vagy áthelyezhetem az állandó köteteket az alkalmazásaimból?

Igen.

## <a name="deployment"></a>Környezet

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Támogatja az Azure Spring Cloud a kék-zöld telepítést?
Igen. További információt az [Átmeneti környezet beállítása](spring-cloud-howto-staging-environment.md)című témakörben talál.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Hozzáférhetek a Kubernetes-hez az alkalmazástárolók kezeléséhez?

Nem.  Az Azure Spring Cloud absztrakt a fejlesztő az alapul szolgáló architektúra, amely lehetővé teszi, hogy összpontosítson az alkalmazás kód és az üzleti logika.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Támogatja az Azure Spring Cloud a forrásból származó tárolók készítését?

Igen. További információ: [A Spring Cloud alkalmazás elindítása forráskódból.](spring-cloud-launch-from-source.md)

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Támogatja az Azure Spring Cloud az automatikus skálázást az alkalmazáspéldányokban?

Nem.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Mik az ajánlott eljárások a meglévő tavaszi felhőbeli mikroszolgáltatások Azure Spring Cloudba való áttelepítéséhez?

A meglévő tavaszi felhőalapú mikroszolgáltatások Azure Spring Cloudba való áttelepítése során érdemes figyelembe tenni az alábbi ajánlott eljárásokat:
* Minden alkalmazásfüggőséget fel kell oldani.
* Készítse elő a konfigurációs bejegyzéseket, a környezeti változókat és a JVM-paramétereket, hogy összehasonlíthassa őket az Azure Spring Cloud ban történő üzembe helyezéssel.
* Ha szolgáltatáskötést szeretne használni, menjen végig az Azure-szolgáltatásokon, és győződjön meg arról, hogy beállította a megfelelő hozzáférési engedélyeket.
* Azt javasoljuk, hogy távolítsa el vagy tiltsa le azokat a beágyazott szolgáltatásokat, amelyek ütközhetnek az Azure Spring Cloud által felügyelt szolgáltatásokkal, például a Szolgáltatásfelderítési szolgáltatással, a Config Server rel és így tovább.
* Javasoljuk, hogy használjon hivatalos, stabil Pivotal Spring könyvtárakat. A Pivotal Spring könyvtárak nem hivatalos, béta vagy villás verziói nem támogatják a szolgáltatásiszint-szerződést (SLA).

Az áttelepítés után figyelje a CPU/RAM metrikák és a hálózati forgalmat annak érdekében, hogy az alkalmazáspéldányok megfelelően méretezve.

## <a name="next-steps"></a>További lépések

Ha további kérdései vannak, tekintse meg az [Azure Spring Cloud hibaelhárítási útmutatóját.](spring-cloud-troubleshoot.md)
