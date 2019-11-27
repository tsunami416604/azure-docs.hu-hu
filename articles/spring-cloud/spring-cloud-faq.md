---
title: Gyakran ismételt kérdések az Azure Spring Cloud-ról | Microsoft Docs
description: Ez a cikk az Azure Spring Cloud szolgáltatással kapcsolatos gyakori kérdésekre ad választ.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229253"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud – gyakori kérdések

Ez a cikk az Azure Spring Cloud szolgáltatással kapcsolatos gyakori kérdésekre ad választ. 

## <a name="general"></a>Általános kérdések

### <a name="why-azure-spring-cloud"></a>Az Azure Spring Cloud bemutatása

Az Azure Spring Cloud a tavaszi felhőalapú fejlesztők számára biztosít egy szolgáltatásként elérhető platformot (Péter). Az Azure Spring Cloud felügyeli az alkalmazás-infrastruktúráját, így az alkalmazás kódjára és az üzleti logikára koncentrálhat. Az Azure Spring Cloud beépített alapfunkciói közé tartozik az Eureka, a konfigurációs kiszolgáló, a szolgáltatás beállításjegyzék-kiszolgálója, a Pivotal Build szolgáltatás, a kék zöld környezetek és egyebek. Ez a szolgáltatás azt is lehetővé teszi, hogy a fejlesztők az alkalmazásaikat más Azure-szolgáltatásokkal, például a Azure Cosmos DB, a Azure Database for MySQL és az Azure cache Redis-hez kössenek.

Az Azure Spring Cloud a fejlesztők és a kezelők számára a Azure Monitor, Application Insights és Log Analytics integrálásával fokozza az Application Diagnostics-élményt.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Milyen szolgáltatási csomagokkal rendelkezik az Azure Spring Cloud ajánlat?

Az Azure Spring Cloud egy szolgáltatási csomagot kínál az előzetes verzió ideje alatt.  A Spring Cloud üzemelő példány 16 vCPU magot és 32 GB memóriát tartalmaz.  Egy üzemelő példányon belül az egyes Service-példányok felső határa 4 vCPU mag, 8 GB memóriával.

Erőforrás | Mennyiség
------- | -------
Alkalmazás-példányok rugós alkalmazásként | 20
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 500
Azure Spring Cloud Service instances régiónként/előfizetés | 10
Állandó kötetek | 10 x 50 GByte

\* _a korlát növeléséhez nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/faq/)._

