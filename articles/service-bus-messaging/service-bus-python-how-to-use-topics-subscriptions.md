---
title: 'Gyors útmutató: Azure Service Bus témák használata a Python használatával'
description: 'Gyors útmutató: megtudhatja, hogyan használhatja a Pythonból Azure Service Bus témaköröket és előfizetéseket.'
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 8f7d47879a025742dbca6a5cafa634899e60ee68
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719181"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata a Python használatával

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk a Service Bus-üzenettémakörök és -előfizetések használatát ismerteti. A minták Pythonban íródnak, és az [Azure PYTHON SDK-csomagot][Azure Python package]használják. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése egy témakörnek 
- Üzenetek fogadása egy előfizetésből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörhöz](service-bus-quickstart-topics-subscriptions-portal.md) , és hozzon létre egy Service Bus **névteret** , és szerezze be a **kapcsolódási karakterláncot**.

    > [!NOTE]
    > Ebben a rövid útmutatóban létre fog hozni egy **témakört** és egy **előfizetést** a témakörhöz a **Python** használatával. 
3. Telepítse az [Azure Python-csomagot][Azure Python package]. Lásd a [Python telepítési útmutatóját](/azure/python/python-sdk-azure-install).

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A **ServiceBusService** objektum lehetővé teszi, hogy a témakörökkel működjön. Adja hozzá a következő kódot bármely olyan Python-fájlhoz, amelyben programozott módon szeretné elérni Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

A következő kód létrehoz egy **ServiceBusService** objektumot. Cserélje le `mynamespace`, `sharedaccesskeyname`és `sharedaccesskey` a tényleges névtér, a közös hozzáférésű aláírás (SAS) kulcsának nevére és a kulcs értékére.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Az SAS-kulcs nevének és értékének értékét a [Azure Portal][Azure portal]szerezheti be.

```python
bus_service.create_topic('mytopic')
```

A `create_topic` metódus további lehetőségeket is támogat, amelyek segítségével felülbírálhatja az alapértelmezett témakör-beállításokat, például az üzenetek élettartamát vagy a témakörök maximális méretét. A következő példa a témakör maximális méretét 5 GB-ra állítja, az élettartam (TTL) értéke pedig egy perc:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása

A témakörökre vonatkozó előfizetések szintén a **ServiceBusService** objektummal jönnek létre. Az előfizetések neve és egy opcionális szűrő, amely korlátozza az előfizetés virtuális várólistára kézbesített üzenetek készletét.

> [!NOTE]
> Alapértelmezés szerint az előfizetések állandóak, és addig maradnak érvényben, amíg meg nem történik, vagy a téma, amelyre előfizetett, törölve lesznek.
> 
> Az előfizetéseket a [auto_delete_on_idle tulajdonság](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python)beállításával automatikusan törölheti.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel

Ha nincs megadva szűrő az új előfizetés létrehozásakor, a rendszer a **MatchAll** szűrőt (alapértelmezett) használja. A **MatchAll** szűrő használatakor a témakörben közzétett összes üzenet az előfizetés virtuális várólistáján lesz elhelyezve. A következő példa létrehoz egy `AllMessages` nevű előfizetést, és az alapértelmezett **MatchAll** -szűrőt használja.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel

Meghatározhatja azokat a szűrőket is, amelyek segítségével megadhatja, hogy a témakörbe küldött üzenetek egy adott témakör-előfizetésen belül jelenjenek meg.

Az előfizetések által támogatott legrugalmasabb típusú szűrő egy **SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért lásd az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisát.

Az előfizetéshez szűrőket adhat hozzá a **ServiceBusService** objektum **create\_Rule** metódusának használatával. Ez a módszer lehetővé teszi új szűrők hozzáadását egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrőt a rendszer automatikusan alkalmazza az összes új előfizetésre, először el kell távolítania az alapértelmezett szűrőt, vagy a **MatchAll** felülbírálja az esetlegesen megadható egyéb szűrőket is. Az alapértelmezett szabályt a **ServiceBusService** objektum `delete_rule` metódusának használatával távolíthatja el.
> 
> 

Az alábbi példa egy `HighMessages` nevű előfizetést hoz létre egy olyan **SqlFilter** , amely csak olyan üzeneteket jelöl ki, amelyeken a 3 értéknél nagyobb egyéni `messagenumber` tulajdonság szerepel:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Hasonlóképpen, az alábbi példa egy `LowMessages` nevű előfizetést hoz létre egy **SqlFilter** , amely csak olyan üzeneteket választ ki, amelyek `messagenumber` tulajdonsága kisebb vagy egyenlő, mint 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Most, hogy a rendszer elküld egy üzenetet `mytopic` a rendszer mindig kézbesíti a **AllMessages** témakör-előfizetésre előfizetett fogadóknak, és szelektíven kézbesíti azokat a fogadóknak, akik előfizetettek a **HighMessages** és a **LowMessages** témakörre előfizetések (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

Ha üzenetet szeretne küldeni egy Service Bus témakörnek, az alkalmazásnak a **ServiceBusService** objektum `send_topic_message` metódusát kell használnia.

Az alábbi példa bemutatja, hogyan küldhet öt tesztüzenet `mytopic`ba. Az egyes üzenetek `messagenumber` tulajdonságának értéke a hurok iterációjában változik (ez határozza meg, hogy mely előfizetések kapják meg):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

Az üzenetek egy előfizetésből érkeznek a **ServiceBusService** objektum `receive_subscription_message` metódusának használatával:

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

A rendszer az előfizetésből törli az üzeneteket, amikor a `peek_lock` paraméter **false**értékre van állítva. A (z) és a (z) paramétert az üzenetsor törlésével anélkül is elolvashatja, hogy az `peek_lock` az **igaz**értékre állítja.

A fogadási művelet részeként az üzenet olvasásának és törlésének viselkedése a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A viselkedés megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Ha a `peek_lock` paraméter értéke TRUE ( **igaz**), a fogadás kétlépéses művelet lesz, ami lehetővé teszi a hiányzó üzeneteket nem toleráló alkalmazások támogatását. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a fogadja a fogadási folyamat második szakaszát úgy, hogy meghívja `delete` metódust az **üzenet** objektumon. A `delete` metódus az üzenetet felhasználja, és eltávolítja az előfizetésből.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívja a `unlock` metódust az **üzenet** objektumon. Ezzel a módszerrel Service Bus az üzenet zárolásának feloldására az előfizetésen belül, és elérhetővé teheti, hogy ugyanazt a fogyasztó vagy egy másik alkalmazás használja.

Az előfizetésen belül zárolt üzenethez is tartozik időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `delete` metódus meghívása előtt, az üzenetet a rendszer az újraindításkor visszaküldi az alkalmazásnak. Ezt a viselkedést gyakran nevezik. Legalább egyszer feldolgozás\*; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ehhez használhatja az üzenet **MessageID** tulajdonságát is, amely állandó marad a kézbesítési kísérletek között.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése

A témakörök és az előfizetések állandóak, kivéve, ha a [auto_delete_on_idle tulajdonság](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) be van állítva. A [Azure Portal][Azure portal] vagy programozott módon törölhetők. Az alábbi példa bemutatja, hogyan törölheti `mytopic`nevű témakört:

```python
bus_service.delete_topic('mytopic')
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan törölhet `HighMessages` nevű előfizetést a `mytopic` témakörből:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte Service Bus témakörök alapjait, kövesse az alábbi hivatkozásokat további információért.

* Lásd: [várólisták, témakörök és előfizetések][Queues, topics, and subscriptions].
* A [SqlFilter. SqlExpression][SqlFilter.SqlExpression]dokumentációja.

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
