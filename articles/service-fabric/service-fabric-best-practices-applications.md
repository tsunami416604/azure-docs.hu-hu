---
title: Az Azure Service Fabric application tervezés – ajánlott eljárások |} A Microsoft Docs
description: Ajánlott eljárások a Service Fabric-alkalmazások fejlesztéséhez.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237747"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Az Azure Service Fabric application tervezés – ajánlott eljárások

A cikk ismerteti az ajánlott eljárásokkal kapcsolatos útmutatás a Service Fabricben fejlesztő alkalmazásokat és szolgáltatásokat.
 
## <a name="get-familiar-with-service-fabric"></a>Ismerkedjen meg a Service Fabric
* [Így biztosan ismerje meg a Service Fabric?](service-fabric-content-roadmap.md)
* További információ [Service Fabric-alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md)
* Ezután megismerheti a programozási modell választási lehetőség a [Service Fabric programozási modell áttekintése](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Alkalmazás-tervezési segédlet
A megismerése a [általános architektúra](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) Service Fabric-alkalmazás és a hozzá tartozó [kialakítási szempontok](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Válassza ki az API-átjáró
API-átjáró szolgáltatás, amely kommunikál majd kiterjeszthető háttér-szolgáltatásokhoz való használatához. A leggyakrabban használt API-átjáró szolgáltatásai használni a következők:

- [Az Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), amely [integrálva van a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Az Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) vagy [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) használatával a [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) olvasásához az Event Hubs-partíciók
- [A fordított proxy Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) használatával a [Azure Service Fabric-szolgáltató](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) Megjegyzés: Ez nem közvetlenül integrált a Service Fabric, és az Azure API Management általában egy előnyben részesített választás
- Létrehozhatja saját [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web application gateway

### <a name="choose-stateless-services"></a>Válassza ki az állapotmentes szolgáltatások
Azt javasoljuk, hogy Ön mindig először épület állapotmentes szolgáltatások használatával [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) állapota egy Azure Database, az Azure cosmos DB vagy az Azure storage tárolja. Externalized állapota a legtöbb fejlesztő több jól ismert módszere, és lehetővé teszi az áruházban is lekérdezési képességek kihasználásához.  

### <a name="when-to-choose-stateful-services"></a>Mikor érdemes az állapotalapú szolgáltatások kiválasztása
Állapotalapú szolgáltatások fontolja meg, ha az alacsony késés érdekében egy forgatókönyvet, és szeretné megőrizni az adatokat a számítási közel. Néhány példa: a digitális ikereszköz IoT-eszközök, a játék állapotát, a munkamenet-állapot, az adatokat az adatbázisból, és a hosszú ideig futó munkafolyamatok más hívások nyomon követéséhez.

Döntse el, az adatok megőrzési időkeretet:

- Gyorsítótárban tárolt adatok – használhat gyorsítótárazás késési külső áruházak ahol probléma. Az állapotalapú szolgáltatások használata a saját adatok gyorsítótárazása, vagy fontolja meg a [nyílt forráskódú SoCreate service fabric-elosztott-gyorsítótárazási](https://github.com/SoCreate/service-fabric-distributed-cache). Ebben a forgatókönyvben a gyorsítótárban lévő összes adat elvesztése fogadható el, és nem számít.
- Időhöz kötött adatok - kell tartani az adatok számítási késése egy ideig, de az adatokat, hogy elvész a kivegye egy *vészhelyreállítási* forgatókönyv. Például sok IoT-megoldások kell lenniük, zárja be a számítási erőforrásokat, például kiszámítása az átlaghőmérséklet az elmúlt néhány napban, azonban ha az adatok nem vesztek el, majd az adott adatpontok rögzített nem állnak, amely fontos. Emellett ebben a forgatókönyvben, nem általában érdeklik az egyes adatpontok, csak a számított átlagos írt értékeket a külső tárhelyen, rendszeres időközönként a biztonsági mentés.  
- Hosszú távú adatok számára, a Reliable collections tárolhatja adatait véglegesen. Azonban ebben az esetben kell [Felkészülés vészhelyreállításra](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) beleértve [rendszeres biztonsági mentési szabályzatok konfigurálása](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) a fürtök számára. Konfigurálja, mi történik, ha a fürt megsemmisülésekor vészhelyreállítási forgatókönyvekben, ahol kell hozzon létre egy új fürtöt és hogyan helyezhet üzembe új alkalmazáspéldányok, és helyreállíthatja a legutóbbi biztonsági.

Csökkentheti költségeit és javítható a rendelkezésre állás:
- Mivel nem jár többletköltséggel, a távoli tároló hozzáférési és tranzakciós költségek, és nem kell egy másik szolgáltatás, például az Azure redis Cache használata csökkentheti költségeit, az állapotalapú szolgáltatásokkal.
- Az állapotalapú szolgáltatások, elsősorban a tárolási és számítási a nem a következő költséges és nem ajánlott. Fontolja meg az állapotalapú szolgáltatások számítási olcsó helyi tárterülettel rendelkező.
- Függőségek a más szolgáltatások eltávolítása után a szolgáltatás rendelkezésre állása növelhető. A fürt magas rendelkezésre ÁLLÁS a kezelt állapot kellene közt megadott kezdőkönyvtárra korlátozása, más szolgáltatás leállás vagy késési problémák.

## <a name="how-to-properly-work-with-reliable-services"></a>A Reliable Services megfelelő használata
A Reliable Services lehetővé teszi, egyszerűen az állapot nélküli és állapotalapú szolgáltatások létrehozására. Olvassa el a [Reliable Services bemutatása](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Mindig tartsa tiszteletben a [megszakítás jogkivonat](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) a a `RunAsync()` metódus az állapot nélküli és állapotalapú szolgáltatások és a `ChangeRole()` metódus az állapotalapú szolgáltatások esetében. E nélkül a Service Fabric nem tudja, ha a szolgáltatás be lehet zárni. Például az a megszakítás token nem érvényesítenie eredményezhet sokkal több alkalmazás frissítési alkalommal.
-   Megnyitás és Bezárás [kommunikációs figyelőket](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) időben és a megszakítási tokeneket figyelembe veszi.
-   Soha ne keverje szinkronizálási kód aszinkron kóddal. Például ne használjon `.GetAwaiter().GetResult()` az aszinkron hívásban; kell lennie az aszinkron *egészen* keresztül a hívási veremben.

## <a name="how-to-properly-work-with-reliable-actors"></a>Reliable Actors megfelelően használata
A Reliable Actors segítségével könnyedén hozhat létre az állapotalapú és virtuális actors. Olvassa el a [Reliable Actors bemutatása](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Erősen fontolja meg az alkalmazás méretezése az actors közötti üzenetek pub/sub. Ha például a [nyílt forráskódú SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) vagy [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Győződjön meg arról, mint az aktorok állapotának [részletes lehető](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Kezelheti a [szereplő életciklus-](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Törölje az actors, ha nem fog minden eddiginél újból használják azt. Ez különösen igaz használatakor a [VolatileState szolgáltató](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) , a memóriában tárolt összes állapotát.
- Oka az, hogy azok [kapcsolja alapú egyidejűségi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) actors használhatók a legjobban független objektumként. Nincs több színész, a szinkron metódust hívja, (, amelyek a legnagyobb valószínűséggel lesz egy különálló hálózati hívást) diagramjait létrehozni, vagy nem rendelkezik. kör alakú aktor kérelmek; Ezek jelentős hatással lesz a teljesítmény és méretezhetőség.
- Ne keverje a kód aszinkron kóddal; szinkronizálása aszinkron egészen kell, a teljesítményproblémák elkerüléséhez szükséges.
- Nem-hívások indítása hosszabb ideig futó actors, blokkolja a többi hívások ugyanazon osztoznak a fordulókra párhuzamosság miatt.
- Használó más szolgáltatásokkal való kommunikáció [remoting Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) hoz létre, és egy `ServiceProxyFactory`, majd hozza létre az előállító, a [aktorszolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) szintet és *nem*aktor szintjén.


## <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
- Hozzáadás, a teljes körű [alkalmazásnaplózás](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) szolgáltatás hívásokban. És biztonságosabbá teszi a diagnosztikai forgatókönyvek, ahol szolgáltatások meghívása egymással. Például, ha A -> B -> C a hívás sikertelen lehet, bárhol; D -> Ha nem áll elég naplózási, meglehetősen nehéz diagnosztizálása érdekében. A szolgáltatások túl sok a beérkező hívást miatt jelentkezik, ha legalább mindenképpen hibák és figyelmeztetések naplózása.

## <a name="iot-and-messaging-applications"></a>IoT és üzenetküldő alkalmazások
Üzenetek olvasása során [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) vagy [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) használata [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) , amely integrálható a Service Fabric Reliable Services fenntartása érdekében a az Event Hubs olvasásakor állapotát particionálja, és új üzenetek leküldi a szolgáltatást a használatával a `IEventProcessor::ProcessEventsAsync()` metódust.


## <a name="design-guidance-on-azure"></a>Tervezési útmutatást az Azure-ban
* Látogasson el a [Azure architecture Centert](https://docs.microsoft.com/azure/architecture/microservices/) tervezési útmutatást [mikroszolgáltatások készítése az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/)

* Látogasson el [az Azure for Gaming](https://docs.microsoft.com/gaming/azure/) tervezési útmutatást [Service Fabric használatával a játékszolgáltatások](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
