---
title: Azure Service Bus-üzenetsorok használata Ruby |} Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. Kódminták Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 357a7277dd42b6973cf35a9f642b8eec36a745e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868444"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Service Bus-üzenetsorok használata Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez az útmutató ismerteti a Service Bus-üzenetsorok használatával. A Kódminták Ruby nyelven íródtak, és az Azure gem használják. Az ismertetett forgatókönyvek **üzenetsorok üzenetek küldése és fogadása létrehozása**, és **várólisták törlése**. Service Bus-üzenetsorokkal kapcsolatos további információkért tekintse meg a [lépések](#next-steps) szakasz.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>A várólista létrehozása
A **Azure::ServiceBusService** objektum lehetővé teszi a várólisták használata. A várólista létrehozásához használja a `create_queue()` metódust. Az alábbi példa létrehoz egy üzenetsort, vagy jelenít meg az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Is átadhatja a **Azure::ServiceBus::Queue** objektum további beállításokat, amely lehetővé teszi az alapértelmezett várólista-beállításokat, például az üzenet time to live vagy maximális Várólistaméret felülbírálására. A következő példa bemutatja, hogyan beállítani a várólista maximális hossza 1 percre élő 5 GB és idő:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Egy üzenet küldhető egy Service Bus-üzenetsorba, az alkalmazás hívások a `send_queue_message()` metódust a **Azure::ServiceBusService** objektum. Üzenetek küldenek (és a fogadott) Service Bus-üzenetsor **Azure::ServiceBus::BrokeredMessage** objektumokat, és rendelkezik egy szabványos tulajdonságkészlettel (például `label` és `time_to_live`), egyéni tárolására használt dictionary az alkalmazás-specifikus tulajdonságait, és egy tetszőleges alkalmazásadatokból álló törzzsel. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy a karakterlánc-értékkel, az üzenet, és egyéb szabványos tulajdonságokat a rendszer feltölti az alapértelmezett értékekkel.

A következő példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `test-queue` használatával `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Üzenetek fogadása egy várólista használja a `receive_queue_message()` metódust a **Azure::ServiceBusService** objektum. Alapértelmezés szerint az üzenetek olvassa el és nem törlődnek a várólistából zárolva. Azonban is törölnie üzenetet az üzenetsorból olvasott úgy, hogy a `:peek_lock` lehetőséggel **hamis**.

Az alapértelmezett viselkedés lehetővé teszi az olvasási és egy kétszakaszos művelet, ami is lehetővé teszi az alkalmazások, amelyek működését zavarják a hiányzó üzenetek támogatásához törlése. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával `delete_queue_message()` metódus és a törlendő paraméterként message. A `delete_queue_message()` metódus lesz használnak az üzenetet, és távolítsa el a sorból.

Ha a `:peek_lock` paraméter értéke **hamis**, olvasása, és törli az üzenetet a legegyszerűbb modell válik, és működik a legjobban forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. A Service Bus jelölte meg az üzenetet, feldolgozottként, amikor az alkalmazás újraindításakor és megkezdése az üzenetek újra fel, mert ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

A következő példa bemutatja, hogyan használatával üzenetek fogadása és feldolgozása `receive_queue_message()`. A példa első kap, és törli egy üzenetet használatával `:peek_lock` beállítása **hamis**, akkor egy másik üzenetet kap, és végül törli a üzenet használatával `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlock_queue_message()` metódust a **Azure::ServiceBusService** objektum. Ez a hívás hatására a Service Bus feloldja az üzenet a várólistában zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi az elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `delete_queue_message()` metódust, akkor az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ez a folyamat gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a `message_id` tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

* Áttekintés [üzenetsorok, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md).
* Látogasson el a [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) GitHub tárházából.

A cikkben szereplő Azure Service Bus-üzenetsorok és tárgyalt Azure várólisták összehasonlításához a [a Ruby a Queue storage használata](../storage/queues/storage-ruby-how-to-use-queue-storage.md) cikk című [Azure várólisták és az Azure Service Bus-üzenetsorok - képest és Ellentétben](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

