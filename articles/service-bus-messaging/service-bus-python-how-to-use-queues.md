---
title: Az Azure Service Bus-üzenetsorok használata pythonnal |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Service Bus-üzenetsorok, a Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/25/2019
ms.author: aschhab
ms.openlocfilehash: 172fee19de77deb4ecf679d6884dfcea2a4968be
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865960"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Service Bus-üzenetsorok használata pythonnal

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk a Service Bus-üzenetsorok használatát ismerteti. A minták a Python és -felhasználási nyelven írták az [Python Azure Service Bus-csomag][Python Azure Service Bus package]. Az ismertetett forgatókönyvek között megtalálható **várólisták, üzenetek küldése és fogadása létrehozása**, és **üzenetsorok törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!IMPORTANT]
> Telepítse a Python, vagy a [Python Azure Service Bus-csomag][Python Azure Service Bus package], tekintse meg a [Python-telepítési útmutató](../python-how-to-install.md).
> 
> Tekintse meg a Service Bus Python SDK teljes dokumentációját [Itt](/python/api/overview/azure/servicebus?view=azure-python)


## <a name="create-a-queue"></a>Üzenetsor létrehozása
A **ServiceBusService** objektum lehetővé teszi, hogy az üzenetsorok. Adja hozzá a következő kódot bármely Python-fájlt, amelyben a kívánt programozott módon érheti el a Service Bus tetején:

```python
from azure.servicebus import ServiceBusClient
```

Az alábbi kód létrehoz egy **ServiceBusClient** objektum. Cserélje le `mynamespace`, `sharedaccesskeyname`, és `sharedaccesskey` a névtér, közös hozzáférésű jogosultságkód (SAS-) kulcs nevét és értékét.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Tekintheti meg az értékeket a SAS-kulcs nevét és értékét az [az Azure portal] [ Azure portal] kapcsolati információkat, vagy a Visual Studio **tulajdonságok** panelen, amikor a szolgáltatás kiválasztása A Server Explorer (ahogyan az előző szakaszban látható) Bus-névteret.

```python
sb_client.create_queue("taskqueue")
```

A `create_queue` módszer is támogatja a további lehetőségeket, amelyek segítségével felülírhatja az alapértelmezett várólista beállításait, például az élettartam (TTL) vagy a várólista maximális hossza üzenet ideje engedélyezése. Az alábbi példa a várólista maximális mérete 5 GB-os, és az élettartam értéke 1 percre állítja be:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

sb_client.create_queue("taskqueue", queue_options)
```

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzenet küldése a Service Bus-üzenetsorba, az alkalmazás meghívja a `send_queue_message` metódust a **ServiceBusService** objektum.

Az alábbi példa bemutatja, hogyan tesztüzenet küldése az üzenetsorba nevű `taskqueue` használatával `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(Message("Message"))
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Kvóták kapcsolatos további információkért lásd: [Service Bus-kvóták][Service Bus quotas].

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Egy üzenetsor használatával fogadása a `receive_queue_message` metódust a **ServiceBusService** objektum:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(Message("Message"))
```

További információkért lásd: [Azure Service Bus Python-dokumentáció](/python/api/overview/azure/servicebus?view=azure-python).


Üzenetek törlése az üzenetsorból, mikor olvasott a paraméter `peek_lock` értékre van állítva **hamis**. Olvashat (betekintési) és az üzenet zárolása törlése az üzenetsorból paraméterének beállításával nélkül `peek_lock` való **igaz**.

Olvasása és törlése a fogadás művelet részeként viselkedését a legegyszerűbb modell, és a leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

Ha a `peek_lock` paraméter értéke **igaz**, a receive két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), a **törlése** metódust a **üzenet** az objektum. A **törlése** módszert fog jelölje meg az üzenetet, és távolítsa el az üzenetsorból.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az **feloldásához** metódust a **üzenet** objektum. Ennek hatására a Service Bus feloldja az az üzenetsorban lévő üzenet zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását lesz, és adja meg elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a **törlése** módszert hívja meg, akkor az üzenet újból kézbesítve lesz az alkalmazás amikor újraindul. Ezt gyakran nevezik **feldolgozása során legalább egyszer**, vagyis minden üzenetet legalább egyszer dolgozza, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet **MessageId** tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, tekintse meg ezeket a további cikkeket.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

