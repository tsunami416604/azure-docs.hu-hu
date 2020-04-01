---
title: Események küldése vagy fogadása az Azure Event Hubs-tól JavaScript használatával (legújabb)
description: Ez a cikk egy forgatókönyvet biztosít egy JavaScript-alkalmazás létrehozásához, amely eseményeket küld/fogad az Azure Event Hubs-ba a legújabb azure/event-hubs 5-ös verziójú csomag használatával.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 063f2e1005db177420da7f81b799ab01fd212d7e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478476"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Események küldése eseményközpontokba vagy események fogadása az eseményközpontokból javaScript használatával (azazure/event-hubs 5-ös verzió)
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontba, és hogyan fogadhat eseményeket az **azure/event-hubs 5-ös verziójú JavaScript-csomag** használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató a legújabb azure/event-hubs 5-ös verziójú csomagot használja. A régi azure/event-hubs 2-es verziójú csomagot használó gyorskezdésért olvassa el az [Események küldése és fogadása az azure/event-hubs 2-es verziójával című témakört.](event-hubs-node-get-started-send.md) 

## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- Node.js 8.x vagy újabb verzió. Töltse le a legújabb [hosszú távú támogatási (LTS) verziót.](https://nodejs.org)  
- Visual Studio Code (ajánlott) vagy bármely más integrált fejlesztői környezet (IDE).  
- Aktív Event Hubs névtér és eseményközpont. Ezek létrehozásához tegye a következő lépéseket: 

   1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy *Event Hubs*típusú névteret, majd szerezze be az okat a felügyeleti hitelesítő adatokat, amelyeket az alkalmazásnak az eseményközponttal való kommunikációhoz szüksége kell. 
   1. A névtér és az eseményközpont létrehozásához kövesse a [rövid útmutató utasításait: Hozzon létre egy eseményközpontot az Azure Portal használatával.](event-hubs-create.md)
   1. Folytassa az ebben a rövid útmutatóban található utasításokat követve. 
   1. Az Event Hub névterének kapcsolati karakterláncának lekérnie, kövesse a [Kapcsolati karakterlánc bekerülése című](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)rész utasításait. A rövid útmutató későbbi részében használandó kapcsolati karakterlánc rögzítése.
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az **Event Hubs névtér kapcsolati karakterláncát a** cikk utasításainak követésével kapja [meg: Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A kapcsolati karakterláncot később használja ebben a rövid útmutatóban.

### <a name="install-the-npm-package"></a>Az npm csomag telepítése
Az [Event Hubs node package manager (npm) csomagjának telepítéséhez](https://www.npmjs.com/package/@azure/event-hubs)nyisson meg egy *npm* elérési úton lévő parancssort, módosítsa a könyvtárat arra a mappára, ahol a mintákat meg szeretné tartani, majd futtassa ezt a parancsot:

```shell
npm install @azure/event-hubs
```

A fogadó oldalon két további csomagot kell telepítenie. Ebben a rövid útmutatóban az Azure Blob storage használatával megőrizheti az ellenőrzőpontokat, hogy a program ne olvassa el a már elolvasott eseményeket. Metaadat-ellenőrzőpontokat hajt végre a fogadott üzeneteken rendszeres időközönként egy blobban. Ez a megközelítés megkönnyíti az üzenetek fogadásának folytatását a későbbi helyből, ahol abbahagyta.

Futtassa az alábbi parancsot:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Események küldése

Ebben a szakaszban hozzon létre egy JavaScript-alkalmazást, amely eseményeket küld egy eseményközpontba.

1. Nyissa meg kedvenc szerkesztőjét, például a [Visual Studio-kódot.](https://code.visualstudio.com)
1. Hozzon létre egy *send.js*nevű fájlt, és illessze be a következő kódot a fájlba:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. A kódban valós értékeket használjon a következők lecserélésére:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Futtassa `node send.js` a fájl végrehajtásához. Ez a parancs három eseményből álló köteget küld az eseményközpontba.
1. Az Azure Portalon ellenőrizze, hogy az eseményközpont megkapta-e az üzeneteket. A **Metrikák csoportban váltson** **Üzenetek** nézetre. A lap frissítése a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg megis szállhat meg, hogy az üzenetek érkeztek.

    [![Annak ellenőrzése, hogy az eseményközpont megkapta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskód, beleértve a további tájékoztató megjegyzéseket, látogasson el a [GitHub sendEvents.js oldalon](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gratulálunk! Most eseményeket küldött egy eseményközpontba.


## <a name="receive-events"></a>Események fogadása
Ebben a szakaszban eseményeket kap egy eseményközpontból egy Azure Blob-tároló ellenőrzőpont-tároló használatával egy JavaScript-alkalmazásban. Metaadat-ellenőrzőpontokat hajt végre a fogadott üzeneteken rendszeres időközönként egy Azure Storage-blobban. Ez a megközelítés megkönnyíti az üzenetek fogadásának folytatását a későbbi helyből, ahol abbahagyta.

> [!NOTE]
> Ha az Azure Stack Hubon fut, akkor előfordulhat, hogy a storage blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető. Ha például az [Azure Stack Hub 2002-es verzióján](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)fut, a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben az ebben a szakaszban ismertetett lépések mellett kódot is hozzá kell adnia a Storage service API 2017-11-09-es verziójának célzásához. Egy adott Storage API-verzió célzásával kapcsolatos példát lásd: [JavaScript-](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) és [TypeScript-minták](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) a GitHubon. Az Azure Stack Hubon támogatott Azure Storage-szolgáltatásverzióiról az [Azure Stack Hub storage: Különbségek és szempontok](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)című további információkért tekintse meg.


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure-tárfiók és blobtároló létrehozása
Azure-tárfiók és blobtároló létrehozásához tegye a következő műveleteket:

1. [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Blob-tároló létrehozása a tárfiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [A kapcsolati karakterlánc beszereznie a tárfiókhoz](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Ügyeljen arra, hogy rögzítse a kapcsolati karakterláncot és a tároló nevét későbbi használatra a fogadási kódban.

### <a name="write-code-to-receive-events"></a>Kód írása események fogadásához

1. Nyissa meg kedvenc szerkesztőjét, például a [Visual Studio-kódot.](https://code.visualstudio.com)
1. Hozzon létre egy *receive.js*nevű fájlt, és illessze be a következő kódot a fájlba:

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. A kódban valós értékeket használjon a következő értékek lecserélésére:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Futtassa `node receive.js` a parancssorban a fájl végrehajtásához. Az ablakban meg kell jeleníteni a fogadott eseményekről szóló üzeneteket.

    > [!NOTE]
    > A teljes forráskód, beleértve a további tájékoztató megjegyzéseket, látogasson el a [GitHub receiveEventsUsingCheckpointStore.js oldalon](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gratulálunk! Most már kapott eseményeket az eseményközpontból. A fogadó program az eseményközpont alapértelmezett fogyasztói csoportjának összes partíciójáról fogadja az eseményeket.

## <a name="next-steps"></a>További lépések
Tekintse meg ezeket a mintákat a GitHubon:

- [JavaScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
