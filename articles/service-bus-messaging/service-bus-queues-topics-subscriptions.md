---
title: Az Azure Service Bus üzenetkezelés üzenetsorok, témakörök és előfizetések áttekintése |} Microsoft Docs
description: Service Bus üzenetküldési entitásokról áttekintése.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/18/2018
ms.author: sethm
ms.openlocfilehash: 424004a2a39bd0d05bce515dc17685e60f7a0c9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231576"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)

A Microsoft Azure Service Bus felhőalapú, üzenet célú köztes technológiák – például a megbízható üzenetsor és a tartós közzétételi/előfizetési üzenetküldési támogat. A "közvetítőalapú" üzenettovábbítási képességeket-re, leválasztott üzenetküldési funkcióknak, amely a támogatási közzétételi-feliratkozási, az időalapú leválasztási és a terheléselosztási forgatókönyveket a Service Bus üzenetküldési munkaterhelés használatával. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

Az üzenetküldési entitások, amely az alapvető üzenetkezelési képességeket alkotják a Service Bus üzenetsorok, témakörök és előfizetések és szabályok/művelet.

## <a name="queues"></a>Üzenetsorok

Az üzenetsorok elsőnek *First In, első kimenő* (FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Ez azt jelenti, hogy fogadók általában üzenetek fogadása és feldolgozása a sorrendben, amelyben addig adták hozzá, a várakozási sorba, és csak egy üzenetfogyasztó fogad és dolgoz fel minden üzenetet. Üzenetsorok használata egyik legfontosabb előnye értékre a "időbeli elválasztás" alkalmazás-összetevő. Más szóval az adatalkotóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell küldeniük és fogadniuk az üzeneteket egy időben, mivel üzenetek tartósan tárolja őket a várólista. Továbbá a küldőnek nem kell ahhoz, hogy a feldolgozásához, és üzenetek küldése egy a fogyasztó válaszára a Várakozás.

Egy kapcsolódó előny, "Terheléskiegyenlítés," amely lehetővé teszi a gyártó és a felhasználó számára a különböző ütemben üzeneteket küldjön és fogadjon. Számos alkalmazásban a rendszerterhelés időnként; eltérő azonban az egyes Munkaegységek szükséges feldolgozási idő jellemzően állandó marad. Közé üzenetek létrehozói és felhasználói üzenetsorokat jelenti, hogy a fogyasztó alkalmazás csak úgy kell létrehozni, hogy tudja kezelni az átlagos terheléssel csúcsterhelés helyett. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ezt a képességet közvetlenül alkalmazásterhelés kiszolgálásához szükséges infrastruktúraméret mennyiségének tekintetében pénzt takaríthat meg. Ha a terhelés növekszik, további feldolgozó folyamatok adhatók olvasni az üzenetsorból. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Ezenkívül a lekérésalapú terheléselosztás lehetővé teszi a munkavégző számítógépek optimális használatára akkor is, ha a munkavégző számítógépek feldolgozási teljesítmény tekintetében különböznek, azok lekéréses üzeneteket a saját maximális díj. Ezt a mintát gyakran nevezik "versengő fogyasztó" mintát.

Várólisták kiválasztásával közötti üzenetek létrehozói és felhasználói segítségével biztosítja az összetevők közötti rejlő laza kapcsoló. Mivel létrehozói és felhasználói nem kompatibilis, minden más, a fogyasztó anélkül, hogy a hatása, ha a gyártó a frissítése.

### <a name="create-queues"></a>Várólista létrehozása

A várólisták segítségével létrehozhat a [Azure-portálon](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md), vagy [Resource Manager-sablonok](service-bus-resource-manager-namespace-queue.md). Majd üzeneteket küldjön és fogadjon használatával egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum. 

Gyorsan megtudhatja, hogyan hozzon létre egy várólistát, majd üzeneteket küldjön és fogadjon a és a várólistából, tekintse meg a [quickstarts](service-bus-quickstart-portal.md) az egyes módszerek. Üzenetsorok használata részletesebb oktatóanyag, lásd: [Ismerkedés a Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md). 

Egy minta, tekintse meg a [BasicSendReceiveUsingQueueClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) a Githubon.

### <a name="receive-modes"></a>Fogadási módok

Megadhatja, hogy két különböző üzemmódjainak, amelyen a Service Bus fogad üzeneteket: *ReceiveAndDelete* vagy *PeekLock*. Az a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban, a fogadási művelet egylépéses; Ez azt jelenti, hogy a Service Bus a kérelmet kap, ha azt az üzenetet, feldolgozottként jelöli meg, és visszaadja az alkalmazásnak. **ReceiveAndDelete** mód a legegyszerűbb modell, és leginkább megfelelő forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Szeretné megtudni, ebben a forgatókönyvben, fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. A Service Bus jelöli meg az üzenetet, feldolgozottként, amikor az alkalmazás újraindításakor és megkezdése az üzenetek újra fel, mert ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

A [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) módban, a fogadási művelet válik kétszakaszos, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus a kérést kap, azt talál a következő feldolgozandó üzenetet, zárolja azt, hogy más fogyasztók ne fogadjon azt, és majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) metódus a fogadott üzenethez. Amikor a Service Bus látja a **CompleteAsync** hívás, azt az üzenetet, feldolgozottként jelöli.

