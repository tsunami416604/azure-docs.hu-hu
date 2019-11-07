---
title: 'Gyors útmutató: események küldése és fogadása a Python használatával – Azure Event Hubs'
description: 'Gyors útmutató: Ez a bemutató bemutatja, hogyan hozhat létre és futtathat olyan Python-parancsfájlokat, amelyek események küldését és fogadását küldi el az Azure Event Hubsból.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 9b6c3fb03f696f4142721284a14001eb51153a77
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720553"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Gyors útmutató: események küldése és fogadása Event Hubs a Python használatával

Az Azure Event Hubs egy Big streaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event Hubs az elosztott szoftverekről és eszközökről származó eseményeket, adatok vagy telemetria feldolgozását és tárolását is elvégezheti. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. További információ a Event Hubsről: [azure Event Hubs](event-hubs-about.md) és [szolgáltatások és terminológia az azure-ban Event Hubs](event-hubs-features.md).

Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan Python-alkalmazásokat, amelyek események küldésére és fogadására szolgáló eseményeket küldenek az Event hub-ból. 

> [!NOTE]
> A gyors üzembe helyezés helyett a GitHubról töltheti le és futtathatja a [minta alkalmazásokat](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) . Cserélje le a `EventHubConnectionString` és `EventHubName` sztringeket az Event hub értékeire. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Egy aktív Event Hubs névtér és az Event hub, amelyet a gyors üzembe helyezési útmutatóban talál [: hozzon létre egy Event hub](event-hubs-create.md)-t a Azure Portal használatával. Jegyezze fel a névtér és az Event hub azon neveit, amelyeket később szeretne használni ebben az útmutatóban. 
- A Event Hubs névtér megosztott elérési kulcsának és elsődleges kulcsának értéke. Szerezze be a hozzáférési kulcs nevét és értékét a [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakör útmutatását követve. Az alapértelmezett hozzáférési kulcs neve **RootManageSharedAccessKey**. Másolja a kulcs nevét és az elsődleges kulcs értékét, hogy az a bemutató későbbi részében legyen használatban. 
- Python 3,4 vagy újabb, `pip` telepítve és frissítve.
- A Event Hubs Python-csomagja. A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amely a Python elérési útjában van: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Az ebben a rövid útmutatóban szereplő kód a Event Hubs SDK aktuális stabil verzióját használja. Az SDK előzetes verzióját használó mintakód: [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Események küldése

Eseményeket küldő Python-alkalmazás létrehozása az Event hub-ba:

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

## <a name="next-steps"></a>További lépések
A Event Hubsról a következő cikkekben talál további információt:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

