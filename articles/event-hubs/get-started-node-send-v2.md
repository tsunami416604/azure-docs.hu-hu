---
title: Események küldése és fogadása a Node. js használatával – Azure Event Hubs
description: Ez a cikk bemutatja, hogyan hozhat létre egy Node. js-alkalmazást, amely az Azure Event Hubsról küld eseményeket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: 9ea6febc781422a72ac6547338c8b21239331083
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942515"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Események küldése vagy fogadása az Azure Event Hubs a Node. js használatával 

Az Azure Event Hubs egy Big streaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok és telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan lehet Node. js-alkalmazásokat létrehozni események küldésére vagy események fogadására az Event hub-ból.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Java script SDK 5. verzióját használja. A Java script SDK régi 2. verzióját használó gyors üzembe helyezést [ebben a cikkben](event-hubs-node-get-started-send.md)találja. Ha az SDK 2-es verzióját használja, javasoljuk, hogy telepítse át a kódot a legújabb verzióra. Részletekért tekintse meg az [áttelepítési útmutatót](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- A Node. js 8. x vagy újabb verziója. Töltse le a legújabb LTS-verziót [https://nodejs.org ról ](https://nodejs.org).
- Visual Studio Code (ajánlott) vagy bármely más IDE
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást, majd folytassa az oktatóanyag következő lépéseivel. Ezután szerezze be a kapcsolati karakterláncot az Event hub-névtérhez a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében a kapcsolatok karakterláncát kell használnia.


### <a name="install-npm-packages"></a>NPM-csomagok telepítése
A [Event Hubs NPM-csomagjának](https://www.npmjs.com/package/@azure/event-hubs)telepítéséhez nyisson meg egy parancssort, amely az elérési útjában `npm` van, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```shell
npm install @azure/event-hubs
```

A fogadó oldalon két további csomagot kell telepítenie. Ebben a rövid útmutatóban az Azure Blob Storage fogja használni az ellenőrzőpontok megőrzéséhez, hogy a program ne olvassa be a már elolvasott eseményeket. Egy blobban rendszeres időközönként lefogadja a fogadott üzenetek metaadatait. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan később kilépett.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Események küldése

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Node. js-alkalmazást, amely eseményeket küld az Event hub-nak. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `send.js` nevű fájlt, és illessze be a következő kódot. 

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {
    
      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);
    
      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    
    
      // send the batch to the event hub
      await producer.sendBatch(batch);
    
      // close the producer client
      await producer.close();
    
      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ne felejtse el lecserélni a **kapcsolati sztringet** és az **Event hub-név** értékét a kódban. 
5. Futtassa a parancsot `node send.js` a fájl végrehajtásához. Ez a művelet három eseményből álló köteget küld az Event hub-nak
6. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg meg nem jelenik az üzenetek fogadása. 

    [![annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js) .

Gratulálunk! Már elvégezte az események elküldése az Event hubhoz. 


## <a name="receive-events"></a>Események fogadása 
Ez a szakasz bemutatja, hogyan fogadhat eseményeket egy Event hub-ból egy Node. js-alkalmazásban található Azure Blob ellenőrzőpont-tároló használatával. A beérkező üzenetekre vonatkozó metaadatokat a Azure Storage Blob rendszeres időközönként áthelyezi. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan később kilépett.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blob-tároló létrehozása
Az alábbi lépéseket követve hozzon létre egy BLOB-tárolót az Azure Storage-fiókban. 

1. [Azure Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [BLOB-tároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Jegyezze fel a kapcsolatok sztringjét és a tároló nevét. Ezeket a fogadási kódban fogja használni. 

### <a name="write-code-to-receive-events"></a>Kód írása az események fogadására

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `receive.js` nevű fájlt, és illessze be a következő kódot. Részletekért lásd a kód megjegyzéseit. 
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";
    
    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);
    
      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);
    
      // subscribe for the events and specify handlers for processing the events and errors. 
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint 
            await context.updateCheckpoint(events[events.length - 1]);
          },
    
          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );
        
      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Ne felejtse el megadni a **következő értékeket** a kódban: 
    - A Event Hubs névtérhez tartozó kapcsolódási karakterlánc
    - Az Event hub neve
    - A kapcsolódási karakterlánc az Azure Storage-fiókhoz
    - A blob-tároló neve
5. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node receive.js`. A fogadott eseményekről szóló üzeneteket az ablakban kell megtekinteni. 

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gratulálunk! Most már fogadott eseményeket az Event hub-ból. A fogadó program az Event hub alapértelmezett fogyasztói csoportjának összes partícióján fogad eseményeket.

## <a name="next-steps"></a>Következő lépések
Tekintse meg ezeket a mintákat a GitHubon:

- [JavaScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Írógéppel minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)