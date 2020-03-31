---
title: Az Azure Service Bus-várólisták használata a Rubyval
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Ruby-alkalmazásokat üzenetek küldéséhez és fogadásához egy Service Bus-várólistából.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760589"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Rövid útmutató: A Service Bus-várólisták használata a Ruby val

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Ruby-alkalmazásokat üzenetek küldéséhez és fogadásához egy Service Bus-várólistából. A minták Ruby nyelven vannak írva, és az Azure gem használatával.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Kövesse az Azure Portal használata című cikklépéseit [a Service Bus-várólista-cikk létrehozásához.](service-bus-quickstart-portal.md)
    1. Olvassa el a Service Bus **várólistáinak**gyors **áttekintését.** 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolati karakterlánc bekapcsolása**. 

        > [!NOTE]
        > Hozzon létre egy **várólistát** a Service Bus névtérben a Ruby használatával ebben az oktatóanyagban. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Üzenetsor létrehozása
Az **Azure::ServiceService** objektum lehetővé teszi a várólisták. Várólista létrehozásához `create_queue()` használja a módszert. A következő példa létrehoz egy várólistát, vagy kinyomtatja a hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Az **Azure::ServiceBus::Queue** objektum további beállításokkal is átadhat, amely lehetővé teszi az alapértelmezett várólista-beállítások felülbírálását, például az élő üzenet élettartamát vagy a várólista maximális méretét. A következő példa bemutatja, hogyan állíthatja be a várólista maximális méretét 5 GB-ra, és 1 percre kell élnie:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Üzenetek küldése várólistába
Ha üzenetet szeretne küldeni egy Service Bus-várólistába, az alkalmazás meghívja a `send_queue_message()` metódust az **Azure::ServiceBusService** objektumon. A Service Bus-várólistákba küldött (és onnan érkező) üzenetek **az Azure::ServiceBus::BrokeredMessage** `label` objektumok, és szabványos tulajdonságokkal (például és `time_to_live`) rendelkeznek, amelyek egyéni alkalmazásspecifikus tulajdonságok tárolására szolgálnak, valamint tetszőleges alkalmazásadatok törzsével. Egy alkalmazás beállíthatja az üzenet törzsét egy karakterlánc-érték átadásával, mint az üzenet, és a szükséges szabványos tulajdonságok alapértelmezett értékekkel vannak feltöltve.

A következő példa bemutatja, hogyan küldhet tesztüzenetet a megnevezett `test-queue` várólistába a következő használatával: `send_queue_message()`

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Az üzenetek egy várólistából `receive_queue_message()` érkeznek az **Azure::ServiceBusService** objektum metódusával. Alapértelmezés szerint az üzenetek olvasása és zárolása a várólistából való törlés nélkül történik. Az üzeneteket azonban törölheti a várólistából olvasás `:peek_lock` közben, ha a beállítást **hamis**beállításra állítja.

Az alapértelmezett viselkedés kétlépcsős művelet olvasását és törlését teszi lehetővé, ami lehetővé teszi olyan alkalmazások támogatását is, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a hívási `delete_queue_message()` módszerrel, és paraméterként megadja a törölni kell az üzenetet. A `delete_queue_message()` metódus az üzenetet felhasználásként jelöli meg, és eltávolítja a várólistából.

Ha `:peek_lock` a paraméter **értéke hamis**, olvasása és törlése lesz a legegyszerűbb modell, és működik a legjobban olyan esetekben, amikor egy alkalmazás tolerálja, hogy nem dolgoz fel egy üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet felhasználtként jelölte meg, amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenet hiányozni fog.

A következő példa bemutatja, hogyan `receive_queue_message()`fogadhat és dolgozhat fel üzeneteket a használatával. A példa először fogad és töröl `:peek_lock` egy üzenetet a **beállítás**hamis beállításával, majd `delete_queue_message()`egy másik üzenetet kap, majd törli az üzenetet a következő használatával:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlock_queue_message()` az üzenetet, akkor meghívhatja a metódust az **Azure::ServiceBusService** objektumon. Ez a hívás hatására a Service Bus feloldja az üzenet zárolását a várólistán belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanazt a fogyasztó alkalmazást, akár egy másik fogyasztó alkalmazást.

Időtúltöltés is van társítva a várólistán belül zárolt üzenethez, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és lehetővé teszi újra fogadható.

Abban az esetben, ha az alkalmazás összeomlik `delete_queue_message()` az üzenet feldolgozása után, de a metódus megküldése előtt, majd az üzenet újra kézbesítése az alkalmazáshoz, amikor újraindul. Ezt a folyamatot gyakran *nevezik legalább egyszer feldolgozás*; ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet `message_id` tulajdonságával érhető el, amely a kézbesítési kísérletek során állandó marad.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

* [Várólisták, témakörök és előfizetések áttekintése](service-bus-queues-topics-subscriptions.md).
* Keresse fel az [Azure SDK ruby](https://github.com/Azure/azure-sdk-for-ruby) tárház a GitHubon.

A cikkben tárgyalt Azure Service Bus-várólisták és a [Ruby-ból származó várólista-tárolás használata](../storage/queues/storage-ruby-how-to-use-queue-storage.md) című cikkben tárgyalt Azure Service Bus-várólisták összehasonlítását az [Azure-várólisták és az Azure Service Bus-várólisták című](service-bus-azure-and-service-bus-queues-compared-contrasted.md) témakörben olvashat.

