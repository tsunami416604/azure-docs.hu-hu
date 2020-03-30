---
title: Események küldése vagy fogadása az Azure Event Hubs-tól JavaScript használatával (örökölt)
description: Ez a cikk egy forgatókönyvet biztosít egy JavaScript-alkalmazás létrehozásához, amely eseményeket küld/fogad az Azure Event Hubs-ba a régi azure/event-hubs 2-es verziójú csomag használatával.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162616"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Rövid útmutató: Események küldése az Azure Event Hubs-ból javaScript használatával (2-es@azure/event-hubs verzió)
Ez a rövid útmutató bemutatja, hogyan hozhat létre JavaScript-alkalmazásokat események küldéséhez és események fogadásához egy eseményközpontból az azure/event-hubs 2-es verziójú JavaScript-csomag használatával. 

> [!WARNING]
> Ez a rövid útmutató a régi azure/event-hubs 2-es verziójú csomagot használja. A csomag legújabb **5-ös verzióját** használó rövid útmutatóért [lásd: Események küldése és fogadása az azure/eventhubs 5-ös verziójával.](get-started-node-send-v2.md) Ha az alkalmazást a régi csomag használatával áthelyezi az újcsomagba, olvassa el az útmutatót az [azure/eventhubs 1-es verziójából az 5-ös verzióra való áttelepítéshez.](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md) 


## <a name="prerequisites"></a>Előfeltételek

Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- Node.js 8.x-es vagy újabb verzió. Töltse le a legújabb [https://nodejs.org](https://nodejs.org)LTS verziót a alkalmazásból.
- Visual Studio-kód (ajánlott) vagy bármely más IDE
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)ismertetett eljárást, majd folytassa az oktatóanyag következő lépéseivel. Ezután az eseményközpont névterének kapcsolati karakterláncát a cikk utasításainak követésével kapja meg: [Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében a kapcsolati karakterláncot használja.


### <a name="install-npm-package"></a>Npm csomag telepítése
Az [Event Hubs npm csomagjának telepítéséhez](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0) `npm` nyisson meg egy parancssort, amely az útjában van, módosítsa a könyvtárat arra a mappára, ahol a mintákat szeretné, majd futtassa ezt a parancsot

```shell
npm install @azure/event-hubs@2
```

Az [Event Processor Host npm csomagjának telepítéséhez](https://www.npmjs.com/package/@azure/event-processor-host)futtassa inkább az alábbi parancsot

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Események küldése

Ez a szakasz bemutatja, hogyan hozhat létre egy JavaScript-alkalmazást, amely eseményeket küld egy eseményközpontba. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

1. Nyissa meg kedvenc szerkesztőjét, például a [Visual Studio-kódot.](https://code.visualstudio.com) 
2. Hozzon létre `send.js` egy nevű fájlt, és illessze be az alábbi kódot. Az eseményközpont névterének kapcsolati karakterláncát a cikk utasításainak megfelelően szerezheti be: [Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Írja be a kapcsolati karakterláncot és az Event Hub nevét a fenti kódba
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához. Ezzel 100 eseményt küld az Event Hubra

Gratulálunk! Most eseményeket küldött egy eseményközpontba.


## <a name="receive-events"></a>Események fogadása

Ez a szakasz bemutatja, hogyan hozhat létre egy JavaScript-alkalmazást, amely eseményeket fogad az eseményközpont alapértelmezett fogyasztói csoportjának egyetlen partíciójáról. 

1. Nyissa meg kedvenc szerkesztőjét, például a [Visual Studio-kódot.](https://code.visualstudio.com) 
2. Hozzon létre `receive.js` egy nevű fájlt, és illessze be az alábbi kódot.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Írja be a kapcsolati karakterláncot és az Event Hub nevét a fenti kódba.
4. Ezután futtassa a parancsot `node receive.js` egy parancssorban a fájl végrehajtásához. Ez eseményeket fog kapni az Event Hub alapértelmezett fogyasztói csoportjának egyik partíciójáról

Gratulálunk! Most már kapott eseményeket az eseményközpontból.

## <a name="receive-events-using-event-processor-host"></a>Események fogadása az Event Processor Host használatával

Ez a szakasz bemutatja, hogyan fogadhat eseményeket egy eseményközpontból az Azure [EventProcessorHost](event-hubs-event-processor-host.md) használatával egy JavaScript-alkalmazásban. Az EventProcessorHost (EPH) segítségével hatékonyan fogadhateseményeket egy eseményközpontból azáltal, hogy egy eseményközpont fogyasztói csoportjának összes partícióján létrehozza a fogadókat. Az Azure Storage Blobban rendszeres időközönként ellenőrzőpontok a fogadott üzenetek metaadatait. Ez a megközelítés megkönnyíti az üzenetek fogadását onnan, ahol abbahagyta egy későbbi időpontban.

1. Nyissa meg kedvenc szerkesztőjét, például a [Visual Studio-kódot.](https://code.visualstudio.com) 
2. Hozzon létre `receiveAll.js` egy nevű fájlt, és illessze be az alábbi kódot.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Adja meg a kapcsolati karakterláncot és az Event Hub nevét a fenti kódban, valamint egy Azure Blob Storage kapcsolati karakterláncát
4. Ezután futtassa a parancsot `node receiveAll.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Most már eseményeket kapott az eseményközpontból az Event Processor Host használatával. Ez eseményeket fog kapni az Event Hub alapértelmezett fogyasztói csoportjának összes partíciójáról

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
- Tekintse meg az [Event Hubok](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) és az [Eseményprocesszor-állomás](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) egyéb JavaScript-mintáit a GitHubon
