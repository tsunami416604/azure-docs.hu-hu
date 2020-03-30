---
title: 'Rövid útmutató: A Service Bus témakörei (Ruby) használata'
description: 'Rövid útmutató: Megtudhatja, hogyan használhatja a Service Bus-témaköröket és előfizetéseket az Azure-ban. A kódminták ruby alkalmazásokhoz vannak megírva.'
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718927"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Rövid útmutató: A Service Bus-témakörök és -előfizetések használata a Ruby-val
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk ismerteti, hogyan használhatja a Service Bus-témakörök és a Ruby-alkalmazások előfizetései. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése témakörbe 
- Üzenetek fogadása előfizetésből
- Témakörök és előfizetések törlése


## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [Visual Studio vagy az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy ingyenes [fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Kövesse a [rövid útmutató lépéseit: Az Azure Portal használatával hozzon létre egy Service Bus-témakört, és a témakörelőfizetéseit](service-bus-quickstart-topics-subscriptions-portal.md) hozzon létre egy Service **Bus-névtér** létrehozásához és a **kapcsolati karakterlánc**bekéseléséhez. 

    > [!NOTE]
    > Ebben a rövid útmutatóban a **Ruby** használatával létrehoz egy **témakört** és egy **előfizetést** a témakörhöz. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Az **Azure::ServiceService** objektum lehetővé teszi a témakörök kelvaló t. A következő kód létrehoz egy **Azure::ServiceBusService** objektumot. Témakör létrehozásához használja `create_topic()` a módszert. A következő példa létrehoz egy témakört, vagy kinyomtatja a hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Az **Azure::ServiceBus::Topic** objektum további beállításokkal is átadhat, amelyek lehetővé teszik az alapértelmezett témakörbeállítások, például az üzenet élettartamának vagy a várólista maximális méretének felülbírálását. A következő példa bemutatja, hogy a várólista maximális méretét 5 GB-ra és 1 percre kell állnia az élethez:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
A témakör-előfizetések az **Azure::ServiceBusService** objektummal is létrejönnek. Az előfizetések neve meg van nevezve, és rendelkezhetnek egy választható szűrővel, amely korlátozza az előfizetés virtuális várólistájába kézbesített üzenetek készletét.

Alapértelmezés szerint az előfizetések állandóak. Mindaddig léteznek, amíg vagy azok, vagy a hozzájuk társított témakör tanusítható. Ha az alkalmazás logikát tartalmaz egy előfizetés létrehozásához, először ellenőrizze, hogy az előfizetés már létezik-e a getSubscription metódus használatával.

Az [AutoDeleteOnIdle tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)beállításával automatikusan törölheti az előfizetéseket.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha új előfizetés létrehozásakor nincs megadva szűrő, a **MatchAll** szűrő (alapértelmezett) lesz használva. A **MatchAll** szűrő használatos, a témakörben közzétett összes üzenet az előfizetés virtuális várólistájába kerül. A következő példa létrehoz egy "all-messages" nevű előfizetést, és az alapértelmezett **MatchAll** szűrőt használja.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Olyan szűrőket is megadhat, amelyek lehetővé teszik annak megadását, hogy mely témakörbe küldött üzenetek jelenjenek meg egy adott előfizetésen belül.

Az előfizetések által támogatott szűrőlegrugalmasabb típusa az **Azure::ServiceBus::SqlFilter,** amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrővel használható kifejezésekről további információt az [SqlFilter](service-bus-messaging-sql-filter.md) szintaxisát tekintheti meg.

Szűrőket adhat hozzá egy előfizetéshez az `create_rule()` **Azure::ServiceBusService** objektum metódusával. Ez a módszer lehetővé teszi, hogy új szűrőket adjon egy meglévő előfizetéshez.

Mivel az alapértelmezett szűrő automatikusan minden új előfizetésre vonatkozik, először el kell távolítania az alapértelmezett szűrőt, vagy a **MatchAll** felülírja a többi megadott szűrőt. Az alapértelmezett szabályt eltávolíthatja `delete_rule()` az **Azure::ServiceBusService** objektum metódusával.

A következő példa létrehoz egy "nagy üzenetek" nevű előfizetést egy **Azure::ServiceBus::SqlFilter előfizetéssel,** amely csak a 3-nál nagyobb egyéni `message_number` tulajdonsággal rendelkező üzeneteket választja ki:

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

Hasonlóképpen a következő példa létrehoz `low-messages` egy **Azure::ServiceBus::SqlFilter** nevű előfizetést, `message_number` amely csak 3-nál kisebb vagy azzal egyenlő tulajdonsággal rendelkező üzeneteket választ ki:

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

