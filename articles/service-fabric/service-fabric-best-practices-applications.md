---
title: Az Azure Service Fabric alkalmazástervezési gyakorlati tanácsok
description: Gyakorlati tanácsok és tervezési szempontok az Azure Service Fabric használatával történő alkalmazások és szolgáltatások fejlesztéséhez.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 56df6e28940eb15597a3d6bccca3f85e5f690f89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991654"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Az Azure Service Fabric alkalmazástervezési gyakorlati tanácsok

Ez a cikk az Azure Service Fabric-en alkalmazások és szolgáltatások létrehozásához ajánlott eljárásokat ismerteti.
 
## <a name="get-familiar-with-service-fabric"></a>Ismerkedjen meg a Service Fabric szolgáltatással
* Olvassa el a [Tehát szeretne többet megtudni a Service Fabric?](service-fabric-content-roadmap.md) cikket.
* Olvassa el a [Service Fabric alkalmazásforgatókönyveit.](service-fabric-application-scenarios.md)
* A programozási modell választási lehetőségeinek megismerése a [Service Fabric programozási modell áttekintésének olvasásával.](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Alkalmazástervezési útmutató
Ismerkedjen meg a Service Fabric-alkalmazások [általános architektúrájával](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) és [tervezési szempontjaival.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)

### <a name="choose-an-api-gateway"></a>API-átjáró kiválasztása
Használjon egy API-átjáró szolgáltatást, amely kommunikál a háttérszolgáltatások, amelyek ezután horizontálisan kiskálázható. A leggyakrabban használt API-átjárószolgáltatások a következők:

- [Az Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), amely integrálva van [a Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Az Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) vagy az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)használatával a [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) olvasni Event Hub partíciók.
- [Træfik fordított proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), az [Azure Service Fabric-szolgáltató](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/)használatával.
- [Az Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/)alkalmazást.

   > [!NOTE] 
   > Azure Application Gateway isn't directly integrated with Service Fabric. Az Azure API Management általában az előnyben részesített választás.
- Saját, egyedi legeltetésű [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) webalkalmazás-átjáróval.

### <a name="stateless-services"></a>Állapotnélküli szolgáltatások
Azt javasoljuk, hogy mindig kezdje állapotmentes szolgáltatások létrehozása [a Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) használatával, és az állapot tárolása egy Azure-adatbázisban, az Azure Cosmos DB vagy az Azure Storage. A külső állapot a legtöbb fejlesztő számára ismerősebb megközelítés. Ez a megközelítés azt is lehetővé teszi, hogy kihasználja a lekérdezési képességek a tárolóban.  

### <a name="when-to-use-stateful-services"></a>Mikor kell igénybe tenni az állapotalapú szolgáltatásokat?
Fontolja meg az állapotalapú szolgáltatások, ha van egy forgatókönyv alacsony késésű, és az adatokat a számítási közel kell tartania. Néhány példa forgatókönyvek közé tartozik az IoT digitális ikereszközök, játék állapota, munkamenet-állapot, gyorsítótárazás i adatok at egy adatbázisból, és a hosszú ideig futó munkafolyamatok nyomon követésére hívások más szolgáltatások.

Döntse el az adatmegőrzési időkeretet:

