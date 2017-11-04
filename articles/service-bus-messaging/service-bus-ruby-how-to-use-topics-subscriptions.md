---
title: "Service Bus-üzenettémakörök (Ruby) használatával |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja a Service Bus-üzenettémák és előfizetések az Azure-ban. Kódminták Ruby alkalmazásokhoz íródtak."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Service Bus-üzenettémák és előfizetések használata Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

A cikkből megtudhatja, hogyan használhatja a Service Bus-üzenettémák és előfizetések Ruby alkalmazásokból. Az ismertetett forgatókönyvek **üzenettémák és előfizetések üzenetküldésre, előfizetés-szűrők létrehozása létrehozása** egy témakörbe **üzenetek fogadása egy előfizetésből**, és **törlése üzenettémák és előfizetések**. A témakörök és előfizetések további információkért lásd: a [lépések](#next-steps) szakasz.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **Azure::ServiceBusService** objektum lehetővé teszi a témakörök együttműködni. Az alábbi kód létrehoz egy **Azure::ServiceBusService** objektum. Létrehoz egy témát, használja a `create_topic()` metódust. Az alábbi példa létrehoz egy üzenettémakört vagy jelenít meg a hibákat, ha vannak ilyenek.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Is átadhatja egy **Azure::ServiceBus::Topic** további beállításokat, amelyek lehetővé teszik a például az üzenet time to live vagy maximális Várólistaméret alapértelmezett témakör beállításainak felülbírálása objektum. A következő példa bemutatja, hogy a várólista maximális hossza 1 percre élő 5 GB és idő beállítása:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Üzenettémakör-előfizetéseket is jönnek létre a **Azure::ServiceBusService** objektum. Előfizetések vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorában kézbesített üzenetek korlátoz.

Előfizetések állandó, és továbbra is létezik, vagy csak, vagy a következő témakörben társítva, a rendszer törli. Ha az alkalmazás előfizetés létrehozása a logikát tartalmaz, azt kell először ellenőrizze, hogy az előfizetés már léteznek getSubscription módszer használatával.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő, amely akkor használatos, ha nincs meghatározva szűrő egy új előfizetés létrehozásakor. Ha a **MatchAll** szűrővel, a témakörbe közzétett összes üzenetet kerülnek, az előfizetés virtuális üzenetsorában. Az alábbi példában a "minden-üzenetek" nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrő.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Is meghatározhat, amelyek lehetővé teszik, hogy adja meg, hogy szűrők egy témakörbe küldött üzenetek egy adott előfizetésen belül kell megjeleníteni.

A szűrő előfizetések által támogatott legrugalmasabb típusú a **Azure::ServiceBus::SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse át a [SqlFilter](service-bus-messaging-sql-filter.md) szintaxist.

Előfizetés szűrők a segítségével hozzáadhatók a `create_rule()` metódusában a **Azure::ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrők hozzáadása egy meglévő előfizetéshez.

Mivel minden új előfizetés automatikusan alkalmazza az alapértelmezett szűrő, távolítsa el az alapértelmezett szűrő, vagy a **MatchAll** felülírják más szűrőket is megadhat. Az alapértelmezett szabály eltávolításához használja a `delete_rule()` metódust a **Azure::ServiceBusService** objektum.

A következő példa egy "jó-üzenetek" nevű előfizetést hoz létre egy **Azure::ServiceBus::SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `message_number` tulajdonságának értéke nagyobb, mint 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Hasonlóképpen, a következő példa egy nevű előfizetést hoz létre `low-messages` rendelkező egy **Azure::ServiceBus::SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `message_number` tulajdonságának értéke kisebb vagy egyenlő, mint 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Ha egy most üzenettel `test-topic`, mindig kell biztosítását előfizetett fogadó számára a `all-messages` üzenettémakör-előfizetésre, és szelektív módon kézbesíti a a `high-messages` és `low-messages` üzenettémakör-előfizetéseket (attól függően, esetén az üzenet tartalma).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus-témakörbe való üzenetküldéshez az alkalmazást kell használnia a `send_topic_message()` metódust a **Azure::ServiceBusService** objektum. Service Bus-üzenettémakörök küldött üzenetek példánya a **Azure::ServiceBus::BrokeredMessage** objektumok. **Azure::ServiceBus::BrokeredMessage** objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például `label` és `time_to_live`), amellyel az egyéni alkalmazásspecifikus tulajdonságokat, valamint egy karakterláncadatokat törzsét. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy a karakterlánc-érték a `send_topic_message()` metódus, és minden szükséges szabványos tulajdonságkészlettel alapértelmezett értékek szerint lesz kitöltve.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az `test-topic`. Vegye figyelembe, hogy a `message_number` minden üzenetet egyéni tulajdonság értékének a az a ciklus ismétléseinek számától függően változik (Ez határozza meg, melyik előfizetéssel fogadó):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetés
Üzenetek fogadása egy előfizetés használatával a `receive_subscription_message()` metódust a **Azure::ServiceBusService** objektum. Alapértelmezés szerint az üzenetek read(peak) és anélkül, hogy törölné az előfizetés zárolva van. Olvassa el, és törölje az üzenetet az előfizetésből úgy, hogy a `peek_lock` lehetőséggel **hamis**.

Az alapértelmezett viselkedés lehetővé teszi az olvasási és egy kétszakaszos művelet, ami is lehetővé teszi az alkalmazások, amelyek működését zavarják a hiányzó üzenetek támogatásához törlése. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával `delete_subscription_message()` metódus és a törlendő paraméterként message. A `delete_subscription_message()` metódus lesz használnak az üzenetet, és távolítsa el az előfizetésből.

Ha a `:peek_lock` paraméter értéke **hamis**, olvasása, és törli az üzenetet a legegyszerűbb modell válik, és működik a legjobban forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával `receive_subscription_message()`. A példa első kap, és törli az üzenetét a `low-messages` használatával előfizetés `:peek_lock` beállítása **hamis**, majd egy másik üzenetet kap a `high-messages` majd törli az üzenet `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlock_subscription_message()` metódust a **Azure::ServiceBusService** objektum. Ennek hatására a Service Bus feloldja az üzenet zárolását az előfizetésen belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van lévő az előfizetéshez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldást az üzenet és tegye elérhetővé az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `delete_subscription_message()` metódust, akkor az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer dolgoz fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ezt gyakran érhető el, használja a `message_id` az üzenet, amely állandó marad a kézbesítési kísérletek tulajdonságát.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Üzenettémák és előfizetések következetesek, és explicit módon kell lennie vagy törölte a [Azure-portálon] [ Azure portal] vagy programon keresztül. Az alábbi példa bemutatja, hogyan nevű üzenettémakör törlése `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan nevű előfizetés törlése `high-messages` a a `test-topic` témakör:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, az alábbi hivatkozásokból további.

* Lásd: [üzenetsorok, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md).
* Az [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter) API-referenciája.
* Látogasson el a [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) GitHub tárházából.

[Azure portal]: https://portal.azure.com
