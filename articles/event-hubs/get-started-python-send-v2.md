---
title: Események küldése vagy fogadása a Python használatával – Azure Event Hubs | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy olyan Python-alkalmazást, amely eseményeket küld az Azure Event Hubsnak.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: a137f274744a6acec22d036f9f4c5c4a5174cc14
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942593"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Események küldése vagy fogadása Event Hubsról a Python használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok és telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag leírja, hogyan hozhat létre olyan Python-alkalmazásokat, amelyek események küldésére és fogadására használhatók az Event hub-ból. 

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 5. verzióját használja. A Python SDK régi 1. verzióját használó gyors üzembe helyezést [ebben a cikkben](event-hubs-python-get-started-send.md)találja. Ha az SDK 1. verzióját használja, javasoljuk, hogy telepítse át a kódot a legújabb verzióra. Részletekért tekintse meg az [áttelepítési útmutatót](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Egy aktív Event Hubs névtér és az Event hub, amelyet a gyors üzembe helyezési útmutatóban talál [: hozzon létre egy Event hub](event-hubs-create.md)-t a Azure Portal használatával. Jegyezze fel a névtér és az Event hub azon neveit, amelyeket később szeretne használni ebben az útmutatóban. 
- A Event Hubs névtér megosztott elérési kulcsának és elsődleges kulcsának értéke. Szerezze be a hozzáférési kulcs nevét és értékét a [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakör útmutatását követve. Az alapértelmezett hozzáférési kulcs neve **RootManageSharedAccessKey**. Másolja a kulcs nevét és az elsődleges kulcs értékét, hogy az a bemutató későbbi részében legyen használatban. 
- Python 2,7 és 3,5 vagy újabb, `pip` telepítve és frissítve.
- A Event Hubs Python-csomagja. A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amely a Python elérési útjában van: 
    
    ```cmd
    pip install azure-eventhub
    ```

    Telepítse ezt a csomagot az események fogadásához az Azure Blob Storage-ban ellenőrzőpont-tárolóként. 

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Események küldése
Ebben a szakaszban egy Python-szkriptet hoz létre, amely az eseményeket a korábban létrehozott Event hubhoz küldi. 

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy **Send.py**nevű szkriptet. Ez a parancsfájl egy köteget küld a korábban létrehozott Event hubhoz. 
3. Illessze be a következő kódot a send.py. A részletekért tekintse meg a kód megjegyzéseit. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData
    
    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and 
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()
    
            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))
    
            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())
    
    ```

    > [!NOTE]
    > A teljes forráskódot nagyon hasznos megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py) .

## <a name="receive-events"></a>Események fogadása
Ez a rövid útmutató egy Azure-Blob Storage mutat be ellenőrzőpont-tárolóként. Az ellenőrzőpont-tároló az ellenőrzőpontok megőrzésére szolgál (utolsó olvasási pozíció).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blob-tároló létrehozása
Az alábbi lépéseket követve hozzon létre egy BLOB-tárolót az Azure Storage-fiókban. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [BLOB-tároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Jegyezze fel a kapcsolatok sztringjét és a tároló nevét. Ezeket a fogadási kódban fogja használni. 


### <a name="create-python-script-to-receive-events"></a>Python-szkript létrehozása események fogadásához

Ebben a szakaszban egy Python-szkriptet hoz létre az Event hub eseményeinek fogadásához:

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/) -ot
2. Hozzon létre egy **recv.py**nevű szkriptet.
3. Illessze be a következő kódot a recv.py. A részletekért tekintse meg a kód megjegyzéseit. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore
    
    
    async def on_event(partition_context, event):
        # print the event data 
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))
    
        # update the checkpoint so that the program doesn't read the events 
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)
    
    async def main():
        # create an Azure blob checkpoint store to store the checkpoints 
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")
    
        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)
    
    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > A teljes forráskódot nagyon hasznos megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py) .


### <a name="run-the-receiver-app"></a>A fogadó alkalmazás futtatása

A parancsfájl futtatásához nyisson meg egy parancssort, amely a Python elérési útjában található, majd futtassa a következő parancsot:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>A küldő alkalmazás futtatása

A parancsfájl futtatásához nyisson meg egy parancssort, amely a Python elérési útjában található, majd futtassa a következő parancsot:

```bash
python send.py
```

Ekkor meg kell jelennie az Event hub számára a fogadó ablakban küldött üzeneteknek. 


## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban aszinkron módon küldi el és fogadja az eseményeket. Az események szinkron módon történő küldésével és fogadásával kapcsolatban tekintse meg az [ezen a helyen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)található mintákat.

[Itt](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)megtalálhatja az összes mintát (a szinkronizálást és az aszinkront is) a githubon. 
