---
title: Service Bus-üzenettémakörök (Ruby) használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható a Service Bus-üzenettémák és előfizetések az Azure-ban. Ruby-alkalmazások Kódminták készültek.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: c2862c3521c05ba81f7a567f7951d22a9ab95c44
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609421"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Service Bus-üzenettémák és előfizetések használata a Ruby használatával
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk ismerteti, hogyan használható a Service Bus-üzenettémák és előfizetések a Ruby-alkalmazásokat. Az ismertetett forgatókönyvek a következők:

- Üzenettémák és előfizetések létrehozása 
- Előfizetés-szűrők létrehozása 
- Üzenetek küldése egy üzenettémakörbe 
- Üzenetek fogadása egy előfizetésből
- Üzenettémák és előfizetések törlése


## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [Visual Studio vagy az MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse lépéseket a [a rövid útmutató: Az Azure portal használatával hozzon létre egy Service Bus-témakörbe, és a témakörbe az előfizetések](service-bus-quickstart-topics-subscriptions-portal.md) hozhat létre egy Service Bus **névtér** , és a **kapcsolati karakterlánc**. 

    > [!NOTE]
    > Létrehozhat egy **témakör** és a egy **előfizetés** használatával a témakörbe **Ruby** ebben a rövid útmutatóban. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **Azure::ServiceBusService** objektum lehetővé teszi, hogy a témakörök. Az alábbi kód létrehoz egy **Azure::ServiceBusService** objektum. A témakör létrehozásához használja a `create_topic()` metódust. Az alábbi példa létrehoz egy üzenettémakört vagy kiírja az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Is átadhat egy **Azure::ServiceBus::Topic** további beállítások, például a valós idejű vagy maximális Várólistaméret üzenet ideje témakör alapbeállítások felülírását is lehetővé tevő objektum. Az alábbi példa bemutatja, hogy a várólista maximális mérete 5 GB-os és az idő élettartama 1 percre állítja:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Üzenettémakör-előfizetéseket is jönnek létre az a **Azure::ServiceBusService** objektum. Előfizetés neve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorának üzenetet korlátoz.

Előfizetések állandóak. A futtatásuk továbbra is létezik, vagy csak, vagy a témakör társítva, a rendszer törli. Ha az alkalmazás-előfizetés létrehozása logikát tartalmaz, azt kell először ellenőrizze, hogy az előfizetés már a getSubscription módszer használatával.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs meghatározva szűrő egy új előfizetés létrehozásakor, a **MatchAll** szűrő (alapértelmezett) használatos. Ha a **MatchAll** szűrőt használ, a témakörbe közzétett összes üzenetet az előfizetés virtuális üzenetsorának vannak elhelyezve. Az alábbi példa egy "minden-üzenetek" nevű előfizetést hoz létre, és használja az alapértelmezett **MatchAll** szűrőt.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Is meghatározhat szűrőket, amelyek lehetővé teszik annak meghatározását, amely egy témakörbe küldött üzenetek egy adott előfizetésen belül kell megjeleníteni.

A legrugalmasabb típusú szűrő előfizetések által támogatott a **Azure::ServiceBus::SqlFilter**, amely megvalósítja az SQL92 egy részhalmazát. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további részletekért tekintse át a [SqlFilter](service-bus-messaging-sql-filter.md) szintaxist.

Az előfizetéshez szűrők is hozzáadhat a `create_rule()` módszere a **Azure::ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrőket hozzáadni egy meglévő előfizetéshez.

Mivel minden új előfizetés automatikusan alkalmazza az alapértelmezett szűrő, távolítsa el az alapértelmezett szűrő vagy a **MatchAll** bármely más szűrők, megadhatja azt felülírja. Eltávolíthatja az alapértelmezett szabály használatával a `delete_rule()` metódust a **Azure::ServiceBusService** objektum.

A következő példában létrehozunk egy "nagy-üzenetek" nevű előfizetést egy **Azure::ServiceBus::SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `message_number` 3-nál nagyobb tulajdonság:

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

