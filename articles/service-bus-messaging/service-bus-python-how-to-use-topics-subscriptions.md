---
title: "Azure Service Bus-üzenettémakörök használata a Python |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Service Bus-üzenettémák és előfizetések a Python."
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 15269f9728e9dc45e6436e53b1859f76d4a7a0c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Service Bus-üzenettémák és előfizetések használata Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk a Service Bus-üzenettémakörök és -előfizetések használatát ismerteti. A mintákat a Python és -felhasználási nyelven íródtak a [Azure Python SDK csomag][Azure Python package]. Az ismertetett forgatókönyvek **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzeneteket küld egy témakör**, **üzenetek fogadása egy előfizetésből**, és **üzenettémák és előfizetések törlése**. Üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd: a [lépések](#next-steps) szakasz.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Ha telepítenie kell Python vagy a [Azure Python-csomag][Azure Python package], tekintse meg a [Python a telepítési útmutató](../python-how-to-install.md).

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A **ServiceBusService** objektum lehetővé teszi a témakörök együttműködni. Adja hozzá a következő tetejénél található bármely Python-fájl, amelyben a Service Bus programon keresztüli eléréséhez kíván:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Az alábbi kód létrehoz egy **ServiceBusService** objektum. Cserélje le `mynamespace`, `sharedaccesskeyname`, és `sharedaccesskey` a tényleges névterű közös hozzáférésű Jogosultságkód (SAS) kulcs nevét, és a kulcs értékét.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Szerezze be az értékeket a SAS-kulcs nevét, és értéket a [Azure-portálon][Azure portal].

```python
bus_service.create_topic('mytopic')
```

A `create_topic` metódus is támogatja a további beállításokat, amelyek lehetővé teszik a bírálja felül az alapértelmezett témakör beállításokat, például üzenet time to live vagy maximális témakör ezen méretét. A következő példa a témakör maximális méretét 5 GB, és egyesével élettartam (TTL) értéke 1 perc állítja be:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Előfizetések témakörök is jönnek létre a **ServiceBusService** objektum. Előfizetések vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorában kézbesített üzenetek korlátoz.

> [!NOTE]
> Előfizetések állandó, és továbbra is léteznek, amíg azokat, vagy a témakört, amelyre előfizetett, a rendszer törli.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
A **MatchAll** szűrő az alapértelmezett szűrő, amely akkor használatos, ha nincs meghatározva szűrő egy új előfizetés létrehozásakor. Ha a **MatchAll** szűrővel, a témakörbe közzétett összes üzenetet kerülnek, az előfizetés virtuális üzenetsorában. A következő példa egy nevű előfizetést hoz létre `AllMessages` , és használja az alapértelmezett **MatchAll** szűrő.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Is meghatározhat, amelyek lehetővé teszik, hogy adja meg, hogy szűrők egy témakörbe küldött üzenetek belül egy adott üzenettémakör-előfizetésben kell megjeleníteni.

A szűrő előfizetések által támogatott legrugalmasabb típusú egy **SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkat az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisa tartalmaz.

Előfizetés szűrők a segítségével hozzáadhatók a **létrehozása\_szabály** metódusában a **ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrők hozzáadása egy meglévő előfizetéshez.

> [!NOTE]
> Mivel minden új előfizetés automatikusan alkalmazza az alapértelmezett szűrő, távolítsa el az alapértelmezett szűrő vagy a **MatchAll** felülírják más szűrőket is megadhat. Az alapértelmezett szabály eltávolításához használja a `delete_rule` metódusában a **ServiceBusService** objektum.
> 
> 

A következő példa egy nevű előfizetést hoz létre `HighMessages` rendelkező egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `messagenumber` tulajdonságának értéke nagyobb, mint 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Hasonlóképpen, a következő példa egy nevű előfizetést hoz létre `LowMessages` rendelkező egy **SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `messagenumber` tulajdonságának értéke kisebb vagy egyenlő, mint 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Most, amikor egy üzenetet küld `mytopic` mindig biztosítását előfizetett fogadó számára a **AllMessages** üzenettémakör-előfizetésre, és szelektív módon kézbesíti a a **HighMessages**és **LowMessages** (attól függően, hogy az üzenet tartalma) üzenettémakör-előfizetéseket.

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus-témakörbe való üzenetküldéshez az alkalmazást kell használnia a `send_topic_message` metódusában a **ServiceBusService** objektum.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az `mytopic`. Vegye figyelembe, hogy a `messagenumber` minden üzenetet tulajdonság értékének meg az a ciklus ismétléseinek számától függően változik (Ez határozza meg, melyik előfizetések fogják megkapni):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetés
Üzenetek fogadása egy előfizetés használatával a `receive_subscription_message` metódust a **ServiceBusService** objektum:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Üzenetek törlődnek az előfizetésből, mikor olvasott a paramétere `peek_lock` értékre van állítva **hamis**. (A betekintés) olvashatja, és az üzenet törlése az üzenetsorból úgy, hogy a paraméter nélkül zárolása `peek_lock` való **igaz**.

Olvasási és az üzenet törlése a fogadási művelet részeként viselkedését a legegyszerűbb modell, és működik a legjobban az forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet meghibásodása. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Ha a `peek_lock` paraméter értéke **igaz**, a receive két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával `delete` metódust a **üzenet** objektum. A `delete` metódus az üzenetet, feldolgozottként jelöli meg, és eltávolítása az előfizetésből.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a `unlock` metódust a **üzenet** objektum. Ennek hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van lévő az előfizetéshez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási előtt időkorlát lejárta (például, ha az alkalmazás összeomlik), majd a Service Bus automatikusan feloldja az üzenet és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a `delete` metódust, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer dolgoz fel, de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **MessageId** az üzenet, amely állandó marad a kézbesítési kísérletek tulajdonságát.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Üzenettémák és előfizetések következetesek, és explicit módon kell lennie vagy törölte a [Azure-portálon] [ Azure portal] vagy programon keresztül. A következő példa bemutatja, hogyan nevű üzenettémakör törlése `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan nevű előfizetés törlése `HighMessages` a a `mytopic` témakör:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, az alábbi hivatkozásokból további.

* Lásd: [üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].
* A hivatkozási [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
