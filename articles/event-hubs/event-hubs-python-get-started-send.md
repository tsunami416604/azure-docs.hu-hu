---
title: Események küldése vagy fogadása a Python használatával – Azure Event Hubs | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy olyan Python-alkalmazást, amely eseményeket küld az Azure Event Hubsnak.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 22726ed8acae69adc09389b8f5f28df594a570a3
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915431"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Események küldése vagy fogadása Event Hubsról a Python használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag leírja, hogyan hozhat létre olyan Python-alkalmazásokat, amelyek események küldésére és fogadására használhatók az Event hub-ból. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-python/tree/master/examples). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Python 3.4-es vagy újabb.
- A [Azure Portal](https://portal.azure.com) használatával hozzon létre Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md). Ezután szerezze be az Event hub elérési kulcsának értékét a cikk utasításait követve: A [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A hozzáférési kulcsot a kód írása az oktatóanyag későbbi részében fogja használni. Az alapértelmezett kulcs neve: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Event Hubs a Python-csomag telepítéséhez nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Események küldése

> [!NOTE]
> Az ebben a szakaszban szereplő kód a Event Hubs SDK jelenlegi stabil verziójára (1.3.1) érvényes. Ha az SDK előzetes verzióját használó mintakód keres, tekintse meg [ezt a lapot](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

### <a name="create-a-python-script-to-send-events"></a>Hozzon létre egy Python-szkriptet események küldésére

Ezután hozzon létre egy Python-alkalmazás, amely elküldi az eseményeket egy eseményközpontba:

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy parancsfájlt nevű **send.py**. Ez a szkript 100 eseményeket küld az event hub.
3. Send.py, és cserélje le a címet, a felhasználó és a kulcs értékeit az Azure Portalról az előző szakaszban beszerzett illessze be a következő kódot: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

### <a name="run-application-to-send-events"></a>Események küldése az alkalmazás futtatása

A szkript futtatásához nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot:

```bash
start python send.py
```

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

### <a name="create-a-python-script-to-receive-events"></a>Hozzon létre egy Python-szkriptet eseményeket szeretne fogadni.

Ezután hozzon létre egy Python-alkalmazás, amely fogadja az eseményeket egy adott eseményközpontból:

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy parancsfájlt nevű **recv.py**.
3. Recv.py, és cserélje le a címet, a felhasználó és a kulcs értékeit az Azure Portalról az előző szakaszban beszerzett illessze be a következő kódot: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(
        CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
        total += 1

    end_time = time.time()
    client.stop()
    run_time = end_time - start_time
    print("Received {} messages in {} seconds".format(total, run_time))

except KeyboardInterrupt:
    pass
finally:
    client.stop()
```

### <a name="receive-events"></a>Események fogadása

A szkript futtatásához nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

