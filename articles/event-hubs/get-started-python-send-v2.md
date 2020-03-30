---
title: Események küldése vagy fogadása az Azure Event Hubs-ból python használatával (legújabb)
description: Ez a cikk egy forgatókönyvet biztosít egy Python-alkalmazás létrehozásához, amely eseményeket küld/fogad az Azure Event Hubs-ba a legújabb azure-eventhub 5-ös verziójával.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 7c971dcac702318d15a27736828092e987468ca3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162973"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Események küldése eseményközpontokba vagy események fogadása a Python használatával (az azure-eventhub 5-ös verziójával)
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontból az **azure-eventhub 5 Python-csomag** használatával.

> [!IMPORTANT]
> Ez a rövid útmutató a legújabb azure-eventhub 5-ös verziójú csomagot használja. A régi azure-eventhub 1-es verziójú csomagot használó gyorskezdésért olvassa el az [Események küldése és fogadása az azure-eventhub 1-es verziójával című témakört.](event-hubs-python-get-started-send.md) 

## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- Python 2.7 vagy 3.5 vagy újabb, a PIP telepítve és frissítve.
- Az Event Hubs Python-csomagja. 

    A csomag telepítéséhez futtassa ezt a parancsot egy olyan parancssorban, amelynek útjában a Python található:

    ```cmd
    pip install azure-eventhub
    ```

    Telepítse a következő csomagot az események fogadásához az Azure Blob storage használatával ellenőrzőpont-tárolóként:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az **Event Hubs névtér kapcsolati karakterláncát a** cikk utasításainak követésével kapja [meg: Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A kapcsolati karakterláncot később használja ebben a rövid útmutatóban.

## <a name="send-events"></a>Események küldése
Ebben a szakaszban hozzon létre egy Python-parancsfájlt, amely eseményeket küld a korábban létrehozott eseményközpontnak.

1. Nyissa meg kedvenc Python-szerkesztőjét, például [a Visual Studio-kódot.](https://code.visualstudio.com/)
2. *Hozzon*létre egy send.py nevű parancsfájlt. Ez a parancsfájl egy eseményköteget küld a korábban létrehozott eseményközpontnak.
3. Illessze be a következő kódot *send.py:*

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
    > A teljes forráskódot, beleértve a tájékoztató megjegyzéseket, látogasson el a [GitHub send_async.py lapra.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Események fogadása
Ez a rövid útmutató az Azure Blob storage-ot használja ellenőrzőpont-tárolóként. Az ellenőrzőpont-tároló ellenőrzőpontok (azaz az utolsó olvasási pozíciók) megőrzésére szolgál.  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure-tárfiók és blobtároló létrehozása
Hozzon létre egy Azure-tárfiókot és egy blobtárolót az alábbi lépésekkel:

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A kapcsolati karakterlánc beszereznie a tárfiókhoz](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Ügyeljen arra, hogy rögzítse a kapcsolati karakterláncot és a tároló nevét későbbi használatra a fogadási kódban.


### <a name="create-a-python-script-to-receive-events"></a>Python-parancsfájl létrehozása események fogadásához

Ebben a szakaszban egy Python-parancsfájlt hoz létre az események fogadásához az eseményközpontból:

1. Nyissa meg kedvenc Python-szerkesztőjét, például [a Visual Studio-kódot.](https://code.visualstudio.com/)
2. *Hozzon*létre egy recv.py nevű parancsfájlt.
3. Illessze be a következő kódot *recv.py:*

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
    > A teljes forráskódért, beleértve a további tájékoztató megjegyzéseket, keresse fel a [GitHub recv_with_checkpoint_store_async.py lapot.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


### <a name="run-the-receiver-app"></a>A vevőalkalmazás futtatása

A parancsfájl futtatásához nyisson meg egy parancssort, amelynek a Python az útjában van, majd futtassa ezt a parancsot:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>A feladó alkalmazás futtatása

A parancsfájl futtatásához nyisson meg egy parancssort, amelynek a Python az útjában van, majd futtassa ezt a parancsot:

```bash
python send.py
```

A fogadó ablakban meg kell jeleníteniaz üzeneteket, amelyeket az eseményközpontba küldött.


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban aszinkron módon küldött és fogadott eseményeket. Az események szinkron küldéséről és fogadásáról a [GitHub sync_samples lapra.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)

A GitHubon az összes minta (szinkron és aszinkron) esetében keresse fel az [Azure Event Hubs ügyfélkódtárját a Python-mintákért.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
