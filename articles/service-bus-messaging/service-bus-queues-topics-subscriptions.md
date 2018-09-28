---
title: Az Azure Service Bus-üzenetsorok, témakörök és előfizetések üzenetkezelés áttekintése |} A Microsoft Docs
description: Service Bus üzenetküldési entitásokról áttekintése.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: spelluru
ms.openlocfilehash: 047c4c37090db77f7a7a692604dd63c5effff9fa
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409761"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)

A Microsoft Azure Service Bus felhőalapú, üzenetorientált közbenső technológiák többek között a megbízható üzenetsor és a tartós közzétételi/feliratkozási üzenetkezelési támogatja. Ezeket "közvetítőalapú" üzenettovábbítási képességeket tekinthető, leválasztott támogatási közzétételi-feliratkozási üzenetkezelési szolgáltatások időbeli elválasztás és terheléselosztási forgatókönyveket a Service Bus üzenetkezelési munkaterhelés használatával. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

Az üzenetküldési entitások, a Service Bus üzenetkezelési képességeket a core alkotó üzenetsorokat, üzenettémákat és előfizetéseket és a szabályok és műveletek.

## <a name="queues"></a>Üzenetsorok

Az üzenetsorok elsőnek *First In, első ki* (FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Vagyis a fogadók általában üzenetek fogadásához és feldolgozásához a sorrendben, amelyben hozzá lettek adva az üzenetsorba, és csak egy üzenetfogyasztó fogad, és minden üzenetet feldolgozza. Üzenetsorok használata egyik legfőbb előnye, hogy érhet el a "időbeli elválasztás" az alkalmazás-összetevők. Más szóval az adatalkotóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell lennie üzenetek küldése és fogadása egy időben, mert az üzeneteket az üzenetsor tartósan tárolja. Továbbá a gyártó nem kell ahhoz, hogy feldolgozásához és üzenetek küldése a fogyasztó válaszára várakozás.

Egy kapcsolódó előny, "Terheléskiegyenlítés," amely lehetővé teszi a gyártók és a fogyasztók különböző ütemben üzenetek küldése és fogadása. Számos alkalmazásban a rendszerterhelés időnként eltérő idő; azonban az egyes Munkaegységek szükséges feldolgozási idő, jellemzően állandó marad. Üzenetek létrehozói és felhasználói jelenti, hogy a fogyasztó alkalmazás csak ki kell építeni tudják helyett csúcsterhelés átlagos terhelés kezeléséhez. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ez a funkció közvetlenül menti kapcsolatban az alkalmazásterhelés kiszolgálásához szükséges mennyiségű költséget takaríthat meg. Ahogy a terhelés növekszik, további feldolgozó folyamatok olvasása az üzenetsorból is hozzáadhatók. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Ezenkívül a lekérésalapú terheléselosztás lehetővé teszi a feldolgozó számítógépek optimális használatát akkor is, ha a feldolgozási teljesítmény, a feldolgozó számítógépek különbözőek, azok üzenetek kérje le a saját maximális díj. Ezt a mintát gyakran nevezik a "versengő fogyasztó" mintának.

Az összetevők közötti egy rejlő laza kapcsolódásokra köztes közötti üzenet előállítókat és fogyasztókat,-üzenetsorok használatával biztosít. Előállítók és fogyasztók nem állnak egymással tudomást, mert egy fogyasztó a termelő hatással nélkül frissíthetők.

### <a name="create-queues"></a>Várólista létrehozása

Üzenetsorok használatával létrehozhat a [az Azure portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), vagy [Resource Manager-sablonok](service-bus-resource-manager-namespace-queue.md). Ezután küldött és fogadott üzenetek egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum. 

Gyors útmutató: hozzon létre egy üzenetsort, majd üzeneteket küldhet és fogadhat, és a várólistából, tekintse meg a [útmutatóink](service-bus-quickstart-portal.md) minden módszer esetén. Üzenetsorok használata a részletesebb oktatóanyagért lásd: [Ismerkedés a Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md). 

Egy működő minta: a [BasicSendReceiveUsingQueueClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) a Githubon.

### <a name="receive-modes"></a>Módok fogadása

Megadhatja, amelyben a Service Bus fogadja az üzeneteket kétféle: *ReceiveAndDelete* vagy *PeekLock*. Az a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) mód, a receive műveletet egylépéses; azt jelenti, amikor a Service Bus a kérelmet kap, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. **ReceiveAndDelete** mód a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Szeretné megtudni, ebben a forgatókönyvben, fontolja meg egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. A Service Bus jelöli meg az üzenetet, a rendszer, elérheti, ha az alkalmazás újraindításakor és megkezdésekor üzeneteket, mert ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

A [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) mód, a receive műveletet válik két szakaszból álló, amely lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. A Service Bus a kérelmet kap, amikor azt talál a következő feldolgozandó üzenetet, zárolja azt, hogy más fogyasztók számára fogadni, és majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) a fogadott üzenethez. Amikor a Service Bus látja a **CompleteAsync** hívások esetén megjelöli az üzenetet.

