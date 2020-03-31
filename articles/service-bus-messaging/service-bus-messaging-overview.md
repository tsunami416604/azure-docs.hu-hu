---
title: Az Azure Service Bus-üzenetkezelés áttekintése | Microsoft Docs
description: Ez a cikk magas szintű áttekintést nyújt az Azure Service Bus, egy teljes körűen felügyelt vállalati integrációs üzenetközvetítő.
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
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240631"
---
# <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?

A Microsoft Azure Service Bus egy teljes mértékben felügyelt vállalati integrációs üzenetközvetítő. A Service Bus leválaszthatja az alkalmazásokat és szolgáltatásokat. A Service Bus megbízható és biztonságos platformot kínál az adatok és állapotok aszinkron továbbításához.

Az adatok különböző alkalmazások és szolgáltatások közötti átvitele az *üzenetek* segítségével történik. Az üzenetek bináris formátumúak, és Tartalmazhatnak JSON-t, XML-t vagy csak szöveget. További információ: [Integration Services](https://azure.com/integration).

Néhány gyakori üzenetküldési forgatókönyv:

* *Üzenetküldés*. Üzleti adatok, például eladási vagy beszerzési rendelések, naplók vagy készletmozgások átvitele.
* *Alkalmazások leválasztása*. Az alkalmazások és szolgáltatások megbízhatóságának és méretezhetőségének javítása. Az ügyfélnek és a szolgáltatásnak nem kell egyszerre online állapotban lennie.
* *Témák és előfizetések*. 1 engedélyezése:*n* kapcsolatok a közzétevők és az előfizetők között.
* *Üzenetmunkamenetek*. Üzenetrendezést vagy üzenethalasztást igénylő munkafolyamatok megvalósítása.

## <a name="namespaces"></a>Névterek

A névtér az összes üzenetküldési összetevő tárolója. Több várólisták és témakörök lehetnek egyetlen névtérben, és a névterek gyakran alkalmazástárolókként szolgálnak.

## <a name="queues"></a>Üzenetsorok

Az üzenetek az *üzenetsorokba* érkeznek be, és onnan küldi ki őket a rendszer. A várólisták addig tárolják az üzeneteket, amíg a fogadó alkalmazás elérhetővé nem válik azok fogadására és feldolgozására.

![Várólista](./media/service-bus-messaging-overview/about-service-bus-queue.png)

A várólistákban lévő üzenetek érkezéskor megvannak rendezve és időbélyeggel vannak ellátva. Az üzenet elfogadása után azt a rendszer egy redundáns tárolóban helyezi biztonságba. Az üzenetek *lekéréses* módban történnek, és csak kérésre kézbesítik az üzeneteket.

## <a name="topics"></a>Témakörök

Az üzenetek küldéséhez és fogadásához *témaköröket* is használhat. Amíg egy üzenetsort gyakran használnak közvetlen kommunikációra, addig a témakörök hasznosak a közzétételi/előfizetési forgatókönyvekben.

![Témakör](./media/service-bus-messaging-overview/about-service-bus-topic.png)

A témaköröknek több független előfizetése is lehet. Egy adott témakör előfizetője az adott témakörben küldött összes üzenetről kaphat másolatot. Az előfizetések nevesített entitások. Az előfizetések megmaradnak, de lejárnak vagy automatikusan törölhetők.

Előfordulhat, hogy nem szeretné, hogy az egyes előfizetések a témakörbe küldött összes üzenetet megkapják. Ha igen, *a szabályok* és *szűrők* segítségével meghatározhatja a választható műveleteket kiváltó *feltételeket.* Szűrheti a megadott üzeneteket, és beállíthatja vagy módosíthatja az üzenetek tulajdonságait. További információt a [Témakörszűrők és -műveletek című témakörben talál.](topic-filters.md)

## <a name="advanced-features"></a>Speciális funkciók

A Service Bus speciális szolgáltatásokat tartalmaz, amelyek lehetővé teszik az összetettebb üzenetkezelési problémák megoldását. Az alábbi szakaszok e szolgáltatások közül többet is lepleznek le.

### <a name="message-sessions"></a>Üzenet-munkamenetek

Először be, első kihelyezési (FIFO) garanciát a Service Bus,használja a munkamenetek. Az üzenet-munkamenetek lehetővé teszik a nem kötött kapcsolódó üzenetsorozatok együttes és rendezett kezelését. További információ: [Üzenetmunkamenetek: először be, először ki (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Automatikus továbbítás

Az automatikus továbbítási szolgáltatás egy várólistát vagy előfizetést egy másik várólistára vagy témakörbe láncolja. Ugyanannak a névtérnek a részei nek kell lenniük. Az automatikus továbbítással a Service Bus automatikusan eltávolítja az üzeneteket egy várólistából vagy előfizetésből, és egy másik várólistába vagy témakörbe helyezi őket. További információ: [Chaining Service Bus entitások autoforwarding.](service-bus-auto-forwarding.md)

### <a name="dead-letter-queue"></a>Kézbesítetlen levelek várólistája

A Service Bus támogatja a kézbesítetlen levelek várólistáját (DLQ). A DLQ olyan üzeneteket tartalmaz, amelyek et nem lehet kézbesíteni egyetlen címzettnek sem. Olyan üzeneteket tartalmaz, amelyeket nem lehet feldolgozni. A Service Bus lehetővé teszi az üzenetek eltávolítását a DLQ-ból, és azok vizsgálatát. További információ: [A Service Bus kézbesítetlen levelek várólistáinak áttekintése.](service-bus-dead-letter-queues.md)

### <a name="scheduled-delivery"></a>Ütemezett kézbesítés

A várólistába vagy témakörbe küldhet üzeneteket késleltetett feldolgozásra. Ütemezheti, hogy egy feladat egy adott időpontban elérhetővé váljon a rendszer általi feldolgozásra. További információt az Ütemezett üzenetek című [témakörben talál.](message-sequencing.md#scheduled-messages)

### <a name="message-deferral"></a>Üzenetek halasztása

A várólista- vagy előfizetés-ügyfél elhalaszthatja az üzenetek lekérését egy későbbi időpontra. Ez a halasztás oka lehet az alkalmazás különleges körülményeinek. Az üzenet a várólistában vagy az előfizetésben marad, de nincs félretéve. További információ: [Message halasztás](message-deferral.md).

### <a name="batching"></a>Kötegelés

Az ügyféloldali kötegelés lehetővé teszi egy üzenetsor vagy témakör ügyfél számára egy üzenet elküldésének adott ideig történő késleltetését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. További információ: [Ügyféloldali kötegelés](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Tranzakciók

Egy tranzakció két vagy több műveletet csoportosít egy *végrehajtási hatókörbe*. A Service Bus egyetlen üzenetküldő entitás csoporton belüli csoportosítási műveleteket támogat egyetlen tranzakció hatókörén belül. Az üzenetentitás lehet várólista, témakör vagy előfizetés. További információt [a Service Bus tranzakciófeldolgozás áttekintése](service-bus-transactions.md)című témakörben talál.

### <a name="filtering-and-actions"></a>Szűrés és műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezen üzenetek egy vagy több névvel ellátott előfizetési szabály formájában adhatók meg. Minden egyes egyező szabályfeltételhez az előfizetés létrehoz egy másolatot az üzenetből, amely minden egyes egyező szabályhoz eltérő jegyzeteket készíthet. További információt a [Témakörszűrők és -műveletek című témakörben talál.](topic-filters.md)

### <a name="autodelete-on-idle"></a>Automatikus törlés tétlenül

Az automatikus törlés tétlenjáratkor lehetővé teszi egy tétlen időköz megadását, amely után a várólista automatikusan törlődik. A minimális érték 5 perc. További információt a [QueueDescription.AutoDeleteOnIdle tulajdonságban talál.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)

### <a name="duplicate-detection"></a>Duplikálás észlelése

Egy hiba miatt az ügyfélnek kétségei lehetnek a küldési művelet kimenetelét illetően. A duplikáltelem-észlelés lehetővé teszi, hogy a feladó újraküldje ugyanazt az üzenetet. Egy másik lehetőség, hogy a várólista vagy a témakör elveti az ismétlődő másolatokat. További információ: [Duplikáltelem-észlelés](duplicate-detection.md).

### <a name="security-protocols"></a>Biztonsági protokollok
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

A Service Bus támogatja az olyan biztonsági protokollokat, mint a [közös hozzáférésű jogosultságkód](service-bus-sas.md) (SAS), a [szerepköralapú hozzáférés-vezérlés](authenticate-application.md) (RBAC) és az [Azure-erőforrások felügyelt identitásai](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geo-vészhelyreállítás

Az Azure-régiók vagy adatközpontok leállása esetében a Geo-vészhelyreállítás lehetővé teszi az adatfeldolgozási művelet folytatását egy másik régióban vagy adatközpontban. További információ: [Azure Service Bus Geo-katasztrófa-helyreállítási.](service-bus-geo-dr.md)

### <a name="security"></a>Biztonság

A Service Bus támogatja a szabványos [AMQP 1.0](service-bus-amqp-overview.md) és [HTTP/REST](/rest/api/servicebus/) protokollokat.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Service Bus támogatja a [.NET,](https://github.com/Azure/azure-service-bus-dotnet/tree/master) [Java](https://github.com/Azure/azure-service-bus-java/tree/master)és [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client)ügyfélkódtárakat.

## <a name="integration"></a>Integráció

A Service Bus teljes mértékben integrálható a következő Azure-szolgáltatásokkal:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>További lépések

A Service Bus-üzenetküldéssel való megismerkedéshez tekintse meg a következő cikkeket:

* Az Azure üzenetküldő szolgáltatásainak összehasonlításáról a [Szolgáltatások összehasonlítása (Összehasonlítása) (Szolgáltatások összehasonlítása) (Összehasonlítása) (A szolgáltatások összehasonlítása) (Szolgáltatások összehasonlítása) (A szolgáltatások](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* Próbálja ki a [.NET,](service-bus-dotnet-get-started-with-queues.md) [java](service-bus-java-how-to-use-queues.md)vagy JMS rövid [útmutatóit.](service-bus-java-how-to-use-jms-api-amqp.md)
* A Service Bus erőforrásainak kezeléséről a [Service Bus Explorer című témakörben lehet.](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* Ha többet szeretne megtudni a standard és prémium csomagokról és azok díjszabásáról, olvassa el a [Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/).
* A prémium szintű teljesítményről és késésről a [Prémium szintű üzenetküldés című témakörben](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)olvashat.