Amikor egy üzenetet most `test-topic`küld a rendszernek, az mindig `all-messages` a témakör-előfizetésre előfizetett fogadóknak `high-messages` lesz `low-messages` kézbesítve, és szelektíven kézbesíti a és a témakör-előfizetésre előfizetett fogadóknak (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ha üzenetet szeretne küldeni egy Service Bus-témakörnek, az alkalmazásnak az `send_topic_message()` **Azure::ServiceBusService** objektumon lévő metódust kell használnia. A Service Bus-témakörökbe küldött üzenetek az **Azure::ServiceBus::BrokeredMessage** objektumok példányai. **Azure::ServiceBus::BrokeredMessage** objektumok szabványos tulajdonságokkal `label` (például `time_to_live`és ), egy szótár, amely az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgál, és egy karakterlánc-adattörzs. Egy alkalmazás beállíthatja az üzenet törzsét azáltal, `send_topic_message()` hogy karakterlánc-értéket ad át a metódusnak, és a szükséges szabványos tulajdonságokat az alapértelmezett értékek töltik fel.

A következő példa bemutatja, hogyan `test-topic`küldhet öt tesztüzenetet a rendszernek. Az `message_number` egyes üzenetek egyéni tulajdonságértéke a ciklus iterációján alapul (ez határozza meg, hogy melyik előfizetés kapja meg):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből
Az `receive_subscription_message()` **azure::ServiceBusService** objektum metódusával előfizetésből érkeznek üzenetek. Alapértelmezés szerint az üzenetek olvasása (csúcs) és zárolt törlése nélkül az előfizetésből. Az üzenetet elolvashatja és törölheti `peek_lock` az előfizetésből, ha a beállítás átállítása **hamis**.

Az alapértelmezett viselkedés kétlépcsős művelet olvasását és törlését teszi lehetővé, ami lehetővé teszi olyan alkalmazások támogatását is, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a hívási `delete_subscription_message()` módszerrel, és paraméterként megadja a törölni kell az üzenetet. A `delete_subscription_message()` metódus az üzenetet felhasználásként jelöli meg, és eltávolítja azt az előfizetésből.

Ha `:peek_lock` a paraméter **értéke hamis**, olvasása és törlése lesz a legegyszerűbb modell, és működik a legjobban olyan esetekben, amikor egy alkalmazás tolerálja, hogy nem dolgoz fel egy üzenetet, ha hiba történik. Fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel a Service Bus az üzenetet felhasználtként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenetet kihagyta.

A következő példa bemutatja, hogyan fogadhatók `receive_subscription_message()`és dolgozhatók fel az üzenetek a használatával. A példa először fogad és töröl `low-messages` egy `:peek_lock` üzenetet az előfizetésből a **hamis**beállítás `high-messages` használatával, majd egy `delete_subscription_message()`másik üzenetet kap a, majd törli az üzenetet a következő használatával:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni `unlock_subscription_message()` az üzenetet, akkor meghívhatja a metódust az **Azure::ServiceBusService** objektumon. Ez okozza a Service Bus feloldja az üzenetet az előfizetésen belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanazt a fogyasztó alkalmazást, vagy egy másik fogyasztó alkalmazás.

Az előfizetésen belül zárolt üzenethez is időtúltöltés van társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és újra elérhetővé kell tenni.

Abban az esetben, ha az alkalmazás összeomlik `delete_subscription_message()` az üzenet feldolgozása után, de a metódus megküldése előtt, majd az üzenet újra kézbesítése az alkalmazáshoz, amikor újraindul. Gyakran *nevezik legalább egyszer feldolgozás*; ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez a logika gyakran `message_id` érhető el az üzenet tulajdonságával, amely a kézbesítési kísérletek során állandó marad.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakörök és az előfizetések állandóak, kivéve, ha az [AutoDeleteOnIdle tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) be van állítva. Ezek törölhetők az [Azure Portalon][Azure portal] keresztül vagy programozott módon. A következő példa bemutatja, hogyan `test-topic`lehet törölni a témakör neve .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan `high-messages` lehet `test-topic` törölni a témakörből megnevezett előfizetést:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a Service Bus-témakörök alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* Lásd: [Várólisták, témakörök és előfizetések.](service-bus-queues-topics-subscriptions.md)
* Az [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) API-referenciája.
* Keresse fel az [Azure SDK ruby](https://github.com/Azure/azure-sdk-for-ruby) tárház a GitHubon.

[Azure portal]: https://portal.azure.com
