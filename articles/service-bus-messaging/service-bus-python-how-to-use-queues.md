---
title: "Azure Service Bus-üzenetsorok használata Python |} Microsoft Docs"
description: "Útmutató: a Python Azure Service Bus-üzenetsorok használata."
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm;lmazuel
ms.openlocfilehash: e1e81ad1d7b4fe0e044917f090cac59dfd5b6332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Service Bus-üzenetsorok használata Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk a Service Bus-üzenetsorok használatát ismerteti. A mintákat a Python és -felhasználási nyelven íródtak a [Python Azure Service Bus csomag][Python Azure Service Bus package]. Az ismertetett forgatókönyvek **üzenetsorok üzenetek küldése és fogadása létrehozása**, és **várólisták törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> Python telepítéséhez vagy a [Python Azure Service Bus csomag][Python Azure Service Bus package], tekintse meg a [Python a telepítési útmutató](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi a várólisták használata. Adja hozzá a következő kódot programon keresztüli eléréséhez a Service Bus kívánja bármely Python fájl felső részén:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

Az alábbi kód létrehoz egy **ServiceBusService** objektum. Cserélje le `mynamespace`, `sharedaccesskeyname`, és `sharedaccesskey` a névtér, közös hozzáférésű jogosultságkód (SAS) kulcs nevét és értékét.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

A SAS-kulcs nevét és értékét értékeit megtalálhatók a [Azure-portálon] [ Azure portal] kapcsolatadatok, vagy a Visual Studio **tulajdonságok** ablaktáblán, ha a szolgáltatás kiválasztása Szolgáltatásbusz-névtér a Server Explorer (ahogy az előző szakaszban).

```python
bus_service.create_queue('taskqueue')
```

A `create_queue` metódus is támogatja a további beállításokat, amelyek lehetővé teszik a bírálja felül az alapértelmezett várólista beállításait, például az üzenet time to live (TTL) vagy a várólista maximális hossza. A következő példa a várólista maximális hossza 5 GB, és az élettartam értéke 1 percre állítja be:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Egy üzenet küldhető egy Service Bus-üzenetsorba, az alkalmazás hívások a `send_queue_message` metódust a **ServiceBusService** objektum.

A következő példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `taskqueue` használatával `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Üzenetek fogadása egy várólista használja a `receive_queue_message` metódust a **ServiceBusService** objektum:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Üzenetek törlődnek a várólistából, mikor olvasott a paramétere `peek_lock` értéke **hamis**. (A betekintés) olvashatja, és az üzenet törlése az üzenetsorból úgy, hogy a paraméter nélkül zárolása `peek_lock` való **igaz**.

Olvasási és az üzenet törlése a fogadási művelet részeként viselkedését a legegyszerűbb modell, és működik a legjobban az forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet meghibásodása. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

Ha a `peek_lock` paraméter értéke **igaz**, a receive két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második szintjére meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), a **törlése** metódust a **üzenet** objektum. A **törlése** metódus lesz használnak az üzenetet, és távolítsa el a sorból.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a **zárolásának feloldásához** metódust a **üzenet** objektum. Ennek hatására a Service Bus feloldja az üzenet a várólistában zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (pl., ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és meg fogja teszi elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a **törlése** metódust, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik **legalább egyszeri feldolgozásnak**, ez azt jelenti, hogy minden üzenet legalább egyszer dolgoz fel, de bizonyos helyzetekben a a ugyanazon üzenet újbóli kézbesítése is lehet. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **MessageId** az üzenet, amely állandó marad a kézbesítési kísérletek tulajdonságát.

## <a name="next-steps"></a>Következő lépések
Most, hogy rendelkezik megismerte a Service Bus-üzenetsorok alapjait, tanulmányozza a további.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

