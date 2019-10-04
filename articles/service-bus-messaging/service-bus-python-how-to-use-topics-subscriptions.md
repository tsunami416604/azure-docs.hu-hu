---
title: Azure Service Bus témakörök használata a Python használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Pythonból Azure Service Bus témaköröket és előfizetéseket.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: ef0237b38c8f640c0fc4b1b1788215c8804a5cd4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141902"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Service Bus témakörök és előfizetések használata a Python használatával

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk a Service Bus-üzenettémakörök és -előfizetések használatát ismerteti. A minták Pythonban íródnak, és az [Azure PYTHON SDK-csomagot][Azure Python package]használják. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése egy témakörnek 
- Üzenetek fogadása egy előfizetésből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) előfizetői előnyeit, vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse a rövid útmutató [lépéseit: Az Azure Portal segítségével hozzon létre egy Service Bus témakört és előfizetéseket a témakörhöz](service-bus-quickstart-topics-subscriptions-portal.md) , és hozzon létre egy Service Bus **névteret** , és szerezze be a kapcsolódási **karakterláncot**.

    > [!NOTE]
    > Ebben a rövid útmutatóban létre fog hozni egy **témakört** és egy előfizetést a témakörhöz a **Python** használatával. 
3. Telepítse az [Azure Python-csomagot][Azure Python package]. Lásd a [Python telepítési útmutatóját](/azure/python/python-sdk-azure-install).

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A **ServiceBusService** objektum lehetővé teszi, hogy a témakörökkel működjön. Adja hozzá a következő kódot bármely olyan Python-fájlhoz, amelyben programozott módon szeretné elérni Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

A következő kód létrehoz egy **ServiceBusService** objektumot. Cserélje le `mynamespace`a, `sharedaccesskey` a és a értéket a tényleges névtér, a közös hozzáférésű aláírás (SAS) kulcsának nevére és a kulcs értékére. `sharedaccesskeyname`

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

Ha nincs megadva szűrő az új előfizetés létrehozásakor, a rendszer a **MatchAll** szűrőt (alapértelmezett) használja. A **MatchAll** szűrő használatakor a témakörben közzétett összes üzenet az előfizetés virtuális várólistáján lesz elhelyezve. A következő példa létrehoz egy nevű `AllMessages` előfizetést, és az alapértelmezett **MatchAll** szűrőt használja.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel

Meghatározhatja azokat a szűrőket is, amelyek segítségével megadhatja, hogy a témakörbe küldött üzenetek egy adott témakör-előfizetésen belül jelenjenek meg.

Az előfizetések által támogatott legrugalmasabb típusú szűrő egy **SqlFilter**, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért lásd az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisát.

Az előfizetéshez szűrőket adhat hozzá a **ServiceBusService** objektum **create\_Rule** metódusának használatával. Ez a módszer lehetővé teszi új szűrők hozzáadását egy meglévő előfizetéshez.

> [!NOTE]
> Mivel az alapértelmezett szűrőt a rendszer automatikusan alkalmazza az összes új előfizetésre, először el kell távolítania az alapértelmezett szűrőt, vagy a **MatchAll** felülbírálja az esetlegesen megadható egyéb szűrőket is. Az alapértelmezett szabályt a `delete_rule` **ServiceBusService** objektum metódusának használatával távolíthatja el.
> 
> 

Az alábbi példa egy nevű `HighMessages` előfizetést hoz létre egy **SqlFilter** , amely csak olyan üzeneteket választ `messagenumber` ki, amelyek 3. nagyobb egyéni tulajdonsággal rendelkeznek:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Hasonlóképpen, a következő példa létrehoz egy nevű `LowMessages` előfizetést egy **SqlFilter** , amely csak a `messagenumber` 3 értéknél kisebb vagy azzal egyenlő értékű üzeneteket jelöl ki:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Most, hogy a rendszer `mytopic` az üzenetet küldi el, mindig kézbesíti a **AllMessages** témakör-előfizetésre előfizetett fogadóknak, és szelektíven kézbesítve a **HighMessages** és a **LowMessages** témakörre előfizetett fogadóknak. előfizetések (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe

Ha üzenetet szeretne küldeni egy Service Bus témakörnek, az alkalmazásnak a `send_topic_message` **ServiceBusService** objektum metódusát kell használnia.

Az alábbi példa bemutatja, hogyan küldhet öt tesztüzenet a `mytopic`következőnek:. Az `messagenumber` egyes üzenetek tulajdonságának értéke a hurok iterációjában változik (ez határozza meg, hogy mely előfizetések kapják meg):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

Az üzenetek a `receive_subscription_message` **ServiceBusService** objektum metódusának használatával érkeznek az előfizetésből:

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Az üzenet törlődik az előfizetésből, mert a paraméter `peek_lock` **hamis**értékre van állítva. A paraméter `peek_lock` **true**értékre állításával elolvashatja (betekintést) és zárolhatja az üzenetet anélkül, hogy törölné azt a sorból.

A fogadási művelet részeként az üzenet olvasásának és törlésének viselkedése a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A viselkedés megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Ha a `peek_lock` paraméter értéke TRUE ( **igaz**), a fogadás kétlépéses művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a metódus meghívásával `delete` végrehajtja a fogadási folyamat második szakaszát. A `delete` metódus felhasználja az üzenetet, és eltávolítja azt az előfizetésből.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívja a `unlock` metódust az **üzenet** objektumon. Ezzel a módszerrel Service Bus az üzenet zárolásának feloldására az előfizetésen belül, és elérhetővé teheti, hogy ugyanazt a fogyasztó vagy egy másik alkalmazás használja.

Az előfizetésen belül zárolt üzenethez is tartozik időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, `delete` de a metódus hívása előtt, akkor az üzenet az újraindításkor újra bekerül az alkalmazásba. Ezt a viselkedést gyakran nevezik. Legalább egyszer feldolgozás\*; azaz minden üzenet feldolgozása legalább egyszer történik, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ehhez használhatja az üzenet **MessageID** tulajdonságát is, amely állandó marad a kézbesítési kísérletek között.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése

A témakörök és az előfizetések állandóak, kivéve, ha a [auto_delete_on_idle tulajdonság](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) be van állítva. A [Azure Portal][Azure portal] vagy programozott módon törölhetők. Az alábbi példa bemutatja, hogyan törölheti a nevű `mytopic`témakört:

```python
bus_service.delete_topic('mytopic')
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan törölhet egy nevű `HighMessages` előfizetést a `mytopic` témakörből:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte Service Bus témakörök alapjait, kövesse az alábbi hivatkozásokat további információért.

* Lásd: [várólisták, témakörök és][Queues, topics, and subscriptions]előfizetések.
* A [SqlFilter. SqlExpression][SqlFilter.SqlExpression]dokumentációja.

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
