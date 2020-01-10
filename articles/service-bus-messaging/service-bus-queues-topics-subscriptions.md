---
title: Üzenetkezelés – várólisták, témakörök és előfizetések Azure Service Bus
description: Service Bus üzenetkezelési entitások áttekintése.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 9ac8d95dda392bd3f2a438389f5f6aa434b8a2fa
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772142"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)

Microsoft Azure Service Bus támogatja a felhőalapú, Message-orientált middleware-technológiákat, beleértve a megbízható üzenetsor-kezelési és tartós közzétételi/előfizetési üzeneteket. Ezek a "felügyelt" üzenetkezelési képességek olyan leválasztott üzenetkezelési funkciók, amelyek támogatják a közzétételi-előfizetéseket, az időbeli leválasztást és a terheléselosztási forgatókönyveket a Service Bus üzenetkezelési számítási feladattal. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

A Service Bus üzenetkezelési képességeinek magját alkotó üzenetkezelési entitások várólisták, témakörök és előfizetések, valamint szabályok/műveletek.

## <a name="queues"></a>Üzenetsorok

A várólisták egy vagy több versengő fogyasztó számára biztosítanak *első, első kimenő* (FIFO) üzenet kézbesítését. Ez azt eredményezi, hogy a fogadók általában abban a sorrendben fogadják és dolgozzák fel az üzeneteket, ahogy azokat hozzáadták a várólistához, és csak egy üzenet fogyasztója fogad és dolgoz fel minden üzenetet. A várólisták használatának egyik fő előnye az alkalmazás-összetevők időbeli leválasztása. Ez azt jelenti, hogy a gyártóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell egyszerre küldeniük és fogadniuk az üzeneteket, mert az üzenetek a tartósan vannak tárolva a várólistában. Emellett a termelőnek nem kell megvárnia a fogyasztó válaszát, hogy továbbra is feldolgozza és elküldhesse az üzeneteket.

A kapcsolódó juttatás "load szintező", amely lehetővé teszi, hogy a termelők és a fogyasztók különböző díjszabású üzeneteket küldjenek és fogadnak. Számos alkalmazásban a rendszerterhelés az idő múlásával változik; azonban az egyes munkaegységekhez szükséges feldolgozási idő jellemzően állandó. Az közvetítő és a fogyasztóknak egy üzenetsor azt jelenti, hogy a felhasználható alkalmazást csak úgy kell kiépíteni, hogy a maximális terhelés helyett az átlagos terhelést tudja kezelni. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ez a funkció közvetlenül az alkalmazás terhelésének kiszolgálásához szükséges infrastruktúra mennyiségével kapcsolatos pénzt takarít meg. A terhelés növekedésével több munkavégző folyamat is felvehető a várólistából való olvasáshoz. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Emellett ez a lekéréses terheléselosztás lehetővé teszi a munkavégző számítógépek optimális használatát, még akkor is, ha a feldolgozó számítógépek eltérnek a feldolgozási teljesítménytől, ahogy az üzeneteket a saját maximális díjszabás szerint lekérik. Ezt a mintát gyakran a "versengő fogyasztó" mintát nevezzük.

A várólisták és a felhasználók közti közti kapcsolat lehetővé teszi az összetevők közötti, laza összekapcsolást. Mivel a termelők és a fogyasztók nem ismerik egymást, a fogyasztókat a gyártóra gyakorolt hatás nélkül lehet frissíteni.

### <a name="create-queues"></a>Várólisták létrehozása

