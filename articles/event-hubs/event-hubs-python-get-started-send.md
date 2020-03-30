---
title: Események küldése vagy fogadása az Azure Event Hubs-ból python használatával (régi)
description: Ez a forgatókönyv bemutatja, hogyan hozhat létre és futtathat Python-parancsfájlokat, amelyek eseményeket küldenek az Azure Event Hubs-hoz, vagy eseményeket fogadnak a régi azure-eventhub 1-es verziójával.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162599"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Rövid útmutató: Események küldése és fogadása az Event Hubs használatával Python (azure-eventhub 1-es verzió)
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontból az **azure-eventhub 1 Python-csomag** használatával. 

> [!WARNING]
> Ez a rövid útmutató a régi azure-eventhub 1-es verziójú csomagot használja. A csomag legújabb **5-ös verzióját** használó rövid útmutatóért [lásd: Események küldése és fogadása az azure-eventhub 5-ös verziójával.](get-started-python-send-v2.md) Ha az alkalmazást a régi csomagról az újra szeretné áthelyezni, olvassa el az útmutatót az [azure-eventhub 1-es verziójáról az 5-ös verzióra való áttéréshez.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
 

## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- Python 3.4-es `pip` vagy újabb, telepített és frissített.
- Az Event Hubs Python-csomagja. A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amelynek útjában a Python található: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az eseményközpont hozzáférési kulcsának értékét a cikk utasításainak követésével kapja meg: [Kapcsolati karakterlánc beírása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató ban a később írt kód hozzáférési kulcsát használhatja. Az alapértelmezett kulcsnév: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Események küldése

Python-alkalmazás létrehozása, amely eseményeket küld egy eseményközpontba:

> [!NOTE]
> Ahelyett, hogy a rövid útmutató, letöltheti és futtathatja a [minta alkalmazásokat](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) a GitHubról. Cserélje `EventHubConnectionString` le `EventHubName` a karakterláncokat az eseményközpont értékeire.

1. A kedvenc Python-szerkesztő, például a [Visual Studio-kód megnyitása](https://code.visualstudio.com/)
2. Hozzon létre egy *új fájlt, amelynek*neve send.py. Ez a parancsfájl 100 eseményt küld az eseményközpontba.
3. Illessze be *send.py*a következő kódot a \<send.py , \<cserélje le az Event Hubs névteret>, az eventhub>, \<az AccessKeyName> és \<az elsődleges kulcs érték> az értékekre: 
   
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

A parancsfájl futtatásához a *send.py*könyvtárból futtassa a következő parancsot:

```cmd
start python send.py
```

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

Python-alkalmazás létrehozása, amely eseményeket fogad egy eseményközpontból:

1. A Python-szerkesztőben hozzon létre egy *recv.py*nevű fájlt.
2. Illessze be a következő kódot *a recv.py,* \<cserélje le \<az Event Hubs \< \<névteret>, az eventhub>, az AccessKeyName> és az elsődleges kulcs érték> az értékekre: 
   
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

A parancsfájl futtatásához a *recv.py*könyvtárból futtassa a következő parancsot:

```cmd
start python recv.py
```

## <a name="next-steps"></a>További lépések
Az Event Hubs ról további információt az alábbi cikkekben talál:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