Hasonlóképpen, az alábbi példa egy nevű előfizetést hoz létre `low-messages` az egy **Azure::ServiceBus::SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `message_number` tulajdonság kisebb vagy egyenlő, mint 3:

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

Ha egy már üzenettel `test-topic`, akkor mindig lehet kézbesíti az üzenetet az előfizetett a `all-messages` témakör-előfizetés, és szelektív módon kézbesíti a `high-messages` és `low-messages` üzenettémakör-előfizető (attól függően esetén a tartalmukat).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Egy üzenetet küld egy Service Bus-témakörbe, az alkalmazás használatához a `send_topic_message()` metódust a **Azure::ServiceBusService** objektum. Service Bus-témakörökbe küldött üzenetek olyan példányai a **Azure::ServiceBus::BrokeredMessage** objektumokat. **Azure::ServiceBus::BrokeredMessage** objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például `label` és `time_to_live`), egyéni alkalmazásspecifikus tulajdonságokat használt, valamint egy karakterláncadatokat törzse. Az alkalmazás beállíthatja az üzenet törzsét egy karakterláncértéket történő átadásával az `send_topic_message()` módszerrel és bármilyen szükséges alapvető tulajdonságainak alapértelmezett értékek szerint fel van töltve.

Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az `test-topic`. A `message_number` egyéni tulajdonság értékének egyes üzenetek függ attól, a ciklus ismétléseinek (azt határozza meg, hogy melyik előfizetéssel kap,):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Üzenetek kapott egy előfizetés az a `receive_subscription_message()` metódust a **Azure::ServiceBusService** objektum. Alapértelmezés szerint az üzenetek read(peak) és anélkül, hogy törölné az előfizetés zárolva van. Olvassa el, és törli az üzenetet az előfizetésből beállításával a `peek_lock` beállítást **hamis**.

Az alapértelmezett viselkedés lehetővé teszi az olvasási és a egy kétszakaszos művelet, amely is lehetővé teszi alkalmazások, amelyek működését zavarják a hiányzó üzenetek támogatása törlése. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `delete_subscription_message()` metódust, és a paramétert a törlendő üzenet megadása. A `delete_subscription_message()` módszer jelöli meg az üzenetet, és távolítsa el az előfizetésből.

Ha a `:peek_lock` paraméter értéke **hamis**olvasása és törlése az üzenet válik, a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Képzeljen el egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, van megjelölve, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, kimaradt az összeomlás előtt feldolgozott üzenetet.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával `receive_subscription_message()`. A példában először kap, és törli az üzenetet a `low-messages` előfizetés használatával `:peek_lock` beállítása **hamis**, és a egy másik üzenetet kap a `high-messages` majd törli az üzenetet használatával`delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlock_subscription_message()` metódust a **Azure::ServiceBusService** objektum. Hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy előfizetésen belül zárolva üzenethez társított időtúllépés, és az alkalmazás nem tudja feldolgozni az üzenetet a zárolási előtt időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet és tegye elérhetővé számára az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `delete_subscription_message()` módszert hívja meg, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ezt gyakran nevezik *feldolgozása során legalább egyszer*; azaz minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. A logikai gyakran érhető el, használja a `message_id` tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Üzenettémák és előfizetések állandóak, és explicit módon kell-e törölve keresztül a [az Azure portal] [ Azure portal] vagy programozott módon. A következő példa bemutatja, hogyan lehet törölni a témakör nevű `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan lehet törölni az előfizetés nevű `high-messages` származó a `test-topic` témakör:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, kövesse az alábbi hivatkozások további.

* Lásd: [üzenetsorok, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md).
* Az [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) API-referenciája.
* Látogasson el a [Rubyhoz készült Azure SDK-t](https://github.com/Azure/azure-sdk-for-ruby) tárházban a Githubon.

[Azure portal]: https://portal.azure.com
