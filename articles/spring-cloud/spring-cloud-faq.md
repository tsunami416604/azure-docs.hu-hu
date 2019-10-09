---
title: Gyakran ismételt kérdések az Azure Spring Cloud-ról | Microsoft Docs
description: Az Azure Spring Cloud gyakori kérdéseinek áttekintése
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 09970468a277dcaf3f28b4f5065572568089a12e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039118"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk az Azure Spring Cloud szolgáltatással kapcsolatos gyakori kérdéseket tárgyalja. 

## <a name="general"></a>Általános

### <a name="why-azure-spring-cloud"></a>Az Azure Spring Cloud bemutatása

Az Azure Spring Cloud egy szolgáltatásként szolgáló platformként szolgál (a tavaszi fejlesztők számára). Az Azure Spring Cloud az alkalmazás-infrastruktúrát kezeli, így az alkalmazás kódjára és az üzleti logikára koncentrálhat. Az Azure Spring Cloud beépített alapfunkciói közé tartozik az Eureka, a konfigurációs kiszolgáló, a szolgáltatás beállításjegyzék-kiszolgálója, a Pivotal Build szolgáltatás, a kék zöld környezetek és egyebek. Ez a szolgáltatás azt is lehetővé teszi, hogy a fejlesztők az Azure-szolgáltatásokkal, például a CosmosDB, a MySQL-sel és az Azure cache-sel Redis az alkalmazásaikat.

Az Azure Spring Cloud a fejlesztők és a kezelők számára a Azure Monitor, a Application Insights és a Log Analytics integrálásával fokozza az Application Diagnostics felületét.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Milyen szolgáltatási csomagokkal rendelkezik az Azure Spring Cloud ajánlat?

Az Azure Spring Cloud ingyenes az előzetes verzió ideje alatt.

Resource | Mennyiség
------- | -------
vCPU | 4
Memory (Memória) | 8 GByte
Alkalmazás-példányok rugós alkalmazásként | 20
Alkalmazás-példányok teljes száma Azure Spring Cloud Service-példányon | 50 *
Azure Spring Cloud Service instances régiónként/előfizetés | 2
Állandó kötetek | 10 x 50 GByte