Ha az alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból, meghívhatja a [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust a fogadott üzenethez (helyett [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ez a módszer lehetővé teszi, hogy a Service Bus feloldja az üzenet zárolását, és tegye elérhetővé számára ugyanaz a feldolgozó vagy egy másik versengő felhasználó általi ismételt fogadását. Másodszor nincs társítva a zárolási időtúllépés és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúllépést lejárta előtt (például, ha az alkalmazás összeomlik), majd a Service Bus feloldja az üzenet, és lehetővé teszi számára legyen elérhető fogadott újra) lényegében hajt végre egy [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) művelet alapértelmezés szerint).

Abban az esetben, ha az üzenet, de mielőtt feldolgozása után az alkalmazás összeomlik a **CompleteAsync** kérés kiadása, az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ez a folyamat gyakran nevezik *legalább egyszeri* feldolgozása; hogy minden üzenet feldolgozása során legalább egyszer. Azonban bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat újbóli kézbesítése. Ha a forgatókönyv zavarják a duplikált feldolgozásra, majd további logikát ismétlődések észlelése az alkalmazásban van szükség, amely elérhető alapján a [üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid) az üzenet, amely több állandó marad a tulajdonság kézbesítési kísérletek során. Ez a funkció néven *pontosan egyszer* feldolgozása.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések

Várólisták, amelyben minden üzenetet dolgoz fel egy-egy fogyasztó, szakembereket *témakörök* és *előfizetések* egy-a-többhöz típusú kommunikációt biztosítanak az egy *Közzétételésfeliratkozás* mintát. Hasznos címzettek nagy számú méretezési, minden egyes közzétett üzenetek szeretné elérhetővé tenni az adott témakörre regisztrált összes előfizetéshez. Az üzenetek a témakörbe küldött és a egy vagy több kapcsolódó előfizető, attól függően, hogy minden előfizetés is beállíthat Állapotszűrő szabályok. Az előfizetések további szűrők használatával korlátozhatja az üzeneteket, amelyeket be szeretne kapni. Üzeneteket küld egy témakörbe, ugyanúgy, mint a rendszer elküldte őket az üzenetsor, de nem fogadása a témakörből közvetlenül. Ehelyett az előfizetésből érkezésükkor. Egy témakör-előfizetésbe hasonlít egy virtuális üzenetsorra, amely megkapja a témakörbe küldött üzenetek másolatát. Fogadása egy előfizetésből azonos módon kaptak az üzenetsorból.

Összehasonlításképpen működésének üzenet küldése egy üzenetsorba közvetlenül a témakör vannak leképezve, és üzenet-forgalmat fogadó termékfunkciókat képez le egy előfizetést. Többek között a funkció azt jelenti, hogy az előfizetések támogatja-e a várólisták kapcsolatban a jelen szakaszban fentebb leírt azonos minták: versengő felhasználó, leválaszthatók, Terheléskiegyenlítés és terheléselosztást.

### <a name="create-topics-and-subscriptions"></a>Üzenettémák és előfizetések létrehozása

A témakör létrehozása nagyon hasonlít egy üzenetsort, az előző szakaszban leírtak szerint. Elküldendő üzenetek a [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) osztály. Üzenetek fogadásához hozzon létre egy vagy több előfizetés a témakörbe. Üzenetsorok hasonlóan fogadása egy előfizetés az egy [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objektumazonosító helyett egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum. Hozzon létre az előfizetés-ügyfelet, az üzenettéma nevére, a neve, az előfizetés, és (opcionálisan) a fogadás mód átadása paraméterekként. 

Egy teljes működik például lásd a [BasicSendReceiveUsingTopicSubscriptionClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) a Githubon.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Sok esetben üzeneteket, amelyek meghatározott jellemzőkkel rendelkeznek, különböző módon kell feldolgozni. Ahhoz, hogy ezt a feldolgozást, konfigurálhatja az üzeneteket, amely rendelkezik a kívánt tulajdonságot, és hajtsa végre az egyes módosításokat, azokat a tulajdonságokat, az előfizetések. Amíg előfizetések a Service Bus-témakörbe küldött összes üzenet megtekintéséhez csak másolhatja azokat az üzeneteket egy részét az virtuális előfizetés üzenetsorába. Ez a szűrés történik az előfizetésszűrők használata. Ezek a módosítások az úgynevezett *szűrési műveletek*. Előfizetés létrehozásakor megadhat egy kifejezést, amely az üzenetet, a rendszer tulajdonságai tulajdonságainak (például **címke**) és az egyéni alkalmazástulajdonságokat (például  **StoreName**.) Az SQL-szűrési kifejezés ebben az esetben; nem kötelező megadni anélkül, hogy egy SQL-kifejezést egy adott előfizetés definiálva szűrő semmit az adott előfizetéshez tartozó összes üzenet végrehajtására kerül sor.

Egy teljes működik például lásd a [TopicSubscriptionWithRuleOperationsSample minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) a Githubon.

Lehetséges szűrőértéket kapcsolatos további információkért lásd: a dokumentációban a [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) és [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) osztályokat. 

## <a name="next-steps"></a>További lépések

További információk és példák a Service Bus-üzenetkezelés használatával tekintse meg az alábbi speciális témakörök:

* [A Service Bus üzenetkezelésének áttekintése](service-bus-messaging-overview.md)
* [Gyors útmutató: Üzenetek küldése és fogadása az Azure portal és a .NET használatával](service-bus-quickstart-portal.md)
* [Oktatóanyag: Az Azure Portallal és az üzenettémák, előfizetések készlet frissítése](service-bus-tutorial-topics-subscriptions-portal.md)


