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
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203458"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Az Azure Service Fabric application tervezés – ajánlott eljárások

A cikk ismerteti az ajánlott eljárásokkal kapcsolatos útmutatás alkalmazások és szolgáltatások létrehozásához az Azure Service Fabricban.
 
## <a name="get-familiar-with-service-fabric"></a>Ismerkedjen meg a Service Fabric
* Olvassa el a [ezért ismerje meg a Service Fabric?](service-fabric-content-roadmap.md) cikk.
* További információ [Service Fabric-alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md).
* Olvassa el a programozási modell lehetőségek ismertetése [Service Fabric programozási modell áttekintése](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Alkalmazás-tervezési segédlet
A megismerése a [általános architektúra](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) a Service Fabric-alkalmazások és azok [kialakítási szempontok](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Válassza ki az API-átjáró
API-átjáró szolgáltatás, amely kommunikál majd kiterjeszthető háttér-szolgáltatásokhoz való használatához. A leggyakrabban használt API-átjáró szolgáltatásai használni a következők:

- [Az Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), amely [integrálva van a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Az Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) vagy [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)révén a [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) olvasásához az Event Hubs-partíciók.
- [A fordított proxy Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)révén a [Azure Service Fabric-szolgáltató](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Az Azure Application Gateway nem közvetlenül integrálva a Service Fabric. Az Azure API Management akkor jellemzően az előnyben részesített választani.
- A saját személyre szabott [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web application gateway.

### <a name="stateless-services"></a>Állapotmentes szolgáltatások
Azt javasoljuk, hogy mindig megkezdése az állapotmentes szolgáltatások létrehozásával [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) és az állapot egy Azure database, az Azure Cosmos DB-hez vagy az Azure Storage tárolja. Externalized állapota a legtöbb fejlesztő több jól ismert módszere. Ez a megközelítés lehetővé teszi a tároló lekérdezési képességek előnyeit.  

### <a name="when-to-use-stateful-services"></a>Mikor érdemes használni az állapotalapú szolgáltatások
Állapotalapú szolgáltatások fontolja meg, ha az alacsony késés érdekében egy forgatókönyvet, és szeretné megőrizni az adatokat a számítási közel. Néhány példa forgatókönyvet a digitális ikereszköz IoT-eszközök, a játék állapotát, a munkamenet-állapot gyorsítótárazása egy adatbázis és a hosszan futó munkafolyamatok nyomon követéséhez-hívások egyéb adatait tartalmazza.

Döntse el, az adatok megőrzési időkeretet:

- **Gyorsítótárazott adatokat**. Használjon gyorsítótárazást, ha külső áruházak késés problémát. Egy állapotalapú service használata a saját adatgyorsítótár, vagy vegye fontolóra a [nyílt forráskódú SoCreate Service Fabric elosztott gyorsítótár](https://github.com/SoCreate/service-fabric-distributed-cache). Ebben a forgatókönyvben, nem kell lenniük az érintett a gyorsítótárban lévő összes adat elvesztése esetén.
- **Időhöz kötött adatok**. Ebben a forgatókönyvben tartsa zárja be az adatok számítási késése egy ideig szüksége, de Ön engedhet az adatok egy *vészhelyreállítási*. Számos IoT-megoldások, például adatokat kell lennie a számítási, például ha az átlaghőmérséklet az elmúlt néhány napra van számítja ki, zárja be, de ezek az adatok nem vesztek el, ha az adott adatpontok rögzített fontos, hogy nem. Emellett ebben a forgatókönyvben nem általában olyan fontos a külön adatpontok biztonsági mentéséről. Csak készítsen számított átlagos értékek, rendszeres időközönként írt külső tárhelyen.  
- **Hosszú távú adatok**. A Reliable collections véglegesen tárolhatja az adatokat. Azonban ebben az esetben kell [Felkészülés vészhelyreállításra](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), többek között [rendszeres biztonsági mentési szabályzatok konfigurálása](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) a fürtök számára. Konfigurálja, mi történik, ha a fürt megsemmisülésekor a katasztrófa, hol kell hozzon létre egy új fürtöt és hogyan helyezhet üzembe új alkalmazáspéldányok, és helyreállíthatja a legutóbbi biztonsági mentés.

Csökkentheti költségeit és javítható a rendelkezésre állás:
- Állapotalapú szolgáltatások használatával, mert nem díjak adatelérési és tranzakciók költségek csökkentése a távoli tárolóból, és hogy nem kell használni egy másik szolgáltatás, például az Azure Cache Redis.
- Az állapotalapú szolgáltatások, elsősorban a tárolási és számítási a nem a következő költséges, és nem ajánlott. Úgy gondolja, hogy az állapotalapú szolgáltatások számítási olcsó helyi tárterülettel rendelkező.
- Függőségek a más szolgáltatások eltávolítása után a szolgáltatás rendelkezésre állása növelhető. A fürt magas rendelkezésre ÁLLÁSÚ állapot kezelése közt megadott kezdőkönyvtárra korlátozása, más szolgáltatás leállás vagy késési problémák.

## <a name="how-to-work-with-reliable-services"></a>A Reliable Services használata
Service Fabric Reliable Services lehetővé teszi, hogy könnyedén hozhat létre az állapot nélküli és állapotalapú szolgáltatások. További információkért lásd: a [Bevezetés a Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Mindig tartsa tiszteletben a [megszakítás jogkivonat](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) a a `RunAsync()` metódus az állapot nélküli és állapotalapú szolgáltatások és a `ChangeRole()` metódus az állapotalapú szolgáltatások esetében. Ha nem, akkor a Service Fabric nem ismert, ha a szolgáltatás be lehet zárni. Például ha nem fogadja el a a megszakítás jogkivonatot, sokkal több alkalmazás frissítési alkalommal fordulhat elő.
-   Megnyitás és Bezárás [kommunikációs figyelőket](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) időben módon, és figyelembe veszi a megszakítási tokeneket.
-   Soha ne keverje szinkronizálási kód aszinkron kóddal. Például ne használjon `.GetAwaiter().GetResult()` az async-hívásokban. Használjon aszinkron *egészen* keresztül a hívási veremben.

## <a name="how-to-work-with-reliable-actors"></a>Reliable Actors használatának módját
Service Fabric Reliable Actors segítségével könnyedén hozhat létre az állapotalapú és virtuális actors. További információkért lásd: a [Reliable Actors bemutatása](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Komolyan fontolja meg az alkalmazás méretezése az actors közötti üzenetek pub/sub. Ezt a szolgáltatást biztosító eszközök közé tartozik a [nyílt forráskódú SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) és [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Győződjön meg arról, mint az aktorok állapotának [részletes lehető](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Kezelheti a [szereplő életciklusának](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Törölje az actors, ha nem fog újból használják azt. A felesleges actors törlése különösen fontos használatakor a [felejtő riasztásiállapot-szolgáltató](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), mert a memóriában tárolt összes állapotát.
- Mert azok [fordulókra egyidejűségi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), actors használhatók a legjobban független objektumként. Nem több színész, a szinkron metódust hívja, (, amelyek a legnagyobb valószínűséggel lesz egy különálló hálózati hívást) diagramjait létrehozni, vagy hozzon létre. kör alakú aktor kérelmeket. Ezek jelentős hatással lesz a teljesítmény és méretezhetőség.
- Ne keverje a kód aszinkron kóddal szinkronizálása. Aszinkron használja következetesen teljesítménybeli problémák elkerülése érdekében.
- Ne actors hosszú lefutású hívásokat. A hosszú lefutású hívásokat letiltja a más intézett hívások ugyanazon osztoznak a fordulókra párhuzamosság miatt.
- A más szolgáltatásokkal való kommunikáció van [remoting Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) hoz létre, és egy `ServiceProxyFactory`,-előállító létrehozása a [aktorszolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) szint és *nem* az aktor szintjén.


## <a name="application-diagnostics"></a>Az Application diagnostics
Teljes körű hozzáadásával kapcsolatos [alkalmazásnaplózás](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) szolgáltatás hívásokban. Segít diagnosztizálni a forgatókönyvek, amelyben a szolgáltatások meghívása egymással. Ha például egy meghívja a B hívások C D hív meg, amikor a hívás sikertelen lehet, bárhol. Ha nem rendelkezik elég naplózási, hibák biztonságosak az olyan diagnosztizálása érdekében. Ha túl sok a beérkező hívást miatt jelentkeznek a szolgáltatások, ügyeljen arra, legalább a hibák és figyelmeztetések naplózása.

## <a name="iot-and-messaging-applications"></a>IoT és üzenetküldő alkalmazások
Amikor acélja épp üzeneteit [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) vagy [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), használja [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integrálható a Service Fabric Reliable Services az event hubs olvasó állapota particionálja, és új üzenetek leküldi a szolgáltatást a használatával a `IEventProcessor::ProcessEventsAsync()` metódust.


## <a name="design-guidance-on-azure"></a>Tervezési útmutatást az Azure-ban
* Látogasson el a [Azure architecture Centert](https://docs.microsoft.com/azure/architecture/microservices/) tervezési útmutatást [mikroszolgáltatások készítése az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/).

* Látogasson el [az Azure for Gaming](https://docs.microsoft.com/gaming/azure/) tervezési útmutatást [Service Fabric használatával a játékszolgáltatások](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
