---
title: 'Gyors útmutató: Azure Service Bus várólisták használata a Python használatával'
description: Ez a cikk bemutatja, hogyan használható a Python a Azure Service Bus várólistákból érkező üzenetek létrehozására, üzenetek küldésére és fogadására.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 55ed71c6947c70ac797656f2f18cf71dd8aaae9d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852480"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Gyors útmutató: Azure Service Bus várólisták használata a Python használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk bemutatja, hogyan használható a Python a Azure Service Bus várólistákból érkező üzenetek létrehozására, üzenetek küldésére és fogadására. 

További információ a Python Azure Service Bus kódtárakkal kapcsolatban: [Service Bus kódtárak a Pythonhoz](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Egy Service Bus névtér, amely a gyors üzembe helyezés lépéseinek követésével jön létre [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket](service-bus-quickstart-topics-subscriptions-portal.md). Másolja az elsődleges kapcsolati karakterláncot a **közös hozzáférésű házirendek** képernyőről a cikk későbbi részében való használatra. 
- Python 3.4 x vagy újabb verzió, a [python Azure Service Bus][Python Azure Service Bus package] csomaggal. További információ: a [Python telepítési útmutatója](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A **ServiceBusClient** objektum lehetővé teszi a várólistákkal való munkavégzést. A Service Bus programozott eléréséhez adja hozzá a következő sort a Python-fájl elejéhez:

```python
from azure.servicebus import ServiceBusClient
```

Adja hozzá a következő kódot egy **ServiceBusClient** objektum létrehozásához. Cserélje le az `<connectionstring>` értéket a Service Bus elsődleges kapcsolattípus értékére. Ezt az értéket a [Azure Portal][Azure portal]Service Bus névterében található **megosztott hozzáférési házirendek** területen találja.

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

A következő kód a `create_queue` **ServiceBusClient** metódusát használja az `taskqueue` alapértelmezett beállításokkal rendelkező várólista létrehozásához:

```python
sb_client.create_queue("taskqueue")
```

A beállításokkal felülbírálhatja az alapértelmezett üzenetsor-beállításokat, például az üzenetek élettartamát (TTL) vagy a témakör maximális méretét. A következő kód egy nevű várólistát hoz létre, amely a `taskqueue` várólista maximális mérete 5 GB, a TTL értéke pedig 1 perc:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba

Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja a `send` metódust a **ServiceBusClient** objektumon. A következő kódrészlet egy üzenetsor-ügyfelet hoz létre, és Tesztüzenet küldése a `taskqueue` várólistára. Cserélje le az `<connectionstring>` értéket a Service Bus elsődleges kapcsolattípus értékére. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Az üzenet méretének korlátai és kvótái

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A várólista által megtartható üzenetek száma nincs korlátozva, de a várólista által tárolt üzenetek teljes méretére vonatkozó korlát szerepel. A várólista mérete a létrehozáskor határozható meg, amelynek felső korlátja 5 GB. 

További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

A várólista-ügyfél a `get_receiver` **ServiceBusClient** objektum metódusának használatával fogad üzeneteket egy várólistából. A következő kódrészlet létrehoz egy üzenetsor-ügyfelet, és üzenetet fogad a `taskqueue` várólistából. Cserélje le az `<connectionstring>` értéket a Service Bus elsődleges kapcsolattípus értékére. 

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

A választható `peek_lock` paraméter `get_receiver` azt határozza meg, hogy Service Bus törli-e az üzeneteket a várólistáról az olvasáskor. Az üzenetek fogadásának alapértelmezett módja a *PeekLock*, vagy `peek_lock` **igaz**értékre van állítva, amely beolvassa (betekintést) és zárolja az üzeneteket anélkül, hogy törölné őket a várólistából. Ezután minden üzenetnek explicit módon el kell végeznie, hogy eltávolítsa azt a várólistából.

Ha a várólistán lévő üzeneteket az olvasás során szeretné törölni, beállíthatja a `peek_lock` paramétert `get_receiver` **hamis**értékre. Ha a fogadási művelet részeként törli az üzeneteket, a legegyszerűbb modell, de csak akkor működik, ha az alkalmazás nem képes elviselni a hiányzó üzeneteket. Ennek a viselkedésnek a megismeréséhez Vegyünk egy olyan forgatókönyvet, amelyben a fogyasztó egy fogadási kérelmet bocsát ki, majd összeomlik a feldolgozás előtt. Ha a rendszer törölte az üzenetet a fogadáskor, amikor az alkalmazás újraindul, és megkezdi az üzenetek újrafelhasználását, az összeomlás előtt elmulasztotta a kapott üzenetet.

Ha az alkalmazás nem tudja elviselni a kihagyott üzeneteket, a fogadás egy kétfázisú művelet. A PeekLock megkeresi a használni kívánt következő üzenetet, zárolja, hogy megakadályozza más fogyasztók számára a fogadást, és visszaadja az alkalmazásnak. Az üzenet feldolgozása vagy tárolása után az alkalmazás végrehajtja a fogadási folyamat második szakaszát úgy, hogy meghívja a `complete` metódust az **üzenet** objektumon.  A `complete` metódus felhasználja az üzenetet, és eltávolítja azt a várólistából.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tud feldolgozni egy üzenetet, meghívja a `unlock` metódust az **üzenet** objektumon. Service Bus feloldja az üzenet zárolását a várólistán belül, és lehetővé teszi az újbóli fogadását, akár ugyanazon, akár egy másik alkalmazásban.

A várólistán lévő üzenetek esetében is időtúllépés van. Ha egy alkalmazás nem tud feldolgozni egy üzenetet a zárolási időtúllépés lejárta előtt, például ha az alkalmazás összeomlik, Service Bus feloldja az üzenet automatikus zárolását, és elérhetővé teszi azt újra.

Ha egy alkalmazás egy üzenet feldolgozása után összeomlik, de a metódus meghívása előtt `complete` , a rendszer az újraindításkor újra továbbítja az üzenetet az alkalmazásnak. Ezt a viselkedést gyakran egyszeri *feldolgozásnak*nevezik. Minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, használhatja az üzenet **MessageID** tulajdonságát, amely állandó marad a kézbesítési kísérletek között, hogy kezelni tudja a duplikált üzenetek kézbesítését. 

> [!TIP]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. Service Bus Explorer lehetővé teszi egy Service Bus névtérhez való kapcsolódást és az üzenetkezelési entitások egyszerű felügyeletét. Az eszköz olyan speciális funkciókat biztosít, mint például az importálási/exportálási funkciók, a témakörök, a várólisták, az előfizetések, a továbbító szolgáltatások, az értesítési központok és az Event hub-eszközök tesztelése.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte Service Bus várólisták alapjait, további információt a [várólisták, témakörök és előfizetések][Queues, topics, and subscriptions] című témakörben talál.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
