---
title: Események küldése vagy fogadása az Azure Event Hubs a Python használatával (legújabb)
description: Ez a cikk egy olyan Python-alkalmazás létrehozásához nyújt útmutatást, amely az Azure-Event Hubs az Azure-eventhub 5. verziójának legújabb csomagjának használatával küld/fogad eseményeket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906359"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Események küldése vagy fogadása az Event hubokból a Python használatával (Azure-eventhub 5. verzió)

Az Azure Event Hubs egy olyan big data streaming platform és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event hubok feldolgozhatják és tárolhatják az elosztott szoftverekkel és eszközökkel előállított eseményeket, adatfájlokat vagy telemetria. Az Event hub-nak elküldett adatai bármilyen valós idejű elemzési szolgáltató vagy batch-vagy Storage-adapter használatával átalakíthatók és tárolhatók. További információ: [Event Hubs áttekintése](event-hubs-about.md) és [Event Hubs szolgáltatások](event-hubs-features.md).

Ez a rövid útmutató azt ismerteti, hogyan lehet olyan Python-alkalmazásokat létrehozni, amelyek események küldését és fogadását is elküldhetik az Event hub-ból.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 5. verzióját használja. A Python SDK 1. verzióját használó gyors üzembe helyezést [ebben a cikkben](event-hubs-python-get-started-send.md)találja. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Aktív Event Hubs névtér és Event hub. A létrehozásához kövesse a rövid útmutató [: Event hub létrehozása a Azure Portal használatával](event-hubs-create.md)című témakör utasításait. Jegyezze fel a névtér és az Event hub nevét, hogy a rövid útmutató későbbi részében használni lehessen.
- A Event Hubs névtér megosztott elérési kulcsának és elsődleges kulcsának értéke. A hozzáférési kulcs nevének és értékének beszerzéséhez kövesse az [Event hub kapcsolati karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakör útmutatását. Az alapértelmezett hozzáférési kulcs neve *RootManageSharedAccessKey*. Jegyezze fel a kulcs nevét és az elsődleges kulcs értékét, hogy a rövid útmutatóban később használhassa.
- Python 2,7 vagy 3,5 vagy újabb, a PIP telepítve és frissítve.
- A Event Hubs Python-csomagja. 

    A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amely a Python elérési útjában van:

    ```cmd
    pip install azure-eventhub
    ```

    Telepítse az alábbi csomagot az események fogadásához az Azure Blob Storage-ban ellenőrzőpont-tárolóként:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Események küldése
Ebben a szakaszban egy Python-szkriptet hoz létre, amely a korábban létrehozott Event hubhoz küld eseményeket.

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/)-ot.
2. Hozzon létre egy *Send.py*nevű szkriptet. Ez a parancsfájl a korábban létrehozott Event hubhoz küldi az események kötegét.
3. Illessze be a következő kódot a *Send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > A teljes forráskódhoz, beleértve a tájékoztató megjegyzéseket is, lépjen a [GitHub send_async... lapra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Események fogadása
Ez a rövid útmutató az Azure Blob Storage-t használja ellenőrzőpont-tárolóként. Az ellenőrzőpont-tároló az ellenőrzőpontok (azaz az utolsó olvasási pozíciók) megőrzésére szolgál.  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage-fiók és blob-tároló létrehozása
Hozzon létre egy Azure Storage-fiókot és egy BLOB-tárolót a következő lépésekkel:

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB-tároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

A fogadási kódban jegyezze fel a kapcsolódási karakterláncot és a tároló nevét.


### <a name="create-a-python-script-to-receive-events"></a>Python-szkript létrehozása események fogadásához

Ebben a szakaszban egy Python-szkriptet hoz létre az Event hub eseményeinek fogadásához:

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com/)-ot.
2. Hozzon létre egy *recv.py*nevű szkriptet.
3. Illessze be a következő kódot a *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > A teljes forráskódhoz, beleértve a további tájékoztató megjegyzéseket is, ugorjon a [GitHub recv_with_checkpoint_store_async... oldalára](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


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

A fogadó ablakban az Event hub számára küldött üzeneteket kell megjeleníteni.


## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban aszinkron módon küldött és fogadott eseményeket. Ha szeretné megtudni, hogyan küldhet és fogadhat eseményeket szinkronban, lépjen a [GitHub sync_samples oldalra](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

A GitHubon található összes minta (szinkron és aszinkron) esetében az [Azure Event Hubs Python-mintákhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)című részében találhat.