Ha az alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, akkor meghívhatja a [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust a fogadott üzenethez (ahelyett, hogy [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ez a módszer lehetővé teszi, hogy a Service Bus feloldja az üzenet zárolását, és lehetővé teszi az ugyanazon felhasználó vagy egy másik versengő felhasználó ismételt fogadását. Másodszor nincs társítva a zárolási időtúllépés és ha az alkalmazás nem tudja feldolgozni az üzenetet, a zárolás időtúllépését lejárta előtt (például, ha az alkalmazás összeomlik), akkor a Service Bus feloldja az üzenet, és lehetővé teszi az érhető fogadott újra) lényegében hajt végre egy [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) művelet alapértelmezés szerint).

Abban az esetben, ha az alkalmazás összeomlik az üzenet, de előtte feldolgozása után a **CompleteAsync** kérés kiadása, az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ez a folyamat gyakran nevezik *legalább egyszeri* feldolgozása; Ez azt jelenti, hogy minden üzenet legalább egyszer fel. Azonban bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újból kézbesítve. Ha ismétlődő feldolgozás, a forgatókönyvben nem lehetségesek, akkor további logikát azért van szükség az alkalmazás ismétlődések észlelése, amely elérhető alapján a [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonság az üzenet, amely több állandó marad kézbesítési kísérletek során. Ez a funkció néven ismert *pontosan egyszer* feldolgozása.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések

Várólisták, amelyben minden üzenetet dolgoz fel egy-egy fogyasztó, ellentétben *témakörök* és *előfizetések* egy egy-a-többhöz típusú kommunikációt biztosítanak a egy *közzétételi/előfizetési* mintát. Hasznos címzettek nagy számú méretezését, minden egyes közzétett üzenetek szeretné elérhetővé tenni az adott témakörre regisztrált összes előfizetéshez. Üzenetek elküldenek egy témakörbe, és hogy egy vagy több kapcsolódó előfizetések, attól függően, hogy a szűrési szabályokat, amelyek egy előfizetésenként történő beállítására is beállítható. Az előfizetések további szűrőkkel korlátozza az üzeneteket, amelyeket be szeretne kapni. Üzenetek küldése történik, a témakör ugyanúgy azok küldik annak a várólistára, de nem érkezik üzenet témakörben közvetlenül. Ehelyett azok érkező előfizetések. Egy üzenettémakör-előfizetésben hasonlít egy virtuális üzenetsorra, amelyik a témakörbe küldött üzenetek példányait megkapja. Üzenetek érkező előfizetés azonos az üzenetsorból érkező felügyeletéhez.

Összehasonlításképpen az üzenet küldése funkciókat várólista közvetlenül leképezve a témakör és előfizetés üzenet-fogadás funkciókat van leképezve. Többek között a funkció lehetővé teszi, hogy előfizetések támogatja-e a várólisták tekintetében ebben a szakaszban bemutatott azonos minták: versengő felhasználó, az időalapú leválasztási, terhelés simítás és terheléselosztás.

### <a name="create-topics-and-subscriptions"></a>Üzenettémák és előfizetések létrehozása

A témakör létrehozása hasonlít egy üzenetsort szeretne létrehozni az előző szakaszban leírtak szerint. Elküldheti az üzenetek a [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) osztály. Üzenetek fogadására, a témakör egy vagy több előfizetést hoz létre. Várólisták hasonló üzenetek fogadása egy előfizetés használatával egy [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) ahelyett, hogy az objektum egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum. Az előfizetés ügyfél, a témakör a neve, a az előfizetést, és (opcionálisan) a fogadás mód átadása paraméterként létrehozása. 

Egy teljes működik-e példában, tekintse meg a [BasicSendReceiveUsingTopicSubscriptionClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) a Githubon.

### <a name="rules-and-actions"></a>Szabályok és műveletek

A sok esetben meghatározott jellemzőkkel rendelkező üzenetek különböző módon kell feldolgozni. Ahhoz, hogy a feldolgozás, konfigurálhatja az előfizetések található üzeneteket tulajdonságok van szükség, és hajtsa végre az egyes módosítások azokat a tulajdonságokat. Miközben Service Bus-előfizetések megtekintéséhez a témakörbe küldött üzenetek csak másolhatja azokat az üzeneteket egy részét az virtuális előfizetés üzenetsorába. A szűrés érhető el, előfizetés-szűrők használata. Ezek a módosítások nevezzük *szűrési műveletek*. Előfizetés létrehozásakor megadhat egy kifejezést, amely az üzenet, a rendszer tulajdonságai tulajdonságai (például **címke**) és az egyéni tulajdonságok (például **storename –**.) Az SQL szűrőkifejezés nem kötelező ebben az esetben; egy SQL-kifejezést, nélkül bármely előfizetés definiált szűrőművelet az adott előfizetéshez tartozó összes üzenet érvényesül.

Egy teljes működik-e példában, tekintse meg a [TopicSubscriptionWithRuleOperationsSample minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) a Githubon.

További információ a lehetséges szűrőértékek dokumentációjában a [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) és [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) osztályok. 

## <a name="next-steps"></a>További lépések

További információt és példákat a használatával a Service Bus üzenetkezelés lásd az alábbi témakörök speciális:

* [A Service Bus üzenetkezelésének áttekintése](service-bus-messaging-overview.md)
* [Gyors üzembe helyezés: Üzeneteket küldjön és fogadjon az Azure portál és a .NET használatával](service-bus-quickstart-portal.md)
* [Oktatóanyag: Azure-portálon és témakörök/előfizetések készlet frissítése](service-bus-tutorial-topics-subscriptions-portal.md)


