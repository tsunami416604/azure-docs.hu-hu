---
title: Események küldése vagy fogadása az Azure Event Hubs a Python használatával (legújabb)
description: Ez a cikk egy olyan Python-alkalmazás létrehozásához nyújt útmutatást, amely az Azure-Event Hubs az Azure-eventhub 5. verziójának legújabb csomagjának használatával küld/fogad eseményeket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 6b16398c7c1fd53562df7e4ac8e801a8c97162f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159437"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Események küldése vagy fogadása az Event hubokból a Python használatával (Azure-eventhub 5. verzió)
Ez a rövid útmutató bemutatja, hogyan lehet eseményeket küldeni és fogadni az Event hub-ból az **Azure-eventhub 5. verziójú Python-** csomag használatával.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure-eventhub 5. verziójának legújabb csomagját használja. Az Azure-eventhub 1. verziójának első csomagját használó gyors üzembe helyezéssel kapcsolatban lásd: az [események küldése és fogadása az Azure-eventhub 1. verziójának használatával](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubsét, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
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
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

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

> [!NOTE]
> Ha Azure Stack hub-on fut, akkor a platform a Storage blob SDK egy másik verzióját is támogatja, mint az Azure-ban általában elérhető. Ha például [Azure stack Hub 2002-es verzióján](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)fut, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2017-11-09-es verzió. Ebben az esetben a szakasz következő lépésein kívül kódot is hozzá kell adnia a Storage szolgáltatás API 2017-11-09-es verziójának célzásához. Az adott tárolási API-verzió célzására vonatkozó példát a következő témakörben talál: [szinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) és [aszinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) minták a githubon. Az Azure Stack hub által támogatott Azure Storage szolgáltatás verziójával kapcsolatos további információkért tekintse meg [Azure stack hub Storage: különbségek és megfontolások](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)című témakört.


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage-fiók és blob-tároló létrehozása
Hozzon létre egy Azure Storage-fiókot és egy BLOB-tárolót a következő lépésekkel:

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md)

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


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban aszinkron módon küldött és fogadott eseményeket. Ha szeretné megtudni, hogyan küldhet és fogadhat eseményeket szinkronban, lépjen a [GitHub sync_samples oldalra](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

A GitHubon található összes minta (szinkron és aszinkron) esetében az [Azure Event Hubs Python-mintákhoz készült ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)című részében találhat.