A várólistákat a [Azure Portal](service-bus-quickstart-portal.md), a [PowerShell](service-bus-quickstart-powershell.md), a [CLI](service-bus-quickstart-cli.md)vagy a [Resource Manager-sablonok](service-bus-resource-manager-namespace-queue.md)segítségével hozhatja létre. Ezután [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum használatával küldhet és fogadhat üzeneteket.

Ha szeretné gyorsan megismerni, hogyan lehet várólistát létrehozni, majd üzeneteket küldeni és fogadni a várólistáról, tekintse [meg az egyes](service-bus-quickstart-portal.md) módszerek rövid útmutatóit. A várólisták használatával kapcsolatos részletesebb oktatóanyagért tekintse meg a [Service Bus Queues használatának első lépéseivel](service-bus-dotnet-get-started-with-queues.md)foglalkozó témakört.

Egy működő minta esetében tekintse meg a [BasicSendReceiveUsingQueueClient mintát](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) a githubon.

### <a name="receive-modes"></a>Fogadási módok

Két különböző módot is megadhat, amelyekben a Service Bus üzeneteket fogad: *ReceiveAndDelete* vagy *PeekLock*. A [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban a fogadási művelet egyetlen-shot; Ez azt jelenti, hogy amikor a Service Bus fogadja a fogyasztótól érkező kérést, az üzenetet felhasználva jelzi, és visszaadja a fogyasztó alkalmazásnak. A **ReceiveAndDelete** mód a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor az alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A forgatókönyv megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva jelöli meg, amikor az alkalmazás újraindul, és megkezdi az üzenetek újrafelhasználását, az összeomlás előtt felhasznált üzenetet kihagyta.

[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) módban a fogadási művelet kétlépcsős lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor Service Bus megkapja a kérést, megkeresi a következő felvenni kívánt üzenetet, zárolja, hogy más fogyasztó ne fogadja el, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja azt a későbbi feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) meghívásával a kapott üzeneten. Ha Service Bus látja a **CompleteAsync** hívást, az üzenetet a rendszer felhasználva jelöli meg.

Ha az alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, meghívja a [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust a fogadott üzeneten ( [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)helyett). Ez a módszer lehetővé teszi az Service Bus számára az üzenet zárolásának feloldását, és elérhetővé tételét, akár ugyanazon fogyasztó, akár egy másik versengő fogyasztó számára. Másodszor a zároláshoz időtúllépés van társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus feloldja az üzenetet, és ismét elérhetővé válik (lényegében [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) műveletet hajt végre alapértelmezés szerint).

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a **CompleteAsync** -kérelem kiadása előtt, a rendszer az újraindításkor visszaküldi az üzenetet az alkalmazásnak. Ezt a folyamatot gyakran nevezik *legalább egyszer* feldolgozásra; azaz minden üzenet feldolgozása legalább egyszer megtörténik. Bizonyos helyzetekben azonban előfordulhat, hogy ugyanazt az üzenetet újra lehet küldeni. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, akkor az alkalmazásban további logikára van szükség az ismétlődések észleléséhez, amelyek az üzenet [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonsága alapján érhetők el, ami állandó marad a kézbesítési kísérletek között. Ezt a szolgáltatást *pontosan egyszer* kell feldolgozni.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések

A várólistákkal ellentétben, amelyekben az egyes üzeneteket egyetlen fogyasztó dolgozza fel, a *témakörök* és az *előfizetések* egy a többhöz típusú kommunikációt biztosítanak egy *közzétételi/* előfizetési mintában. Hasznos a nagy számú címzett méretezéséhez, minden közzétett üzenet elérhetővé válik a témakörben regisztrált összes előfizetés számára. Az üzenetek küldése egy témakörbe történik, és egy vagy több társított előfizetéshez érkezik, attól függően, hogy milyen szűrési szabályok állíthatók elő előfizetések alapján. Az előfizetések további szűrőket is használhatnak a fogadni kívánt üzenetek korlátozására. Az üzeneteket a rendszer ugyanúgy elküldi egy adott témakörnek, ahogyan azt egy várólistába küldik, de az üzenetek nem érkeznek meg közvetlenül a témakörből. Ehelyett az előfizetésből érkeznek. A témakör-előfizetés egy olyan virtuális várólistára hasonlít, amely megkapja a témakörbe küldött üzenetek másolatait. Az üzenetek az előfizetéstől azonos módon érkeznek, mint egy várólistából.

Összehasonlítás útján a várólista üzenetküldési funkciója közvetlenül a témakörre és az üzenet által fogadott funkciókra képezi le az előfizetést. Ez a funkció többek között azt jelenti, hogy az előfizetések az ebben a szakaszban korábban ismertetett mintákat támogatják a várólistákkal kapcsolatban: a versengő fogyasztók, az időbeli leválasztás, a terheléselosztás és a terheléselosztás.

### <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása

A témakör létrehozása hasonló a várólista létrehozásához az előző szakaszban leírtak szerint. Ezután üzeneteket küldhet a [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) osztály használatával. Az üzenetek fogadásához létre kell hoznia egy vagy több előfizetést a témakörbe. A várólistákhoz hasonlóan a [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum helyett egy [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) -objektumtól érkeznek üzenetek az előfizetésből. Hozza létre az előfizetési ügyfelet, adja át a témakör nevét, az előfizetés nevét és (opcionálisan) a fogadási módot paraméterként.

Teljes körű működésre példaként tekintse meg a [BasicSendReceiveUsingTopicSubscriptionClient mintát](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) a githubon.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Számos esetben a konkrét tulajdonságokkal rendelkező üzeneteket különböző módokon kell feldolgozni. A feldolgozás engedélyezéséhez megadhatja az előfizetéseket, hogy megkeresse a kívánt tulajdonságokkal rendelkező üzeneteket, majd végrehajtsa a tulajdonságok bizonyos módosításait. Míg Service Bus előfizetések a témakörbe küldött összes üzenetet láthatják, az üzenetek egy részhalmazát csak a virtuális előfizetési várólistára másolhatja. Ez a szűrés előfizetési szűrők használatával valósítható meg. Ezeket a módosításokat *szűrési műveleteknek*nevezzük. Előfizetés létrehozásakor megadhat egy szűrési kifejezést, amely az üzenet tulajdonságain alapul, valamint a rendszertulajdonságokat (például a **címkét**) és az egyéni alkalmazás tulajdonságait (például **StoreName**). Ebben az esetben az SQL FILTER kifejezés nem kötelező. SQL-szűrő kifejezés nélkül az előfizetésben definiált összes szűrési művelet az adott előfizetés összes üzenetén el lesz hajtva.

Teljes körű működésre példaként tekintse meg a [TopicSubscriptionWithRuleOperationsSample mintát](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) a githubon.

A lehetséges szűrési értékekkel kapcsolatos további információkért tekintse meg a [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) és a [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) osztály dokumentációját.

## <a name="next-steps"></a>Következő lépések

További információt és példákat a Service Bus üzenetkezelés használatával kapcsolatban a következő speciális témakörökben talál:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [Gyors útmutató: üzenetek küldése és fogadása a Azure Portal és a .NET használatával](service-bus-quickstart-portal.md)
* [Oktatóanyag: leltár frissítése Azure Portal és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)


