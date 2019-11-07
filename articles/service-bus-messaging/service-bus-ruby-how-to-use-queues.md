---
title: 'Gyors útmutató: Azure Service Bus várólisták használata a Ruby használatával'
description: 'Gyors útmutató: Service Bus-várólisták használata az Azure-ban. A Rubyban írt kód-minták.'
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 09fdc58254d260b6ffeff958b6bbda50332adfac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718768"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Gyors útmutató: Service Bus várólisták használata a Ruby használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Ruby-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához. A minták a Rubyban íródnak, és az Azure gem-t használják.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. [Service Bus üzenetsor létrehozásához kövesse az Azure Portal használata](service-bus-quickstart-portal.md) című témakör lépéseit.
    1. Olvassa el Service Bus **várólisták**gyors **áttekintését** . 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolatok karakterláncának**beolvasása. 

        > [!NOTE]
        > Az oktatóanyagban a Ruby használatával hozzon létre egy **várólistát** a Service Bus névtérben. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Üzenetsor létrehozása
Az **Azure:: ServiceBusService** objektum lehetővé teszi a várólistákkal való munkavégzést. Várólista létrehozásához használja a `create_queue()` metódust. A következő példa létrehoz egy várólistát, vagy kinyomtatja az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Átadhat egy **Azure:: ServiceBus:: várólista** -objektumot további beállításokkal, amelyekkel felülbírálhatja az alapértelmezett üzenetsor-beállításokat, például az üzenetek élettartamát vagy a várólista maximális méretét. Az alábbi példa bemutatja, hogyan állíthatja be a maximális várólista méretét 5 GB-ra és az idő 1 percen belüli élettartamára:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Üzenetek küldése egy várólistára
Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja a `send_queue_message()` metódust az **Azure:: ServiceBusService** objektumon. Az Service Bus várólisták az **Azure:: ServiceBus:: BrokeredMessage** objektumok, valamint a szabványos tulajdonságok (például `label` és `time_to_live`) egy készlete, amely az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgál. és a tetszőleges alkalmazásadatok törzse. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy egy sztringet küld az üzenetnek, és a szükséges standard tulajdonságokat az alapértelmezett értékekkel tölti fel.

Az alábbi példa bemutatja, hogyan küldhet tesztüzenet a `test-queue` nevű várólistára `send_queue_message()` használatával:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Az üzenetek egy várólistából érkeznek az **Azure:: ServiceBusService** objektum `receive_queue_message()` metódusának használatával. Alapértelmezés szerint az üzenetek az üzenetsor törlése nélkül olvashatók és zárolva lesznek. Az üzenetek a várólistáról való törlését azonban a `:peek_lock` beállítás **hamis**értékre állításával törölheti.

Az alapértelmezett viselkedés lehetővé teszi a kétfázisú művelet olvasását és törlését, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), az `delete_queue_message()` metódus meghívásával, valamint az üzenet paraméterként való törlésével elvégezte a fogadási folyamat második szakaszát. A `delete_queue_message()` metódus az üzenetet felhasználva fogja megjelölni, és eltávolítja azt a várólistából.

Ha a `:peek_lock` paraméter értéke **hamis**, az olvasás és az üzenet törlése a legegyszerűbb modell lesz, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, az alkalmazás újraindításakor és az üzenetek újrafelhasználásának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Az alábbi példa bemutatja, hogyan fogadhat és dolgozhat fel üzeneteket `receive_queue_message()` használatával. A példa először fogad és töröl egy üzenetet `:peek_lock` beállítás **hamis**értékre állításával, majd egy másik üzenetet kap, majd az `delete_queue_message()`használatával törli az üzenetet:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor a `unlock_queue_message()` metódust hívhatja az **Azure:: ServiceBusService** objektumon. Ez a hívás azt eredményezi, hogy Service Bus az üzenetet a várólistán belül, és elérhetővé teszi azt újra, akár egyazon alkalmazás, akár egy másik alkalmazás.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és lehetővé teszi ismét elérhető.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `delete_queue_message()` metódus meghívása előtt, akkor a rendszer az üzenetet az újraindításkor újra továbbítja az alkalmazásnak. Ezt a folyamatot gyakran nevezik *legalább egyszer feldolgozásra*; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet `message_id` tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek között.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

* A [várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)áttekintése.
* Látogasson el az [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) adattárára a githubon.

Az ebben a cikkben tárgyalt Azure Service Bus-várólisták és az Azure-várólisták közötti összehasonlítást lásd a [hogyan kell használni az üzenetsor-tárolás a Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) -ben című cikket: [Azure queues és Azure Service Bus Queues – összehasonlítás és kontrasztos](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

