---
title: Üzenetek küldése és fogadása az Azure Event Hubsból a Python használatával |} A Microsoft Docs
description: Ismerje meg, hogyan küldhet eseményeket származó eseményeket fogadni, és a Python használatával az Event Hubs keresztüli adatfolyamküldés események rögzítése.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 9c9c1bd909222ebb3fed57fb31c4f19404382cd4
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730196"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Python-alkalmazás az Azure Event Hubs használatával
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. További információkért lásd: [az Event Hubs bemutatása](event-hubs-what-is-event-hubs.md). 

Ez a cikk a következő feladatokat végezheti el a nyelven írt alkalmazások ismertető cikkekre mutató hivatkozások **Python**:

- [Események küldése eseményközpontba](#send-events-to-event-hubs)
- [Események fogadása az eseményközpontba](#receive-events-from-event-hubs)
- [Olvassa el a rögzített esemény adatok egy Azure storage-ból](#capture-event-hubs-data). 

## <a name="prerequisites"></a>Előfeltételek
- Létrehoz egy eseményközpontot, az alábbi gyorsútmutatókkal: [az Azure portal](event-hubs-create.md), [Azure CLI-vel](event-hubs-quickstart-cli.md), [Azure PowerShell-lel](event-hubs-quickstart-powershell.md), [AzureResourceManager-sablon](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4-es vagy újabb verzió telepítése a számítógépre.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Event Hubs a Python-csomag telepítéséhez nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Események küldése az Event Hubsba
A következő kód bemutatja, hogyan küldhet eseményeket egy eseményközpontba, Python-alkalmazás: 

1. Hozzon létre változókat, amely tárolja az URL-címét az event hub, a kulcs nevét és a kulcs értékét. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Hozzon létre egy Event Hubs-ügyfél, adjon hozzá egy küldő, futtassa az ügyfelet, a küldő használatával esemény küldése és majd állítsa le az ügyfél, amikor elkészült. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Hogyan küldhet eseményeket egy eseményközpontba való egy pythonban írt alkalmazás teljes oktatóanyagot, tekintse meg a [Ez a cikk](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Események fogadása az Event Hubs
A következő kód bemutatja, hogyan események fogadása az event hubs Python-alkalmazás: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Események fogadása az eseményközpontok a pythonban írt alkalmazások teljes oktatóanyagot, tekintse meg a [Ez a cikk](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Olvasási rögzítési eseményadatokat az Azure Storage-ból
A következő kód bemutatja, hogyan olvassa el a rögzített események a tárolt adatok egy **az Azure blob storage-bA** Python-alkalmazás: engedélyezése **rögzítése** található utasításokat követve, az event hubs szolgáltatást: [Engedélyezze az Event Hubs Capture az Azure portal használatával](event-hubs-capture-enable-through-portal.md). Ezt követően bizonyos események küldése az event hubs a kódot tesztelés előtt. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

Részletes útmutató egy Azure blob Storage rögzített az Event Hubs-adatok beolvasása egy pythonban írt alkalmazás, lásd: [Ez a cikk](event-hubs-capture-python.md)

## <a name="github-samples"></a>GitHub-minták
További Python-minták az annak a [Git-tárházat az azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>További lépések
Olvassa végig az alapelvek szakasz kezdve a cikkek [Event Hubs-szolgáltatások – áttekintés](event-hubs-features.md).