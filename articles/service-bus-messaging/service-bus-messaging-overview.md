---
title: Az Azure Service Bus-üzenetkezelés áttekintése | Microsoft Docs
description: A Service Bus-üzenetkezelés ismertetése
services: service-bus-messaging
documentationcenter: ''
author: spelluru
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 09/22/2018
ms.custom: mvc
ms.author: spelluru
ms.openlocfilehash: 7b4045737ea2be466a436e6abb0de6a1a61e6a5b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636594"
---
# <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?

A Microsoft Azure Service Bus egy teljes mértékben felügyelt vállalati integrációs üzenetközvetítő. A Service Bust leggyakrabban az alkalmazások és szolgáltatások egymástól való elkülönítésére használják, továbbá az aszinkron adat- és állapotátvitel biztonságos platformjaként is alkalmazható. Az adatok különböző alkalmazások és szolgáltatások közötti átvitele az *üzenetek* segítségével történik. Bináris formátum, JSON, XML vagy csupán szöveget tartalmazó üzenet van. 

Néhány gyakori üzenetküldési forgatókönyv:

* Üzenetküldés: üzleti adatok átvitele, például értékesítési és vásárlási megrendelés, naplók vagy leltármozgások.
* Alkalmazások szétválasztása: alkalmazások és szolgáltatások méretezésének és megbízhatóságának növelése (az ügyfélnek és a szolgáltatásnak nem kell egyidejűleg online lennie).
* Témakörök és előfizetések: 1:*n* kapcsolatok lehetővé tétele a közzétevők és előfizetők között.
* Üzenet-munkamenetek: üzenetrendezést vagy üzenetek halasztását igénylő munkafolyamatok kivitelezése.

## <a name="namespaces"></a>Névterek

A névtér egy hatókörkezelési tároló az üzenetkezelés összes összetevője számára. Egyetlen névtér több üzenetsort és témakört tartalmazhat, és a névterek gyakran alkalmazástárolókként is szolgálnak.

## <a name="queues"></a>Üzenetsorok

Az üzenetek az *üzenetsorokba* érkeznek be, és onnan küldi ki őket a rendszer. Az üzenetsorok segítségével addig tárolhatja az üzeneteket, amíg a fogadó alkalmazás elérhetővé nem válik a fogadásra és feldolgozásra.

