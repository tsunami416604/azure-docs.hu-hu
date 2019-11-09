---
title: Az Azure Service Bus-üzenetkezelés áttekintése | Microsoft Docs
description: A Service Bus-üzenetkezelés ismertetése
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: e2460ab760811a3db39058eac74d519ca09046c6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889816"
---
# <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?

A Microsoft Azure Service Bus egy teljes mértékben felügyelt vállalati integrációs üzenetközvetítő. Service Bus elválaszthatja az alkalmazásokat és a szolgáltatásokat. A Service Bus megbízható és biztonságos platformot biztosít az aszinkron adatok és az állapotok átviteléhez.

Az adatok különböző alkalmazások és szolgáltatások közötti átvitele az *üzenetek* segítségével történik. Egy üzenet bináris formátumú, és tartalmazhat JSON, XML vagy csak szöveget. További információ: [Integration Services](https://azure.com/integration).

Néhány gyakori üzenetküldési forgatókönyv:

* *Üzenetkezelés*. Üzleti adatok (például értékesítési vagy beszerzési rendelések, naplók vagy leltározási mozgások) továbbítása.
* *Alkalmazások*leválasztása. Az alkalmazások és szolgáltatások megbízhatóságának és méretezhetőségének javítása. Az ügyfélnek és a szolgáltatásnak nem kell egyszerre online állapotban lennie.
* *Témakörök és előfizetések*. 1:*n* kapcsolatok engedélyezése a kiadók és az előfizetők között.
* *Üzenet-munkamenetek*. Olyan munkafolyamatok implementálása, amelyekhez üzenet-rendezés vagy üzenetek halasztása szükséges.

## <a name="namespaces"></a>Névterek

A névtér az összes üzenetküldési összetevő tárolója. Több várólista és témakör is lehet egyetlen névtérben, és a névterek gyakran alkalmazás-tárolóként szolgálnak.

## <a name="queues"></a>Üzenetsorok

Az üzenetek az *üzenetsorokba* érkeznek be, és onnan küldi ki őket a rendszer. A várólisták üzeneteket tárolnak, amíg a fogadó alkalmazás elérhetővé válik a fogadáshoz és a feldolgozáshoz.

![Várólista](./media/service-bus-messaging-overview/about-service-bus-queue.png)

A várólistákban lévő üzenetek megrendelése és időbélyege az érkezéskor. Az üzenet elfogadása után azt a rendszer egy redundáns tárolóban helyezi biztonságba. Az üzenetek *lekéréses* módban lesznek kézbesítve, csak az üzenetek kézbesítését kérik.

## <a name="topics"></a>Témakörök

Az üzenetek küldéséhez és fogadásához *témaköröket* is használhat. Amíg egy üzenetsort gyakran használnak közvetlen kommunikációra, addig a témakörök hasznosak a közzétételi/előfizetési forgatókönyvekben.

![Témakör](./media/service-bus-messaging-overview/about-service-bus-topic.png)

A témaköröknek több független előfizetése is lehet. Egy adott témakör előfizetője az adott témakörben küldött összes üzenetről kaphat másolatot. Az előfizetések névvel ellátott entitások. Az előfizetések továbbra is megmaradnak, de lejárnak vagy az autodeletenek is

Előfordulhat, hogy nem szeretné, hogy az egyes előfizetések megkapják a témakörbe küldött összes üzenetet. Ha igen, a *szabályok* és *szűrők* segítségével meghatározhatja a választható *műveleteket*kiváltó feltételeket. A megadott üzeneteket szűrheti, és beállíthatja vagy módosíthatja az üzenet tulajdonságait. További információ: a [szűrők és a műveletek](topic-filters.md)témakör.

## <a name="advanced-features"></a>Speciális funkciók

A Service Bus olyan speciális funkciókat tartalmaz, amelyek lehetővé teszik összetettebb üzenetkezelési problémák megoldását. A következő szakaszok ismertetik ezeket a funkciókat.

### <a name="message-sessions"></a>Üzenet-munkamenetek

Ha Service Bus-ben szeretne létrehozni egy beérkező, első kimenő (FIFO) garanciát, használja a munkameneteket. Az üzenet-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorrendjének együttes és rendezett kezelését. További információ [: Message Sessions: First in, First out (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Továbbítás

Az öntovábbító szolgáltatás egy várólistát vagy előfizetést láncok egy másik várólistára vagy témakörbe. Ugyanannak a névtérnek kell lenniük. Az automatikus továbbítással a Service Bus automatikusan eltávolítja az üzeneteket egy várólistából vagy előfizetésből, és egy másik várólistába vagy témakörbe helyezi őket. További információ: [Service Bus entitások Kiláncolása az autoforwarding szolgáltatással](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kézbesítetlen levelek várólistája

Service Bus támogatja a kézbesítetlen levelek várólistáját (DLQ). A DLQ olyan üzeneteket tart, amelyek nem továbbíthatók egyetlen fogadónak sem. A nem feldolgozható üzeneteket tárolja. Service Bus lehetővé teszi az üzenetek eltávolítását a DLQ, és megvizsgálhatja azokat. További információ: [Service Bus kézbesítetlen levelek várólistáinak áttekintése](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Ütemezett kézbesítés

Üzeneteket küldhet egy várólistába vagy témakörbe késleltetett feldolgozás céljából. Ütemezheti, hogy a feladatok egy adott időpontban egy rendszer általi feldolgozásra legyenek elérhetők. További információ: [ütemezett üzenetek](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Üzenetek halasztása

Egy üzenetsor vagy előfizetési ügyfél egy későbbi időpontra elhalaszthatja egy üzenet lekérését. Ezt a késleltetést az alkalmazás különleges körülményei okozhatják. Az üzenet a várólistán vagy az előfizetésen marad, de a készlet el van különítve. További információ: [üzenet halasztása](message-deferral.md).

### <a name="batching"></a>Kötegelés

Az ügyféloldali kötegek lehetővé teszik, hogy egy üzenetsor vagy egy témakör-ügyfél bizonyos ideig késleltetse az üzenetek küldését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. További információ: [ügyféloldali kötegelt feldolgozás](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Tranzakciók

Egy tranzakció két vagy több műveletet egyesít egy *végrehajtási hatókörbe*. Service Bus támogatja az egyetlen üzenetküldési entitások csoportosítási műveleteit egyetlen tranzakció hatókörén belül. Az üzenet entitás lehet üzenetsor, témakör vagy előfizetés. További információkért lásd: [Service Bus tranzakciók feldolgozásának áttekintése](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Szűrés és műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezek az üzenetek egy vagy több elnevezett előfizetési szabály formájában vannak megadva. Az előfizetés minden egyező szabály feltételéhez létrehoz egy másolatot az üzenetről, amely az egyes megfeleltetési szabályokhoz különbözőképpen is elvégezhető. További információ: a [szűrők és a műveletek](topic-filters.md)témakör.

### <a name="autodelete-on-idle"></a>Az autodelete inaktív állapotban

Az automatikus törlés üresjáratban beállítással megadhatja azt az üresjárati időközt, amely után a rendszer automatikusan törli a várólistát. A minimális érték 5 perc. További tudnivalókért tekintse meg a [QueueDescription. AutoDeleteOnIdle tulajdonságot](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Duplikálás észlelése

Egy hiba miatt előfordulhat, hogy az ügyfél kétségbe vonja a küldési művelet eredményét. A duplikált észlelés lehetővé teszi, hogy a küldő ugyanazzal az üzenettel küldje újra. Egy másik lehetőség, hogy a várólista vagy a témakör elvesse az ismétlődő másolatokat. További információ: [duplikált észlelés](duplicate-detection.md).

### <a name="security-protocols"></a>Biztonsági protokollok
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

A Service Bus támogatja az olyan biztonsági protokollokat, mint a [közös hozzáférésű jogosultságkód](service-bus-sas.md) (SAS), a [szerepköralapú hozzáférés-vezérlés](authenticate-application.md) (RBAC) és az [Azure-erőforrások felügyelt identitásai](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geo-vészhelyreállítás

Ha az Azure-régiók vagy-adatközpontok leállást tapasztalnak, a Geo-vész-helyreállítás lehetővé teszi az adatfeldolgozást, hogy egy másik régióban vagy adatközpontban folytassa További információ: [Azure Service Bus földrajzi katasztrófa utáni helyreállítás](service-bus-geo-dr.md).

### <a name="security"></a>Biztonság

A Service Bus támogatja a szabványos [AMQP 1.0](service-bus-amqp-overview.md) és [HTTP/REST](/rest/api/servicebus/) protokollokat.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Service Bus támogatja a [.net](https://github.com/Azure/azure-service-bus-dotnet/tree/master), a [Java](https://github.com/Azure/azure-service-bus-java/tree/master)és a [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client)-hez készült ügyféloldali kódtárakat.

## <a name="integration"></a>Integráció

A Service Bus teljes mértékben integrálható a következő Azure-szolgáltatásokkal:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>További lépések

A Service Bus-üzenetküldéssel való megismerkedéshez tekintse meg a következő cikkeket:

* Az Azure Messaging Services összehasonlításához tekintse meg [a szolgáltatások összehasonlítása](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)című témakört.
* Próbálja ki a [.net](service-bus-dotnet-get-started-with-queues.md), a [Java](service-bus-java-how-to-use-queues.md)vagy a [JMS](service-bus-java-how-to-use-jms-api-amqp.md)rövid útmutatóit.
* Service Bus-erőforrások kezeléséhez lásd: [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Ha többet szeretne megtudni a standard és a prémium szintekről és azok díjszabásáról, tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).
* A prémium szint teljesítményével és késésével kapcsolatos információkért lásd: [prémium szintű üzenetkezelés](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
