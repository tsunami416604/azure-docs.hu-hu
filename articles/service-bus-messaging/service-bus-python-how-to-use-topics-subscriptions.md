---
title: Az Azure Service Bus-üzenettémakörök használata pythonnal |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Service Bus-üzenettémák és előfizetések a Python.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/20/2018
ms.author: sethm
ms.openlocfilehash: 6f262a63de9409d6b355d9783ca958e4715b1ea5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38479316"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Service Bus-üzenettémák és előfizetések használata pythonnal

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk a Service Bus-üzenettémakörök és -előfizetések használatát ismerteti. A minták a Python és -felhasználási nyelven írták az [Azure Python SDK-csomagot][Azure Python package]. Az ismertetett forgatókönyvek között megtalálható **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzenetek küldése egy üzenettémakörbe**, **fogadása előfizetés üzeneteit**, és **üzenettémák és előfizetések törlése**. Üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd: a [lépések](#next-steps) szakaszban.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Ha Python telepítenie kell, vagy a [Azure Python-csomag][Azure Python package], tekintse meg a [Python-telepítési útmutató](../python-how-to-install.md).

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A **ServiceBusService** objektum lehetővé teszi, hogy a témakörök. Adja hozzá a következő kódot bármely Python-fájlt, amelyben a kívánt programozott módon érheti el a Service Bus tetején:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Az alábbi kód létrehoz egy **ServiceBusService** objektum. Cserélje le `mynamespace`, `sharedaccesskeyname`, és `sharedaccesskey` a tényleges névtérrel rendelkező közös hozzáférésű Jogosultságkód (SAS) kulcs nevének és a kulcs értékét.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SAS-kulcs neve értékeinek lekéréséhez, és értéket a [az Azure portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

A `create_topic` módszer is támogatja a további beállítások, így lehetővé teszi, például az üzenet élettartamának idején vagy a témakör maximális méretének alapértelmezett témakör beállításainak felülbírálására. Az alábbi példa a témakör maximális mérete 5 GB-os és egy időt élettartam (TTL) értékének egy percig állítja be:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása

Témakörök, előfizetések is jönnek létre az a **ServiceBusService** objektum. Előfizetés neve, és rendelkezhetnek olyan szűrőkkel, amelyek az előfizetés virtuális üzenetsorának üzenetet korlátoz.

> [!NOTE]
> Előfizetések állandó, és továbbra is megmarad, amíg azokat, vagy a témakört, amelyre előfizetett, a rendszer törli.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel

A **MatchAll** szűrő az alapértelmezett szűrő, amely akkor használatos, ha nincs meghatározva szűrő egy új előfizetés létrehozásakor. Ha a **MatchAll** szűrőt használ, a témakörbe közzétett összes üzenetet az előfizetés virtuális üzenetsorának vannak elhelyezve. Az alábbi példa egy nevű előfizetést hoz létre `AllMessages` , és használja az alapértelmezett **MatchAll** szűrőt.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel

Is meghatározhat szűrőket, amelyek lehetővé teszik annak meghatározását, amely egy témakörbe küldött üzenetek egy adott témakör-előfizetésben kell megjeleníteni.

A legrugalmasabb típusú szűrő előfizetések által támogatott egy **SqlFilter**, amely megvalósítja az SQL92 egy részhalmazát. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkat az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisa tartalmaz.

Az előfizetéshez szűrők is hozzáadhat a **létrehozása\_szabály** módszere a **ServiceBusService** objektum. Ez a módszer lehetővé teszi új szűrőket hozzáadni egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrő automatikusan alkalmazza minden új előfizetés, távolítsa el az alapértelmezett szűrő vagy a **MatchAll** bármely más szűrők, megadhatja azt felülírja. Eltávolíthatja az alapértelmezett szabály használatával a `delete_rule` módszere a **ServiceBusService** objektum.
> 
> 

Az alábbi példa egy nevű előfizetést hoz létre `HighMessages` együtt egy **SqlFilter** , amely csak választja ki, amelyek egyéni üzenetek `messagenumber` 3-nál nagyobb tulajdonság:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Hasonlóképpen, az alábbi példa egy nevű előfizetést hoz létre `LowMessages` az egy **SqlFilter** , amely csak rendelkező üzeneteket választja ki egy `messagenumber` tulajdonság kisebb vagy egyenlő, mint 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Mostantól, ha egy üzenetet küld `mytopic` , akkor mindig kézbesíti az üzenetet az előfizetett a **AllMessages** témakör-előfizetés, és szelektív módon kézbesíti a **HighMessages**és **LowMessages** üzenettémakör-előfizető (függően a tartalmukat).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

Egy üzenetet küld egy Service Bus-témakörbe, az alkalmazás használatához a `send_topic_message` módszere a **ServiceBusService** objektum.

Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az `mytopic`. A `messagenumber` egyes üzenetek tulajdonság értéke a ciklus ismétléseinek a változik (Ez határozza meg, melyik előfizetések megkapni):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

Üzenetek kapott egy előfizetés az a `receive_subscription_message` metódust a **ServiceBusService** objektum:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Üzenetek törlése az előfizetésből, amikor olvasott a paraméter `peek_lock` értékre van állítva **hamis**. Olvashat (betekintési) és az üzenet zárolása törlése az üzenetsorból paraméterének beállításával nélkül `peek_lock` való **igaz**.

Olvasása és törlése a fogadás művelet részeként viselkedését a legegyszerűbb modell, és a leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Ez a viselkedés megismeréséhez, fontolja meg egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Ha a `peek_lock` paraméter értéke **igaz**, a receive két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), `delete` metódust a **üzenet** objektum. A `delete` módszer jelöli meg az üzenetet, és eltávolítja azt az előfizetést.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az `unlock` metódust a **üzenet** objektum. Ez a módszer hatására a Service Bus feloldja az üzenet az előfizetésen belüli zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy előfizetésen belül zárolva üzenethez társított időtúllépés, és az alkalmazás nem tudja feldolgozni az üzenetet a zárolási előtt időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a `delete` módszert hívja meg, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ez a jelenség gyakran nevezik *legalább egyszeri feldolgozásnak*; azt jelenti, minden üzenetet legalább egyszer a program feldolgozza, de bizonyos esetekben a az ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ehhez használhatja a **üzenetazonosító** tulajdonság az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése

Üzenettémák és előfizetések állandóak, és explicit módon kell-e törölve keresztül a [az Azure portal] [ Azure portal] vagy programozott módon. Az alábbi példa bemutatja, hogyan lehet törölni a témakör nevű `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan törölhet egy nevű előfizetést `HighMessages` származó a `mytopic` témakör:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Service Bus-üzenettémakörök alapjait, kövesse az alábbi hivatkozások további.

* Lásd: [üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].
* A hivatkozás [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
