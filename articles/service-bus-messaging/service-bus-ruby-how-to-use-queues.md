---
title: Az Azure Service Bus-üzenetsorok használata a Ruby használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. Ruby nyelven írt kódmintákat.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6c42fbffd0b4569a9b04dede94061e716c48ecf1
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501110"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Service Bus-üzenetsorok használata a Ruby használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebben az oktatóanyagban megismerheti, hogyan hozhat létre Ruby-alkalmazások üzeneteket küld és fogad üzeneteket egy Service Bus-üzenetsorba. A Kódminták Ruby nyelven íródtak, és használja az Azure gem.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse lépéseket a [egy Service Bus-üzenetsor létrehozása az Azure portal](service-bus-quickstart-portal.md) cikk.
    1. Olvassa el a gyors **áttekintése** Service Bus **üzenetsorok**. 
    2. Hozzon létre egy Service Bus **névtér**. 
    3. Első a **kapcsolati karakterlánc**. 

        > [!NOTE]
        > Létrehozhat egy **várólista** a Ruby használatával ebben az oktatóanyagban a Service Bus-névteret. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Várólista létrehozása
A **Azure::ServiceBusService** objektum lehetővé teszi, hogy az üzenetsorok. Egy üzenetsor létrehozásához használja a `create_queue()` metódust. Az alábbi példa létrehoz egy üzenetsort, és kiírja az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Is átadhat egy **Azure::ServiceBus::Queue** további lehetőségeket rendelkező objektum, amely lehetővé teszi, hogy felülírják az alapértelmezett várólista beállításokat, például a valós idejű vagy maximális Várólistaméret üzenet ideje. Az alábbi példa bemutatja, hogyan állítható be a várólista maximális mérete 5 GB és 1 percre élő idő:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzenet küldése a Service Bus-üzenetsorba, az alkalmazás meghívja a `send_queue_message()` metódust a **Azure::ServiceBusService** objektum. Üzeneteket küldenek (és a fogadott) a Service Bus-üzenetsor **Azure::ServiceBus::BrokeredMessage** objektumokat, és a egy szabványos tulajdonságkészlettel rendelkezik (például `label` és `time_to_live`), egy egyéni tárolására használt szótár alkalmazásspecifikus tulajdonságokat, és a egy tetszőleges alkalmazásadatokból álló törzzsel törzse. Az alkalmazás beállíthatja az üzenet törzsét egy karakterláncértéket átadásával az üzenetnek számít, és minden szükséges alapvető tulajdonságainak fel van töltve, az alapértelmezett értékekkel.

Az alábbi példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `test-queue` használatával `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Egy üzenetsor használatával fogadása a `receive_queue_message()` metódust a **Azure::ServiceBusService** objektum. Alapértelmezés szerint az üzenetek olvasása és anélkül, hogy törli a várólistáról zárolva van. Azonban törölheti üzeneteket az üzenetsorból, beállításával olvasott a `:peek_lock` beállítást **hamis**.

Az alapértelmezett viselkedés lehetővé teszi az olvasási és a egy kétszakaszos művelet, amely is lehetővé teszi alkalmazások, amelyek működését zavarják a hiányzó üzenetek támogatása törlése. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `delete_queue_message()` metódust, és a paramétert a törlendő üzenet megadása. A `delete_queue_message()` módszert fog jelölje meg az üzenetet, és távolítsa el az üzenetsorból.

Ha a `:peek_lock` paraméter értéke **hamis**olvasása és törlése az üzenet válik, a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. A Service Bus az üzenetet, a rendszer, elérheti, ha az alkalmazás újraindításakor és megkezdésekor üzenetek van megjelölve, mert ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Az alábbi példa bemutatja, hogyan használatával üzenetek fogadásához és feldolgozásához `receive_queue_message()`. A példában először kap, és törli az üzenetet a `:peek_lock` beállítása **hamis**, majd egy másik üzenetet kap, és végül törli a üzenet használatával `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlock_queue_message()` metódust a **Azure::ServiceBusService** objektum. Ez a hívás hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `delete_queue_message()` módszert hívja meg, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ez a folyamat gyakran nevezik *legalább egyszeri feldolgozásnak*; azaz minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el használatával a `message_id` tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

* Áttekintése [üzenetsorok, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md).
* Látogasson el a [Rubyhoz készült Azure SDK-t](https://github.com/Azure/azure-sdk-for-ruby) tárházban a Githubon.

Az ebben a cikkben tárgyalt Azure Service Bus-üzenetsorok és az Azure Queues tárgyalt összehasonlítását a [Queue storage Ruby használata](../storage/queues/storage-ruby-how-to-use-queue-storage.md) cikkre, lásd: [Azure-üzenetsorok és Azure Service Bus-üzenetsorok - képest és Összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

