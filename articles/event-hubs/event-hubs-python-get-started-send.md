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
ms.custom: tracking-python
ms.openlocfilehash: 96c95efdc8f2154e0586fec59d1af66496acb101
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558968"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Gyors útmutató: események küldése és fogadása Event Hubs a Python használatával (Azure-eventhub 1-es verzió)
Ez a rövid útmutató bemutatja, hogyan lehet eseményeket küldeni és fogadni az Event hub eseményeiről az **Azure-eventhub 1. verziójú Python-** csomag használatával. 

> [!WARNING]
> Ez a rövid útmutató a régi Azure-eventhub 1-es verziójú csomagot használja. A csomag legújabb **5. verzióját** használó gyors útmutató: [események küldése és fogadása az Azure-eventhub 5-ös verziójának használatával](get-started-python-send-v2.md). Ha át szeretné helyezni az alkalmazást a régi csomag használatával egy újat, tekintse meg az [Azure-eventhub 1. verzióról az 5-](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)es verzióra történő Migrálás útmutatóját.
 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubs-t, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- Python 3,4 vagy újabb verzió, `pip` telepítve és frissítve.
- A Event Hubs Python-csomagja. A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amely a Python elérési útjában van: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be az Event hub elérési kulcsának értékét a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutatóban később írt kód elérési kulcsát kell használnia. Az alapértelmezett kulcs neve: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Események küldése

Eseményeket küldő Python-alkalmazás létrehozása az Event hub-ba:

> [!NOTE]
> A gyors üzembe helyezés helyett a GitHubról töltheti le és futtathatja a [minta alkalmazásokat](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) . Cserélje le a `EventHubConnectionString` és a `EventHubName` karakterláncokat az Event hub értékeire.

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy új, *Send.py*nevű fájlt. Ez a szkript 100 eseményt küld az Event hub-nak.
3. Illessze be a következő kódot a *Send.py*-be, \<namespace> és cserélje le a Event Hubs, \<eventhub> , \<AccessKeyName> és értékeket az \<primary key value> értékekre: 
   
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
2. Illessze be a következő kódot a *recv.py*-be, \<namespace> és cserélje le a Event Hubs, \<eventhub> , \<AccessKeyName> és értékeket az \<primary key value> értékekre: 
   
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