- **Gyorsítótárazott adatok**. Akkor használja a gyorsítótárazást, ha a külső tárolókkkkba való késés problémát jelent. Használjon állapotalapú szolgáltatást saját adatgyorsítótárként, vagy fontolja meg a [nyílt forráskódú SoCreate Service Fabric elosztott gyorsítótár használatát.](https://github.com/SoCreate/service-fabric-distributed-cache) Ebben az esetben nem kell aggódnia, ha elveszíti a gyorsítótárban lévő összes adatot.
- **Időhöz kötött adatok**. Ebben a forgatókönyvben az adatokat a késés ideje alatt közel kell tartania a számításhoz, de megengedheti magának, hogy az adatok at vészelje el egy *katasztrófa esetén.* Például számos IoT-megoldásban az adatoknak közel kell lenniük a számításhoz, például amikor az elmúlt néhány nap átlaghőmérsékletét kiszámítják, de ha ezek az adatok elvesznek, a rögzített konkrét adatpontok nem olyan fontosak. Ebben a forgatókönyvben általában nem érdekli az egyes adatpontok biztonsági mentése. Csak a külső tárolóba rendszeresen írt számított átlagértékekről lehet biztonsági másolatot tartani.  
- **Hosszú távú adatok**. A megbízható gyűjtemények véglegesen tárolhatják az adatokat. Ebben az esetben azonban fel kell [készülnie a vész-helyreállításra,](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)beleértve a fürtök [időszakos biztonsági mentési szabályzatainak konfigurálását](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) is. Valójában konfigurálja, hogy mi történik, ha a fürt megsemmisül egy katasztrófa, ahol új fürtöt kell létrehoznia, és hogyan kell telepíteni az új alkalmazáspéldányokat, és a legújabb biztonsági mentésből helyreállítani.

Takarítson meg költségeket és javítsa a rendelkezésre állást:
- Csökkentheti a költségeket az állapotalapú szolgáltatások használatával, mert nem merülnek fel adatelérési és tranzakciós költségek a távoli tárolóból, és mert nem kell használnia egy másik szolgáltatást, például az Azure Cache for Redis.
- Az állapotalapú szolgáltatások elsősorban a tárolás, és nem a számítási költséges, és nem javasoljuk. Gondoljon az állapotalapú szolgáltatásokra, mint az olcsó helyi tárolással rendelkező számításra.
- A más szolgáltatásoktól való függőségek eltávolításával javíthatja a szolgáltatás rendelkezésre állását. Az állapot kezelése a FÜRTHA-val elkülöníti Önt a szolgáltatás egyéb leállási vagy késési problémáitól.

## <a name="how-to-work-with-reliable-services"></a>A megbízható szolgáltatások kal való munka
A Service Fabric megbízható szolgáltatások lehetővé teszi, hogy könnyen hozzon létre állapotnélküli és állapotalapú szolgáltatások. További információt a [Megbízható szolgáltatások című témakörben talál.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Mindig tartsa tiszteletben `RunAsync()` a [lemondási jogkivonatot](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) az `ChangeRole()` állapotnélküli és állapotalapú szolgáltatások és az állapotalapú szolgáltatások metódusában. Ha nem, a Service Fabric nem tudja, ha a szolgáltatás bezárható. Ha például nem tartja tiszteletben a törlési jogkivonatot, sokkal hosszabb alkalmazásfrissítési idő fordulhat elő.
-    A [kommunikációfigyelők időben](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) megnyitása és bezárása, és a lemondási jogkivonatok tiszteletben
-    Soha ne keverje a szinkronizálási kódot az aszinkron kóddal. Például ne használja `.GetAwaiter().GetResult()` az aszinkron hívásokban. Használja az aszinkront *végig a* hívási veremben.

## <a name="how-to-work-with-reliable-actors"></a>Hogyan működjön együtt megbízható szereplők
A Service Fabric reliable actors lehetővé teszi, hogy könnyen hozzon létre állapotalapú, virtuális szereplők. További információt a [Megbízható szereplők című témakörben talál.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Komolyan fontolja meg a pub/sub messaging a szereplők között az alkalmazás méretezéséhez. A szolgáltatást biztosító eszközök közé tartozik a [nyílt forráskódú SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) és az [Azure Service Bus.](https://docs.microsoft.com/azure/service-bus/)
- Az aktor állapotának a [lehető legszemcsésebbé.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)
- A [szereplő életciklusának](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)kezelése . Törölje a színészeket, ha nem használja őket újra. A szükségtelen szereplők törlése különösen fontos az [illékony állapotszolgáltató](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)használatakor, mert az összes állapot a memóriában van tárolva.
- A [fordítva való egyidejűségük](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)miatt a szereplőket leginkább független objektumként lehet használni. Ne hozzon létre grafikonokat többszereplős, szinkron metódushívásokról (amelyek mindegyike valószínűleg külön hálózati hívássá válik), és ne hozzon létre körkörös aktorkéréseket. Ezek jelentősen befolyásolják a teljesítményt és a skálát.
- Ne keverje a szinkronizálási kódot az aszinkron kóddal. Az async használatával megelőzheti a teljesítménnyel kapcsolatos problémákat.
- Ne telefonálj hosszú ideig a színészekben. A hosszú ideig futó hívások blokkolják az ugyanazon aktorhoz irányuló más hívásokat a többalapú egyidejűség miatt.
- Ha más szolgáltatásokkal kommunikál a [Service Fabric-alapú átirányító](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) használatával, `ServiceProxyFactory`és létrehoz egy, hozzon létre egy gyárat az [aktor-szolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) szintjén, és *nem* az aktor szintjén.


## <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
Legyen alapos az [alkalmazásnaplózás](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) hozzáadása a szolgáltatáshívásokhoz. Ez segít diagnosztizálni forgatókönyvek, amelyben a szolgáltatások hívják egymást. Ha például "A" "B" hívásokat hív D-t, a hívás bárhol sikertelen lehet. Ha nincs elég naplózási, hibák nehéz diagnosztizálni. Ha a szolgáltatások túl sokat naplóznak a hívási kötetek miatt, győződjön meg róla, hogy legalább naplózza a hibákat és figyelmeztetéseket.

## <a name="iot-and-messaging-applications"></a>IoT- és üzenetküldő alkalmazások
Amikor üzeneteket olvas az [Azure IoT Hubról](https://docs.microsoft.com/azure/iot-hub/) vagy [az Azure Event Hubs-ból,](https://docs.microsoft.com/azure/event-hubs/)használja a [ServiceFabricProcessor szolgáltatást.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor) ServiceFabricProcessor integrálja a Service Fabric megbízható szolgáltatások állapotának fenntartása az eseményközpont-partíciók olvasása, és leküldi az új üzeneteket a szolgáltatások a `IEventProcessor::ProcessEventsAsync()` metóduson keresztül.


## <a name="design-guidance-on-azure"></a>Tervezési útmutató az Azure-on
* Látogasson el az [Azure architektúrás központjába,](https://docs.microsoft.com/azure/architecture/microservices/) ahol tervezési útmutatást talál [az Azure-beli mikroszolgáltatások létrehozásához.](https://docs.microsoft.com/azure/architecture/microservices/)

* Az [Azure for Gaming használatának első lépései](https://docs.microsoft.com/gaming/azure/) és a Service Fabric játékszolgáltatásokban való használatával kapcsolatos tervezési útmutatásért látogasson el az Első lépések [benyújtásra.](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
