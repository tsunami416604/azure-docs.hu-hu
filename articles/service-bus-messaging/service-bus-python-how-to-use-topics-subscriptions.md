---
title: 'Gyors útmutató: Azure Service Bus témakörök és előfizetések használata a Python használatával'
description: Ez a cikk bemutatja, hogyan hozhat létre egy Azure Service Bus témakört, előfizetést, üzeneteket küldhet egy témakörnek, és üzeneteket fogadhat az előfizetésből.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88064383"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata a Python használatával

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk azt ismerteti, hogyan használható a Python Azure Service Bus témakörökkel és előfizetésekkel. A minták az [Azure PYTHON SDK][Azure Python package] -csomagot használják a következőhöz: 

- Témakörök és előfizetések létrehozása témakörökhöz
- Előfizetési szűrők és szabályok létrehozása
- Üzenetek küldése a témaköröknek 
- Üzenetek fogadása az előfizetésből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Egy Service Bus névtér, amely a gyors üzembe helyezés lépéseinek követésével jön létre [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket](service-bus-quickstart-topics-subscriptions-portal.md). Másolja a névtér nevét, a megosztott elérési kulcs nevét és az elsődleges kulcs értékét a **közös hozzáférésű házirendek** képernyőről a rövid útmutató későbbi részében való használatra. 
- Python 3.4 x vagy újabb verzió, telepített [Azure PYTHON SDK][Azure Python package] -csomaggal. További információ: a [Python telepítési útmutatója](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>ServiceBusService objektum létrehozása

A **ServiceBusService** objektummal témaköröket és előfizetéseket dolgozhat a témakörökben. A Service Bus programozott eléréséhez adja hozzá a következő sort a Python-fájl elejéhez:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adja hozzá a következő kódot egy **ServiceBusService** objektum létrehozásához. Cserélje le a, a `<namespace>` `<sharedaccesskeyname>` és a paramétert `<sharedaccesskeyvalue>` a Service Bus névtér nevére, a közös hozzáférési aláírás (SAS) kulcsának nevére és az elsődleges kulcs értékére. Ezeket az értékeket a [Azure Portal][Azure portal]Service Bus névterében található **megosztott hozzáférési házirendek** területen találja.

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A következő kód a `create_topic` metódus használatával hozza létre a nevű Service Bus témakört `mytopic` alapértelmezett beállításokkal:

```python
bus_service.create_topic('mytopic')
```

A témakör beállításai segítségével felülbírálhatja az alapértelmezett témakör-beállításokat, például az üzenetek élettartamát (TTL) vagy a témakör maximális méretét. Az alábbi példa egy nevű témakört hoz létre, amely egy percen belüli, maximális számú témakört `mytopic` tartalmaz:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása

A **ServiceBusService** objektummal előfizetéseket is létrehozhat a témakörökhöz. Egy előfizetéshez tartozhat egy szűrő, amellyel korlátozható a virtuális várólistára küldött üzenet. Ha nem ad meg szűrőt, az új előfizetések az alapértelmezett **MatchAll** szűrőt használják, amely a témakörben közzétett összes üzenetet elhelyezi az előfizetés virtuális várólistáján. A következő példa létrehoz egy nevű előfizetést, `mytopic` `AllMessages` amely a **MatchAll** szűrőt használja:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Szűrők használata előfizetésekkel

A `create_rule` **ServiceBusService** objektum metódusának használatával szűrheti az előfizetésben megjelenő üzeneteket. Megadhatja az előfizetés létrehozásakor vagy a meglévő előfizetésekhez tartozó szabályok hozzáadására vonatkozó szabályokat.

A legrugalmasabb típusú szűrő egy **SqlFilter**, amely az SQL-92 részhalmazát használja. Az SQL-szűrők a témakörben közzétett üzenetek tulajdonságai alapján működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse meg a [SqlFilter. SqlExpression][SqlFilter.SqlExpression] szintaxist.

Mivel a **MatchAll** alapértelmezett szűrője automatikusan az összes új előfizetésre vonatkozik, el kell távolítania a szűrni kívánt előfizetések közül, vagy a **MatchAll** felülbírálja az Ön által megadott szűrőket. Az alapértelmezett szabályt a `delete_rule` **ServiceBusService** objektum metódusának használatával távolíthatja el.

Az alábbi példa egy nevű előfizetést hoz létre `mytopic` `HighMessages` egy nevű **SqlFilter** szabállyal `HighMessageFilter` . A `HighMessageFilter` szabály csak a `messageposition` 3-nál nagyobb egyéni tulajdonságú üzeneteket választja ki:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Az alábbi példa egy nevű előfizetést hoz létre `mytopic` `LowMessages` egy nevű **SqlFilter** szabállyal `LowMessageFilter` . A `LowMessageFilter` szabály csak a `messageposition` 3 értéknél kisebb vagy azzal egyenlő értékű üzeneteket jelöli ki:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

A `AllMessages` , a `HighMessages` és az `LowMessages` összes hatására a `mytopic` rendszer mindig az előfizetés fogadóinak kézbesíti az elküldött üzeneteket `AllMessages` . Az üzenetek a (z `HighMessages` ) vagy előfizetéshez is szelektív módon érkeznek `LowMessages` , az üzenet `messageposition` tulajdonságának értékétől függően. 

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

Az alkalmazások a `send_topic_message` **ServiceBusService** objektum metódusát használják üzenetek küldésére egy Service Bus témakörben.

A következő példa öt tesztüzenet küldését küldi el a `mytopic` témakörnek. Az egyéni `messageposition` tulajdonság értéke a hurok iterációtól függ, és meghatározza, hogy mely előfizetések kapják meg az üzeneteket. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Az üzenet méretének korlátai és kvótái

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörben megtartható üzenetek száma nem korlátozott, de a témakörben szereplő üzenetek teljes méretének korlátja van. A témakör mérete a létrehozáskor adható meg, és a felső korlátja 5 GB. 

További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

Az alkalmazások az `receive_subscription_message` **ServiceBusService** objektum metódusát használják egy előfizetésből érkező üzenetek fogadására. Az alábbi példa üzeneteket fogad az `LowMessages` előfizetésből, és törli őket az olvasáskor:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

A választható `peek_lock` paraméter `receive_subscription_message` határozza meg, hogy Service Bus törli-e az előfizetésből származó üzeneteket az olvasáskor. Az üzenetek fogadásának alapértelmezett módja a *PeekLock*, vagy `peek_lock` **igaz**értékre van állítva, amely beolvassa (betekintést) és zárolja az üzeneteket anélkül, hogy törölné őket az előfizetésből. Ezután minden üzenetet explicit módon el kell végezni, hogy el lehessen távolítani az előfizetésből.

Ha törölni szeretné az előfizetésből származó üzeneteket, a `peek_lock` paramétert **hamis**értékre állíthatja, ahogy az előző példában is látható. Ha a fogadási művelet részeként törli az üzeneteket, a legegyszerűbb modell, és jól működik, ha az alkalmazás meghibásodás esetén képes elviselni a hiányzó üzeneteket. A viselkedés megértéséhez Vegyünk egy olyan forgatókönyvet, amelyben az alkalmazás egy fogadási kérést ad ki, majd összeomlik a feldolgozás előtt. Ha a rendszer törölte az üzenetet a fogadáskor, amikor az alkalmazás újraindul, és megkezdi az üzenetek újrafelhasználását, az összeomlás előtt elmulasztotta a kapott üzenetet.

Ha az alkalmazás nem tudja elviselni a kihagyott üzeneteket, a fogadás kétfázisú műveletvé válik. A PeekLock megkeresi a használni kívánt következő üzenetet, zárolja, hogy megakadályozza más fogyasztók számára a fogadást, és visszaadja az alkalmazásnak. Az üzenet feldolgozása vagy tárolása után az alkalmazás végrehajtja a fogadási folyamat második szakaszát úgy, hogy meghívja a `complete` metódust az **üzenet** objektumon.  A `complete` metódus felhasználja az üzenetet, és eltávolítja azt az előfizetésből.

Az alábbi példa egy betekintés zárolási forgatókönyvet mutat be:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tud feldolgozni egy üzenetet, meghívja a `unlock` metódust az **üzenet** objektumon. Service Bus feloldja az üzenet zárolását az előfizetésen belül, és lehetővé teszi az újbóli fogadását, akár ugyanazon, akár egy másik alkalmazásban.

Az előfizetésen belül zárolt üzenetek esetében is van időtúllépés. Ha egy alkalmazás nem tud feldolgozni egy üzenetet a zárolási időtúllépés lejárta előtt, például ha az alkalmazás összeomlik, Service Bus feloldja az üzenet automatikus zárolását, és elérhetővé teszi azt újra.

Ha egy alkalmazás egy üzenet feldolgozása után összeomlik, de a metódus meghívása előtt `complete` , a rendszer az újraindításkor visszaküldi az üzenetet az alkalmazásnak. Ezt a viselkedést gyakran egyszeri *feldolgozásnak*nevezik. Minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, használhatja az üzenet **MessageID** tulajdonságát, amely állandó marad a kézbesítési kísérletek között, hogy kezelni tudja a duplikált üzenetek kézbesítését. 

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése

Témakörök és előfizetések törléséhez használja a [Azure Portal][Azure portal] vagy a `delete_topic` metódust. A következő kód törli a nevű témakört `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

Egy témakör törlése törli a témakör összes előfizetését. Az előfizetéseket egymástól függetlenül is törölheti. A következő kód törli a nevű előfizetést `HighMessages` a `mytopic` témakörből:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Alapértelmezés szerint a témakörök és az előfizetések állandóak, és addig léteznek, amíg nem törli őket. Ha az előfizetéseket egy bizonyos időtartam lejárta után szeretné automatikusan törölni, beállíthatja a [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) paramétert az előfizetésben. 

> [!TIP]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. Service Bus Explorer lehetővé teszi egy Service Bus névtérhez való kapcsolódást és az üzenetkezelési entitások egyszerű felügyeletét. Az eszköz olyan speciális funkciókat biztosít, mint például az importálási/exportálási funkciók, a témakörök, a várólisták, az előfizetések, a továbbító szolgáltatások, az értesítési központok és az Event hub-eszközök tesztelése. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte Service Bus témakörök alapjait, kövesse az alábbi hivatkozásokat, ahol további információt talál:

* [Queues, topics, and subscriptions][Queues, topics, and subscriptions]
* [SqlFilter. SqlExpression][SqlFilter.SqlExpression] -hivatkozás

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md