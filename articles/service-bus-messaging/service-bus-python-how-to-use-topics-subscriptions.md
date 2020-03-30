---
title: 'Rövid útmutató: Az Azure Service Bus témaköreinek és előfizetései a Pythonnal'
description: Ez a cikk bemutatja, hogyan hozhat létre egy Azure Service Bus-témakört, előfizetést, üzeneteket küldhet egy témakörnek, és hogyan fogadhat üzeneteket az előfizetésből.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 03e22c4c179850e5140015c0abc2d89f16d4b624
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76774541"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Rövid útmutató: A Service Bus-témakörök és előfizetések használata a Pythonnal

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk ismerteti, hogyan használhatja a Python az Azure Service Bus-témakörök és előfizetések. A minták az [Azure Python SDK-csomagot][Azure Python package] használják a következőkre: 

- Témakörök és témakörökre való előfizetések létrehozása
- Előfizetési szűrők és szabályok létrehozása
- Üzenetek küldése témakörökbe 
- Üzenetek fogadása előfizetésekből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja visual [studio- vagy MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- A Service Bus névtér, a rövid útmutató lépései alapján [létrehozott: Az Azure Portal használatával hozzon létre egy Service Bus-témakört és előfizetéseket.](service-bus-quickstart-topics-subscriptions-portal.md) Másolja a névtér nevét, a megosztott hozzáférés kulcsnevét és az elsődleges kulcs értékét a **Megosztott hozzáférési házirendek** képernyőre, amelyet a rövid útmutató későbbi részében szeretne használni. 
- Python 3.4x vagy újabb, az [Azure Python SDK-csomag][Azure Python package] telepítve. További információt a [Python telepítési útmutatójában talál.](/azure/python/python-sdk-azure-install)

## <a name="create-a-servicebusservice-object"></a>ServiceBusService-objektum létrehozása

A **ServiceBusService** objektum lehetővé teszi, hogy a témakörök és a témakörök előfizetések. A Service Bus programozott eléréséhez adja hozzá a következő sort a Python-fájl tetejéhez:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Adja hozzá a következő kódot egy **ServiceBusService** objektum létrehozásához. Cserélje `<namespace>` `<sharedaccesskeyname>`le `<sharedaccesskeyvalue>` a , és a Service Bus névtér nevét, a Megosztott hozzáférésű aláírás (SAS) kulcsnevét és az elsődleges kulcs értékét. Ezeket az értékeket a Service Bus-névtér **megosztott hozzáférési szabályzatai** az [Azure Portalon][Azure portal]található megosztott hozzáférési szabályzatok alatt találja.

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A következő kód `create_topic` a metódus segítségével hoz `mytopic`létre egy Service Bus témakört, amelynek neve alapértelmezett beállításokkal van eltorpanása:

```python
bus_service.create_topic('mytopic')
```

A témakörbeállításaival felülbírálhatja az alapértelmezett témakörbeállításokat, például az üzenet teljesítési idejét (TTL) vagy a témakör maximális méretét. A következő példa létrehoz `mytopic` egy témakört, amelynek maximális témakörmérete 5 GB, és az alapértelmezett üzenet TTL egy perc:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása

A **ServiceBusService** objektum használatával is hozhat létre témakörökelőfizetéseket. Az előfizetések rendelkezhetnek egy szűrővel, amely korlátozza a virtuális várólistába küldött üzenetkészletet. Ha nem ad meg szűrőt, az új előfizetések az alapértelmezett **MatchAll** szűrőt használják, amely a témakörben közzétett összes üzenetet az előfizetés virtuális várólistájába helyezi. A következő példa létrehoz `mytopic` `AllMessages` egy elnevezett előfizetést, amely a **MatchAll** szűrőt használja:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Szűrők használata előfizetésekkel

A `create_rule` **ServiceBusService** objektum módszerével szűrheti az előfizetésben megjelenő üzeneteket. Az előfizetés létrehozásakor szabályokat adhat meg, vagy hozzáadhat szabályokat a meglévő előfizetésekhez.

A legrugalmasabb szűrőtípus az **SqlFilter**, amely az SQL-92 egy részét használja. Az SQL-szűrők a témakörben közzétett üzenetek tulajdonságai alapján működnek. Az SQL-szűrővel használható kifejezésekről további információt az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisban talál.

Mivel a **MatchAll** alapértelmezett szűrő automatikusan vonatkozik az összes új előfizetésre, el kell távolítania a szűrni kívánt előfizetésekből, különben a **MatchAll** felülírja a többi megadott szűrőt. Az alapértelmezett szabályt eltávolíthatja `delete_rule` a **ServiceBusService** objektum metódusával.

A következő példa létrehoz `mytopic` `HighMessages`egy előfizetést a `HighMessageFilter`névvel ellátott , **egy SqlFilter** szabály nevű . A `HighMessageFilter` szabály csak a 3-nál nagyobb egyéni `messageposition` tulajdonsággal rendelkező üzeneteket jelöli ki:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

A következő példa létrehoz `mytopic` `LowMessages`egy előfizetést a `LowMessageFilter`névvel ellátott , **egy SqlFilter** szabály nevű . A `LowMessageFilter` szabály csak azokat `messageposition` az üzeneteket jelöli ki, amelyek tulajdonsága legfeljebb 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

A `AllMessages` `HighMessages`, `LowMessages` és minden érvényben, `mytopic` küldött üzenetek mindig az `AllMessages` előfizetés fogadói. Az üzenetek szelektíven is `HighMessages` `LowMessages` kézbesítve vannak az üzenet `messageposition` vagy az előfizetés számára, az üzenet tulajdonságértékétől függően. 

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

Az alkalmazások `send_topic_message` a **ServiceBusService** objektum metódusát használják a Service Bus-témakörbe való üzenetküldéshez.

A következő példa öt tesztüzenetet küld a `mytopic` témakörnek. Az `messageposition` egyéni tulajdonság értéke a ciklus iterációját, és meghatározza, hogy mely előfizetések az üzeneteket. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Az üzenetek méretkorlátai és kvótái

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Nincs korlátozva, hogy egy témakör hány üzenetet tarthat, de a témakörben lévő üzenetek teljes méretét korlátozza. A témakör méretét a létrehozás időpontjában, 5 GB-os felső határral határozhatja meg. 

A kvótákról további információt a [Service Bus-kvóták című témakörben talál.][Service Bus quotas]

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása előfizetésből

Az alkalmazások `receive_subscription_message` a **ServiceBusService** objektum on the Method használatával üzeneteket fogadni egy előfizetésből. A következő példa üzeneteket `LowMessages` fogad az előfizetésből, és az olvasás közben törli őket:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

A `peek_lock` választható `receive_subscription_message` paraméter határozza meg, hogy a Service Bus törli-e az üzeneteket az előfizetésből olvasáskor. Az üzenetek fogadásának alapértelmezett módja `peek_lock` a *PeekLock*, vagy a **True**érték, amely beolvassa (betekint) és zárolja az üzeneteket anélkül, hogy kiolvasná őket az előfizetésből. Minden üzenetet explicit módon ki kell tölteni, hogy eltávolítsuk az előfizetésből.

Ha olvasás közben törölni szeretné az üzeneteket az `peek_lock` előfizetésből, beállíthatja a paramétert **Hamis**, mint az előző példában. Az üzenetek törlése a fogadási művelet részeként a legegyszerűbb modell, és jól működik, ha az alkalmazás elviseli a hiányzó üzeneteket, ha hiba van. A viselkedés megértéséhez fontolja meg egy olyan forgatókönyvet, amelyben az alkalmazás fogadási kérelmet ad ki, majd a feldolgozás előtt összeomlik. Ha az üzenetet a fogadáskor törölték, amikor az alkalmazás újraindul, és újra elkezdi az üzenetek et, az összeomlás előtt fogadott üzenetet kihagyta.

Ha az alkalmazás nem tudja elviselni a nem fogadott üzeneteket, a fogadás kétlépcsős műveletté válik. PeekLock megkeresi a következő üzenetet kell fogyasztani, zárolja, hogy megakadályozzák más fogyasztók fogadására, és visszaküldi azt az alkalmazásnak. Az üzenet feldolgozása vagy tárolása után az alkalmazás befejezi a fogadási `complete` folyamat második szakaszát az **Üzenet** objektum metódusának hívásával.  A `complete` metódus az üzenetet felhasználásként jelöli meg, és eltávolítja azt az előfizetésből.

A következő példa egy betekintőzárolási forgatókönyvet mutat be:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Alkalmazásösszeomlások és olvashatatlan üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, meghívhatja a `unlock` metódust az **Üzenet** objektumon. A Service Bus feloldja az üzenetet az előfizetésen belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanaz, akár egy másik fogyasztó alkalmazás.

Van is egy időtúltöltés az előfizetésen belül zárolt üzenetek. Ha egy alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt, például ha az alkalmazás összeomlik, a Service Bus automatikusan feloldja az üzenetet, és elérhetővé teszi, hogy újra megkapja.

Ha egy alkalmazás összeomlik egy üzenet `complete` feldolgozása után, de a metódus hívása előtt, az üzenet újra kézbesítésre kerül az alkalmazásnak, amikor újraindul. Ezt a viselkedést gyakran *nevezik Legalább egyszeri feldolgozásnak.* Minden üzenet feldolgozása legalább egyszer történik, de bizonyos helyzetekben ugyanaz tetsző üzenet kézbesíthető. Ha a forgatókönyv nem tolerálja a duplikált feldolgozást, használhatja az üzenet **MessageId** tulajdonságát, amely a kézbesítési kísérletek során állandó marad, az ismétlődő üzenetek kézbesítésének kezeléséhez. 

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése

Témakörök és előfizetések törléséhez használja az `delete_topic` [Azure Portalon][Azure portal] vagy a metódust. A következő kód törli `mytopic`a témakör neve:

```python
bus_service.delete_topic('mytopic')
```

A témakör törlése törli a témakör összes előfizetését. Az előfizetéseket egymástól függetlenül is törölheti. A következő kód törli `HighMessages` a `mytopic` témakörből megnevezett előfizetést:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Alapértelmezés szerint a témakörök és az előfizetések állandóak, és a törlésükig léteznek. Ha egy bizonyos idő eltelte után automatikusan törölni szeretné az előfizetéseket, beállíthatja az [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) paramétert az előfizetésen. 

> [!TIP]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi, hogy csatlakozzon egy Service Bus-névtérhez, és egyszerűen felügyelje az üzenetkezelési entitásokat. Az eszköz speciális funkciókat, például importálási/exportálási funkciókat, valamint témakörök, várólisták, előfizetések, továbbítási szolgáltatások, értesítési központok és eseményközpontok tesztelését teszi lehetővé. 

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a Service Bus témaköreinek alapjait, kövesse az alábbi hivatkozásokat, ha többet szeretne megtudni:

* [Várólisták, témakörök és előfizetések][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] – referencia

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