![Várólista](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Az üzenetsorokban található üzeneteket a rendszer a beérkezéskor rendezi és időbélyeggel látja el. Miután elfogadták, az üzenet tárolt biztonságosan redundáns tárolást. Üzenetek kézbesítése *lekéréses* üzemmódot, amely a kérést továbbítja az üzeneteket.

## <a name="topics"></a>Témakörök

Az üzenetek küldéséhez és fogadásához *témaköröket* is használhat. Amíg egy üzenetsort gyakran használnak közvetlen kommunikációra, addig a témakörök hasznosak a közzétételi/előfizetési forgatókönyvekben.

![Témakör](./media/service-bus-messaging-overview/about-service-bus-topic.png)

A témaköröknek több független előfizetése is lehet. Egy adott témakör előfizetője az adott témakörben küldött összes üzenetről kaphat másolatot. Az előfizetések tartós használatra készült névvel ellátott entitások, amelyek adott esetben lejárhatnak vagy automatikusan törlődhetnek.

Bizonyos esetekben nem célszerű az, ha az egyes előfizetések az adott témakörben beérkező összes üzenetet megkapják. Ilyen esetben [szabályokat és a szűrőket](topic-filters.md) használhat arra, hogy választható [műveleteket](topic-filters.md#actions) kiváltó feltételeket határozzon meg, szűrjön bizonyos üzenetekre, valamint beállítsa és módosítsa az üzenetek tulajdonságait.

## <a name="advanced-features"></a>Speciális funkciók

A Service Bus olyan speciális funkciókkal is rendelkezik, amelyek segítségével jóval összetettebb üzenetküldési problémákat is megoldhat. A következő szakaszok ezeket a funkciókat ismertetik:

### <a name="message-sessions"></a>Üzenet-munkamenetek

Az érkezési sorrend alapján (FIFO) törtnő feldolgozás garantálásához a Service Busban munkameneteket használhat. Az [üzenet-munkamenetek](message-sessions.md) lehetővé teszik a nem kötött kapcsolódó üzenetsorozatok együttes és rendezett kezelését. 

### <a name="auto-forwarding"></a>Automatikus továbbítás

Az [automatikus-továbbítás](service-bus-auto-forwarding.md) funkció lehetővé teszi önnek egy üzenetsor vagy előfizetés hozzákapcsolását egy szintén ugyanazon névtér részét képező másik üzenetsorhoz vagy témakörhöz. Ha az automatikus-továbbítás engedélyezve van, a Service Bus automatikusan törli azon üzeneteket, amelyek az első üzenetsorba vagy előfizetésbe (forrás) kerülnek, és áthelyezi őket a második üzenetsorba vagy témakörbe (cél).

### <a name="dead-lettering"></a>Kézbesíthetetlen levelek

A Service Bus támogatja a [kézbesíthetetlen levelek várólistáját](service-bus-dead-letter-queues.md) (DLQ), hogy az egyetlen fogadóhoz sem továbbított vagy feldolgozhatatlan üzenetek tárolása is meg legyen oldva. Ezután eltávolíthatja az üzeneteket a kézbesíthetetlen levelek várólistájáról, és megvizsgálhatja azokat.

### <a name="scheduled-delivery"></a>Ütemezett kézbesítés

Az üzeneteket [késleltetett feldolgozásra](message-sequencing.md#scheduled-messages) is beadhatja az üzenetsorba vagy témakörbe például azért, hogy egy ütemezett feladattal az üzenetek elérhetővé váljanak egy másik rendszer általi feldolgozásra egy bizonyos időpontban.

### <a name="message-deferral"></a>Üzenetek halasztása

Amikor egy üzenetsor vagy előfizetés ügyfele olyan üzenetet kap, amelyet hajlandó feldolgozni, azonban a feldolgozás az aktuális alkalmazáson belüli különleges körülményekből adódóan nem lehetséges, az entitásnak lehetősége van az [üzenet lekérésének késleltetésére](message-deferral.md). Az üzenet az üzenetsorban vagy előfizetésben marad, de a rendszer félreteszi azt.

### <a name="batching"></a>Kötegelés

Az [ügyféloldali kötegelés](service-bus-performance-improvements.md#client-side-batching) lehetővé teszi egy üzenetsor vagy témakör ügyfél számára egy üzenet elküldésének adott ideig történő késleltetését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. 

### <a name="transactions"></a>Tranzakciók

Egy [tranzakció](service-bus-transactions.md) két vagy több műveletet kapcsol össze egyetlen végrehajtási hatókörbe. A Service Bus támogatja az egyetlen üzenetküldő entitásra (üzenetsor, témakör, előfizetés) irányuló csoportosítási műveleteket egy tranzakció hatáskörén belül.

### <a name="filtering-and-actions"></a>Szűrés és műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezen üzenetek egy vagy több [névvel ellátott előfizetési szabály](topic-filters.md) formájában adhatók meg. Az előfizetés megfelelő szabályfeltételenként létrehoz egy üzenetmásolatot, amelyek azután megfelelő szabályonként más és más jelöléssel láthatók el.

### <a name="auto-delete-on-idle"></a>Automatikus törlés tétlenség esetén

Az [Automatikus törlés tétlenség esetén](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) lehetővé teszi, hogy meghatározzon egy tétlenségi időtartamot, amelyet követően az üzenetsor automatikus törlődik. A minimális érték 5 perc.

### <a name="duplicate-detection"></a>Duplikálás észlelése

Ha hiba lép fel, amelyek hatására az ügyfél-küldési művelet eredményéről kétségei vannak [észlelési ismétlődő](duplicate-detection.md) vesz igénybe a bizonytalan kívül ezekben a helyzetekben a küldőt, hogy küldje újra ugyanazt az üzenetet, és az üzenetsor vagy témakör engedélyezésével minden ismétlődő példánya elveti.

### <a name="sas-rbac-and-managed-identities-for-azure-resources"></a>SAS, RBAC és az Azure-erőforrások felügyelt identitásai

A Service Bus támogatja az olyan biztonsági protokollokat, mint a [közös hozzáférésű jogosultságkód](service-bus-sas.md) (SAS), a [szerepköralapú hozzáférés-vezérlés](service-bus-role-based-access-control.md) (RBAC) és az [Azure-erőforrások felügyelt identitásai](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geo-vészhelyreállítás

Az Azure-régiók vagy adatközpontok leállása esetében a [Geo-vészhelyreállítás](service-bus-geo-dr.md) lehetővé teszi az adatfeldolgozási művelet folytatását egy másik régióban vagy adatközpontban.

### <a name="security"></a>Biztonság

A Service Bus támogatja a szabványos [AMQP 1.0](service-bus-amqp-overview.md) és [HTTP/REST](/rest/api/servicebus/) protokollokat.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Service Bus támogatja a [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master) és [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client) ügyfélkódtárakat.

## <a name="integration"></a>Integráció

A Service Bus teljes mértékben integrálható a következő Azure-szolgáltatásokkal:

- [Event Grid](https://azure.microsoft.com/services/event-grid/) 
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 
- [Functions](https://azure.microsoft.com/services/functions/) 
- [Dynamics 365](https://dynamics.microsoft.com)
- [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)
 
## <a name="next-steps"></a>További lépések

A Service Bus-üzenetküldéssel való megismerkedéshez tekintse meg a következő cikkeket:

* [Azure üzenetküldő szolgáltatásainak összehasonlítása](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* További tudnivalók az Azure Service Bus [Standard és Prémium](https://azure.microsoft.com/pricing/details/service-bus/) szintjeiről és azok díjszabásáról
* [Az Azure Service Bus Prémium szintű csomag teljesítménye és késése](https://blogs.msdn.microsoft.com/servicebus/2016/07/18/premium-messaging-how-fast-is-it/)
* Próbálja ki a [.NET-hez](service-bus-quickstart-powershell.md), [Javához](service-bus-quickstart-powershell.md) vagy [JMS-hez](service-bus-quickstart-powershell.md) készült gyors útmutatókat