@no__t – 0 _[támogatási jegyet](https://azure.microsoft.com/support/faq/) nyit a korlát növelésére._

További részletekért tekintse meg az [Azure-támogatással kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Mennyire biztonságos az Azure Spring Cloud?

A biztonság és az adatvédelem az Azure és az Azure Spring Cloud ügyfeleinek egyik legfontosabb prioritása. Az Azure biztosítja, hogy csak az ügyfél férhet hozzá az alkalmazásadatokhöz, a naplókhoz és a konfigurációhoz az összes ilyen információ biztonságos titkosításával. Az Azure Spring Cloud összes szolgáltatási példánya el van különítve egymástól.

Az Azure Spring Cloud teljes körű SSL-és Tanúsítványkezelőt biztosít.

A OpenJDK és a Spring Cloud Runtime kritikus biztonsági javításait a lehető leghamarabb alkalmazza az Azure Spring Cloud-ra.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Mely régiókban érhetők el az Azure Spring Cloud?

USA keleti régiója, USA 2. nyugati régiója, Nyugat-Európa és Délkelet-Ázsia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Az Azure Spring Cloud ismert korlátai

Itt láthatja az Azure Spring Cloud ismert korlátozásait, miközben a szolgáltatás előzetes verzióban érhető el.

* a `spring.application.name` felülbírálja az egyes alkalmazások létrehozásához használt alkalmazás nevét.
* @no__t – 0 nem engedélyezett a git-tárház konfigurációs fájljában. A konfigurációs fájlhoz való hozzáadás valószínűleg azt eredményezi, hogy az alkalmazás nem érhető el más alkalmazásokból vagy internetről.
* A Azure Portal és Resource Manager-sablonok nem támogatják az alkalmazáscsomag feltöltését. Ez csak az Azure CLI-vel történő alkalmazás-telepítéssel végezhető el.
* A kvóta korlátaival kapcsolatban tekintse meg, hogy az [Azure Spring Cloud ajánlat milyen szolgáltatási csomagokra](#what-service-plans-does-azure-spring-cloud-offer)vonatkozik.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hogyan adhatok visszajelzést és jelenthetem a jelentéssel kapcsolatos problémákat?

Ha létrehozta a Spring Service-példányokat az Azure Spring Cloud-ban, létrehozhat egy [Azure-támogatási kérelmet](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Ha még nem állította be az Azure Spring Cloud-t, az [Azure feedback](https://feedback.azure.com/) szolgáltatásban kérheti a funkciók kérését, és visszajelzést is küldhet.

## <a name="development"></a>Fejlesztés

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Tavasszal fejlesztem az Azure-t, de ez a leggyorsabb módszer a Azure Spring Cloud-alkalmazások fejlesztéséhez?

Az Azure Spring Cloud első lépéseinek leggyorsabb módja, ha [ezt a gyors üzembe helyezést](spring-cloud-quickstart-launch-app-portal.md)követi.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Milyen Java-futtatókörnyezetet támogat az Azure Spring Cloud?

Az Azure Spring Cloud támogatja a Java 8 és a 11 platformot.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Hol láthatom a rugós alkalmazások naplóit és metrikáit?

A metrikák kereséséhez az alkalmazás áttekintése lapon és a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) lapon.

Az Azure Spring Cloud támogatja a Spring Application-naplók és-metrikák exportálását az Azure Storage, a EventHub és a [log Analyticsba](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). A Log Analytics `AppPlatformLogsforSpring` nevű táblanév. Ennek engedélyezéséhez tekintse át ezt a cikket a [diagnosztikai szolgáltatásokról](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Támogatja az Azure Spring Cloud az elosztott nyomkövetést?

Igen, további részletekért látogasson el az [elosztott nyomkövetésre](spring-cloud-tutorial-distributed-tracing.md) .

### <a name="what-resource-types-does-service-binding-support"></a>Milyen típusú erőforrásokat támogat a szolgáltatás-kötés?

Jelenleg három szolgáltatás támogatott: Azure Cosmos DB, Azure Database for MySQL és Azure cache a Redis-hez.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Megtekinthetem, hogyan lehet állandó köteteket megtekinteni/felvenni vagy áthelyezni az alkalmazásaim belsejében?
Igen.

## <a name="deployment"></a>Környezet

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Támogatja az Azure Spring Cloud a Blue-Green üzembe helyezést?
Igen, tekintse meg az [átmeneti környezet útmutatóját](spring-cloud-howto-staging-environment.md) további részletekért.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Hozzáférhetek a Kubernetes az alkalmazás-tárolók kezeléséhez?

Nem.  Az Azure Spring Cloud elvonta a fejlesztőt a mögöttes architektúrából, így az alkalmazás kódjára és az üzleti logikára koncentrálhat.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Támogatja az Azure Spring Cloud a tárolók létrehozását a forrástól?

Igen, további részletekért látogasson el [a forrás üzembe helyezése forrásból](spring-cloud-launch-from-source.md) .

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Támogatja az Azure Spring Cloud az automatikus skálázást az alkalmazás példányaiban?

Nem.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Mik az ajánlott eljárások a meglévő Spring-szolgáltatások Azure Spring Cloud-ba való áttelepítéséhez?

A meglévő Spring-szolgáltatásoknak az Azure Spring Cloud-ba való migrálása előtt
* Minden alkalmazás-függőséget fel kell oldani.
* Készítse elő a konfigurációs bejegyzéseket, a környezeti változókat és a JVM paramétereket, hogy összehasonlítsa őket az Azure Spring Cloud üzembe helyezésével.
* Ha szolgáltatási kötést szeretne használni, ugorjon az Azure-szolgáltatásaihoz, és győződjön meg arról, hogy megfelelő hozzáférési engedélyeket adott meg.
* Javasoljuk, hogy távolítsa el vagy tiltsa le azokat a beágyazott szolgáltatásokat, amelyek ütközhetnek az Azure Spring Cloud által kezelt szolgáltatásokkal, például a Service Discovery szolgáltatással, a konfigurációs kiszolgálóval stb..
*@no__t – 1* Javasoljuk, hogy használja a hivatalos és a stabil Pivotal Spring könyvtárakat. A Pivotal Spring-kódtárak nem hivatalos, bétaverziós vagy elágazó verziói nem rendelkeznek SLA-támogatással.

Az áttelepítés után figyelje a CPU/RAM mérőszámait és a hálózati forgalmat annak érdekében, hogy az alkalmazás példányai megfelelően méretezhetők legyenek.

## <a name="next-steps"></a>További lépések

[Ha további kérdései vannak, tekintse meg a hibaelhárítási útmutatót](spring-cloud-troubleshoot.md).