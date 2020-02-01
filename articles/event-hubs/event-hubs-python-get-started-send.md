---
title: Események küldése vagy fogadása az Azure Event Hubs a Python (régi) használatával
description: Ez az útmutató bemutatja, hogyan hozhat létre és futtathat olyan Python-parancsfájlokat, amelyek események küldését és fogadását küldi el az Azure Event Hubs a régi Azure-eventhub 1-es verziójú csomag használatával.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902938"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Gyors útmutató: események küldése és fogadása Event Hubs a Python használatával (Azure-eventhub 1-es verzió)

Az Azure Event Hubs egy Big streaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event Hubs az elosztott szoftverekről és eszközökről származó eseményeket, adatok vagy telemetria feldolgozását és tárolását is elvégezheti. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. További információ a Event Hubsről: [azure Event Hubs](event-hubs-about.md) és [szolgáltatások és terminológia az azure-ban Event Hubs](event-hubs-features.md).

Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan Python-alkalmazásokat, amelyek események küldésére és fogadására szolgáló eseményeket küldenek az Event hub-ból. 

> [!WARNING]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 1. verziójához készült. Javasoljuk, hogy a kódot [telepítse át](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) [a Python SDK 5-ös verziójára](get-started-python-send-v2.md).

 
 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Egy aktív Event Hubs névtér és az Event hub, amelyet a gyors üzembe helyezési útmutatóban talál [: hozzon létre egy Event hub](event-hubs-create.md)-t a Azure Portal használatával. Jegyezze fel a névtér és az Event hub azon neveit, amelyeket később szeretne használni ebben az útmutatóban. 
- A Event Hubs névtér megosztott elérési kulcsának és elsődleges kulcsának értéke. Szerezze be a hozzáférési kulcs nevét és értékét a [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakör útmutatását követve. Az alapértelmezett hozzáférési kulcs neve **RootManageSharedAccessKey**. Másolja a kulcs nevét és az elsődleges kulcs értékét, hogy az a bemutató későbbi részében legyen használatban. 
- Python 3,4 vagy újabb, `pip` telepítve és frissítve.
- A Event Hubs Python-csomagja. A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amely a Python elérési útjában van: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>Események küldése

Eseményeket küldő Python-alkalmazás létrehozása az Event hub-ba:

> [!NOTE]
> A gyors üzembe helyezés helyett a GitHubról töltheti le és futtathatja a [minta alkalmazásokat](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) . Cserélje le a `EventHubConnectionString` és `EventHubName` sztringeket az Event hub értékeire.

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy új, *Send.py*nevű fájlt. Ez a szkript 100 eseményt küld az Event hub-nak.
3. Illessze be a következő kódot a *Send.py*-be, és cserélje le a Event Hubs \<névtér >, \<eventhub >, \<AccessKeyName >, és \<elsődleges kulcs értéke > értékeit: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
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
               message = "Message {}".format(i)
               sender.send(EventData(message))
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
   
4. Mentse a fájlt. 

A parancsfájl futtatásához a *Send.py*mentett könyvtárból futtassa a következő parancsot:

```cmd
start python send.py
```

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

Eseményeket fogadó Python-alkalmazás létrehozása az Event hub-ból:

1. A Python-szerkesztőben hozzon létre egy *recv.py*nevű fájlt.
2. Illessze be a következő kódot a *recv.py*-be, és cserélje le a Event Hubs \<névtér >, \<eventhub >, \<AccessKeyName >, és \<elsődleges kulcs értéke > értékeit: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
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
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
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
   
4. Mentse a fájlt.

A parancsfájl futtatásához a *recv.py*mentett könyvtárból futtassa a következő parancsot:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Következő lépések
A Event Hubsról a következő cikkekben talál további információt:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

