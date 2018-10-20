---
title: Események küldése az Azure Event Hubs Python használatával |} A Microsoft Docs
description: Ismerkedés az események küldése az Event Hubs Python használatával
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.openlocfilehash: bb77ed69ae8f2229cbd62afa545cac9f048689e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458003"
---
# <a name="send-events-to-event-hubs-using-python"></a>Események küldése az Event Hubs Python használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba való egy pythonban írt alkalmazás. 

> [!NOTE]
> Ebben a rövid útmutatóban egy mintát, letöltheti a [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), cserélje le `EventHubConnectionString` és `EventHubName` karakterláncot az eseményközpontja értékeire, és futtassa azt. A lépéseket követheti azt is megteheti, ebben az oktatóanyagban hozhat létre saját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Python 3.4-es vagy újabb.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Event Hubs a Python-csomag telepítéséhez nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Hozzon létre egy Python-szkriptet események küldésére

Ezután hozzon létre egy Python-alkalmazás, amely elküldi az eseményeket egy eseményközpontba:

1. Nyissa meg a Python kedvenc szerkesztőjében, például [Visual Studio Code][Visual Studio Code].
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

## <a name="run-application-to-send-events"></a>Események küldése az alkalmazás futtatása

A szkript futtatásához nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot:

```bash
start python send.py
```

Gratulálunk! Üzeneteket küldött egy eseményközpontba.
 
## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban az elküldött üzeneteket az eseményközpontba Python használatával. Események fogadása az eseményközpontok a Python használatával kapcsolatban lásd: [események fogadása az event hubs - Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