További információ: Azure- [támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Mennyire biztonságos az Azure Spring Cloud?

A biztonság és az adatvédelem az Azure és az Azure Spring Cloud ügyfeleinek legfontosabb prioritásai közé tartozik. Az Azure segítségével biztosíthatja, hogy csak az ügyfelek férhessenek hozzá az alkalmazásadatokhöz, a naplókhoz és a konfigurációhoz az összes információ biztonságos titkosításával. Az Azure Spring Cloud összes szolgáltatási példánya el van különítve egymástól.

Az Azure Spring Cloud teljes körű SSL-és Tanúsítványkezelőt biztosít.

A OpenJDK és a Spring Cloud Runtime kritikus biztonsági javításait a lehető leghamarabb alkalmazza az Azure Spring Cloud-ra.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Mely régiókban érhető el az Azure Spring Cloud?

USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Mik az Azure Spring Cloud ismert korlátai?

Az előzetes verzióban az Azure Spring Cloud a következő ismert korlátozásokkal rendelkezik:

* a `spring.application.name` felülbírálja az egyes alkalmazások létrehozásához használt alkalmazás neve.
* `server.port` nem engedélyezett a git-tárház konfigurációs fájljában. Ha hozzáadja azt a konfigurációs fájlhoz, az alkalmazás valószínűleg nem lesz elérhető más alkalmazásokból vagy az internetről.
* A Azure Portal és Azure Resource Manager sablonok nem támogatják az alkalmazáscsomag feltöltését. Az alkalmazás csomagjait csak az Azure CLI-n keresztüli üzembe helyezésével töltheti fel.
* A kvóta korlátaival kapcsolatos további információkért tekintse meg az [Azure Spring Cloud ajánlatának bemutatása](#what-service-plans-does-azure-spring-cloud-offer)című témakört.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hogyan adhatok visszajelzést és jelenthetem a jelentéssel kapcsolatos problémákat?

Ha problémák merülnek fel az Azure Spring Cloudtel kapcsolatban, hozzon létre egy [Azure-támogatási kérelmet](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Ha beküld egy szolgáltatási kérelmet, vagy visszajelzést szeretne küldeni, ugorjon az [Azure-visszajelzések](https://feedback.azure.com/forums/34192--general-feedback)lehetőségre.

## <a name="development"></a>Fejlesztés

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Tavaszi Felhőbeli fejlesztő vagyok, de az Azure új. Mi a leggyorsabb módszer arra, hogy megtudjam, hogyan fejleszthet egy Azure Spring Cloud-alkalmazást?

Az Azure Spring Cloud első lépéseinek megkezdéséhez kövesse a rövid útmutató [: Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart-launch-app-portal.md)című témakör utasításait.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Milyen Java-futtatókörnyezetet támogat az Azure Spring Cloud?

Az Azure Spring Cloud támogatja a Java 8 és a 11 platformot.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Hol tekinthetem meg a Spring Cloud Application-naplókat és-metrikákat?

A metrikák kereséséhez az alkalmazás áttekintése lapon és a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) lapon.

Az Azure Spring Cloud támogatja a Spring Cloud Application-naplók és-mérőszámok exportálását az Azure Storage, a EventHub és a [log Analyticsba](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). A Log Analytics *AppPlatformLogsforSpring*a táblázat neve. Ha szeretné megtudni, hogyan engedélyezheti, tekintse meg a [diagnosztikai szolgáltatásokat](diagnostic-services.md)ismertető témakört.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Támogatja az Azure Spring Cloud az elosztott nyomkövetést?

Igen. További információ: [oktatóanyag: elosztott nyomkövetés használata az Azure Spring Cloud használatával](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Milyen típusú erőforrásokat támogat a szolgáltatás-kötés?

Jelenleg három szolgáltatás támogatott: Azure Cosmos DB, Azure Database for MySQL és Azure cache a Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Megtekinthetem, hozzáadhatom vagy áthelyezhetem az állandó köteteket az alkalmazásaim belsejében?

Igen.

## <a name="deployment"></a>Környezet

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Támogatja az Azure Spring Cloud a Blue-Green üzembe helyezést?
Igen. További információ: [átmeneti környezet beállítása](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Hozzáférhetek a Kubernetes az alkalmazás-tárolók kezeléséhez?

Nem.  Az Azure Spring Cloud elvonta a fejlesztőt a mögöttes architektúrából, így az alkalmazás kódjára és az üzleti logikára koncentrálhat.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Támogatja az Azure Spring Cloud a tárolók létrehozását a forrástól?

Igen. További információ: [a Spring Cloud-alkalmazás elindítása forráskódból](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Támogatja az Azure Spring Cloud az automatikus skálázást az alkalmazás példányaiban?

Nem.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Mik az ajánlott eljárások a meglévő Spring Cloud-szolgáltatások Azure Spring Cloud-ba való áttelepítéséhez?

A meglévő Spring Cloud-szolgáltatásoknak az Azure Spring Cloudba való áttelepítése során érdemes megfigyelni az alábbi ajánlott eljárásokat:
* Minden alkalmazás-függőséget fel kell oldani.
* Készítse elő a konfigurációs bejegyzéseket, a környezeti változókat és a JVM paramétereket, hogy össze lehessen hasonlítani őket az Azure Spring Cloud üzembe helyezésével.
* Ha szolgáltatási kötést szeretne használni, ugorjon az Azure-szolgáltatásaihoz, és győződjön meg arról, hogy a megfelelő hozzáférési engedélyeket adta meg.
* Javasoljuk, hogy távolítsa el vagy tiltsa le azokat a beágyazott szolgáltatásokat, amelyek ütközhetnek az Azure Spring Cloud által felügyelt szolgáltatásokkal, például a Service Discovery szolgáltatással, a konfigurációs kiszolgálóval és így tovább.
* Javasoljuk, hogy a hivatalos és a stabil Pivotal rugós kódtárakat használja. A Pivotal Spring-kódtárak nem hivatalos, bétaverziós vagy elágazó verziói nem rendelkeznek szolgáltatói szerződés (SLA) támogatással.

Az áttelepítés után figyelje a CPU-/RAM-mérőszámokat és a hálózati forgalmat annak érdekében, hogy az alkalmazás példányai megfelelően méretezhetők legyenek.

## <a name="next-steps"></a>Következő lépések

Ha további kérdései vannak, tekintse meg az [Azure Spring Cloud hibaelhárítási útmutatóját](spring-cloud-troubleshoot.md).
