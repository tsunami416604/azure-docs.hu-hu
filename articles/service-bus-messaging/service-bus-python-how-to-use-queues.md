---
title: 'Rövid útmutató: Az Azure Service Bus-várólisták használata pythonnal'
description: Ez a cikk bemutatja, hogyan hozhat létre, küldhet üzeneteket az Azure Service Bus-várólistákból a Python használatával, és hogyan fogadhat üzeneteket.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: aa9ffc48d9b1374fa510f450bab2c66641421446
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76773495"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Rövid útmutató: Az Azure Service Bus-várólisták használata pythonnal

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk bemutatja, hogyan hozhat létre, küldhet üzeneteket az Azure Service Bus-várólistákból a Python használatával, és hogyan fogadhat üzeneteket. 

A Python Azure Service Bus-kódtárakról további információt a [Service Bus-kódtárak pythonhoz](/python/api/overview/azure/servicebus?view=azure-python)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja visual [studio- vagy MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- A Service Bus névtér, a rövid útmutató lépései alapján [létrehozott: Az Azure Portal használatával hozzon létre egy Service Bus-témakört és előfizetéseket.](service-bus-quickstart-topics-subscriptions-portal.md) Másolja az elsődleges kapcsolati karakterláncot a **Megosztott hozzáférési házirendek** képernyőre a cikk későbbi részében. 
- Python 3.4x vagy újabb, a [Python Azure Service Bus][Python Azure Service Bus package] csomag telepítve. További információt a [Python telepítési útmutatójában talál.](/azure/python/python-sdk-azure-install) 

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A **ServiceBusClient** objektum lehetővé teszi a várólisták kal való munkát. A Service Bus programozott eléréséhez adja hozzá a következő sort a Python-fájl tetejéhez:

```python
from azure.servicebus import ServiceBusClient
```

Adja hozzá a következő kódot egy **ServiceBusClient** objektum létrehozásához. Cserélje `<connectionstring>` le a Service Bus elsődleges kapcsolati karakterláncának értékét. Ezt az értéket a Service Bus-névtér **megosztott hozzáférésű szabályzatai** az [Azure Portalon][Azure portal]található megosztott hozzáférési szabályzatok alatt találja.

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

A következő kód `create_queue` a **ServiceBusClient** metódusát használja `taskqueue` az alapértelmezett beállításokkal elnevezett várólista létrehozásához:

```python
sb_client.create_queue("taskqueue")
```

A beállításokkal felülbírálhatja az alapértelmezett várólista-beállításokat, például az üzenet idő az élő (TTL) vagy a maximális témakörméret. A következő kód létrehoz `taskqueue` egy várólistát, amelynek maximális várólistamérete 5 GB, a TTL értéke pedig 1 perc:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba

Ha üzenetet szeretne küldeni egy Service Bus-várólistába, egy alkalmazás meghívja a `send` metódust a **ServiceBusClient** objektumon. A következő kódpélda létrehoz egy várólista-ügyfelet, és tesztüzenetet küld a `taskqueue` várólistának. Cserélje `<connectionstring>` le a Service Bus elsődleges kapcsolati karakterláncának értékét. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Az üzenetek méretkorlátai és kvótái

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Nincs korlátozva, hogy egy várólista hány üzenetet tarthat meg, de a várólista által tartott üzenetek teljes méretére vonatkozó korlát van. A várólista méretét a létrehozás időpontjában, 5 GB-os felső korláttal határozhatja meg. 

A kvótákról további információt a [Service Bus-kvóták című témakörben talál.][Service Bus quotas]

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából

A várólista-ügyfél üzeneteket fogad `get_receiver` egy várólistából a **ServiceBusClient** objektum metódusával. A következő kódpélda létrehoz egy várólista-ügyfelet, és üzenetet fogad a `taskqueue` várólistából. Cserélje `<connectionstring>` le a Service Bus elsődleges kapcsolati karakterláncának értékét. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>A peek_lock paraméter használata

A `peek_lock` választható `get_receiver` paraméter határozza meg, hogy a Service Bus törli-e az üzeneteket a várólistából olvasáskor. Az üzenetek fogadásának alapértelmezett módja `peek_lock` a *PeekLock*, vagy **igaz**értékre, amely beolvassa (betekint) és zárolja az üzeneteket anélkül, hogy kiolvasná őket a várólistából. Ezután minden üzenetet explicit módon ki kell tölteni, hogy eltávolítsuk a várólistából.

Ha olvasás közben törölni szeretné az üzeneteket a `peek_lock` várólistából, beállíthatja `get_receiver` a **Hamis**paraméter t. Az üzenetek törlése a fogadási művelet részeként a legegyszerűbb modell, de csak akkor működik, ha az alkalmazás eltudja viselni a hiányzó üzeneteket, ha hiba van. A viselkedés megértéséhez fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó fogadási kérelmet ad ki, majd a feldolgozás előtt összeomlik. Ha az üzenetet a fogadáskor törölték, amikor az alkalmazás újraindul, és újra elkezdi az üzenetek et, az összeomlás előtt fogadott üzenetet kihagyta.

Ha az alkalmazás nem tudja elviselni a nem fogadott üzeneteket, a fogadás kétlépcsős művelet. PeekLock megkeresi a következő üzenetet kell fogyasztani, zárolja, hogy megakadályozzák más fogyasztók fogadására, és visszaküldi azt az alkalmazásnak. Az üzenet feldolgozása vagy tárolása után az alkalmazás befejezi a fogadási `complete` folyamat második szakaszát az **Üzenet** objektum metódusának hívásával.  A `complete` metódus az üzenetet felhasználásként jelöli meg, és eltávolítja a várólistából.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Alkalmazásösszeomlások és olvashatatlan üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, meghívhatja a `unlock` metódust az **Üzenet** objektumon. A Service Bus feloldja az üzenet zárolását a várólistán belül, és elérhetővé teszi, hogy újra megkapja, akár ugyanaz, akár egy másik fogyasztó alkalmazás.

A várólistán belül zárolt üzenetek időtúltöltése is van. Ha egy alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúldátum lejárta előtt, például ha az alkalmazás összeomlik, a Service Bus automatikusan feloldja az üzenetet, és elérhetővé teszi, hogy újra megkapja.

Ha egy alkalmazás összeomlik egy üzenet `complete` feldolgozása után, de a metódus hívása előtt, az üzenet újraindításakor újra kézbesíti az alkalmazást. Ezt a viselkedést gyakran *nevezik Legalább egyszeri feldolgozásnak.* Minden üzenet feldolgozása legalább egyszer történik, de bizonyos helyzetekben ugyanaz tetsző üzenet kézbesíthető. Ha a forgatókönyv nem tolerálja a duplikált feldolgozást, használhatja az üzenet **MessageId** tulajdonságát, amely a kézbesítési kísérletek során állandó marad, az ismétlődő üzenetek kézbesítésének kezeléséhez. 

> [!TIP]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi, hogy csatlakozzon egy Service Bus-névtérhez, és egyszerűen felügyelje az üzenetkezelési entitásokat. Az eszköz speciális funkciókat, például importálási/exportálási funkciókat, valamint témakörök, várólisták, előfizetések, továbbítási szolgáltatások, értesítési központok és eseményközpontok tesztelését teszi lehetővé.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a Service Bus-várólisták alapjait, további információért tekintse meg [a Várólisták, témakörök és előfizetések című témakört.][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
