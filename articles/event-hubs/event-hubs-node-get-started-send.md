---
title: 'Gyors útmutató: események küldése és fogadása a Node. js használatával – Azure Event Hubs'
description: 'Gyors útmutató: Ez a cikk egy olyan Node. js-alkalmazás létrehozásának bemutatóját ismerteti, amely az Azure Event Hubsról küld eseményeket.'
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: ded2c83bc648e509c8cf00236cdf453b9c61af53
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720573"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Gyors útmutató: események küldése vagy fogadása az Azure Event Hubs a Node. js használatával

Az Azure Event Hubs egy Big streaming platform-és esemény-betöltési szolgáltatás, amely másodpercenként több millió eseményt képes fogadni és feldolgozni. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan lehet Node. js-alkalmazásokat létrehozni események küldésére vagy események fogadására az Event hub-ból.

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- A Node. js 8. x vagy újabb verziója. Töltse le a legújabb LTS-verziót [https://nodejs.orgról ](https://nodejs.org).
- Visual Studio Code (ajánlott) vagy bármely más IDE
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást, majd folytassa az oktatóanyag következő lépéseivel. Ezután szerezze be a kapcsolati karakterláncot az Event hub-névtérhez a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében a kapcsolatok karakterláncát kell használnia.


### <a name="install-npm-package"></a>NPM-csomag telepítése
A [Event Hubs NPM-csomagjának](https://www.npmjs.com/package/@azure/event-hubs)telepítéséhez nyisson meg egy parancssort, amely az elérési útjában `npm` van, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```shell
npm install @azure/event-hubs
```

Ha telepíteni szeretné az [NPM csomagot az Event Processor Host számára](https://www.npmjs.com/package/@azure/event-processor-host), futtassa inkább az alábbi parancsot.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Események küldése

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Node. js-alkalmazást, amely eseményeket küld az Event hub-nak. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `send.js` nevű fájlt, és illessze be az alábbi kódot. Az Event hub-névtér kapcsolati karakterláncának beszerzéséhez kövesse a következő cikk utasításait: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

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
3. Adja meg a kapcsolati karakterláncot és az Event hub nevét a fenti kódban
4. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node send.js`. A művelet 100 eseményt küld az Event hub-nak

Gratulálunk! Már elvégezte az események elküldése az Event hubhoz.


## <a name="receive-events"></a>Események fogadása

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Node. js-alkalmazást, amely az Event hub alapértelmezett fogyasztói csoportjának egyetlen partíciójában fogad eseményeket. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `receive.js` nevű fájlt, és illessze be az alábbi kódot.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

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
3. Adja meg a kapcsolati karakterláncot és az Event hub nevét a fenti kódban.
4. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node receive.js`. Ez a művelet az Event hub alapértelmezett fogyasztói csoportjának egyik partíciójában fogad eseményeket.

Gratulálunk! Most már fogadott eseményeket az Event hub-ból.

## <a name="receive-events-using-event-processor-host"></a>Események fogadása az Event Processor Host használatával

Ez a szakasz bemutatja, hogyan fogadhat eseményeket egy Event hub-ból az Azure [EventProcessorHost](event-hubs-event-processor-host.md) egy Node. js-alkalmazásban való használatával. A EventProcessorHost (EF) segítséget nyújt az Event hub eseményeinek hatékony fogadásához azáltal, hogy az Event hub fogyasztói csoportjában lévő összes partíción létrehoz egy fogadót. A beérkező üzenetekre vonatkozó metaadatokat a Azure Storage Blob rendszeres időközönként áthelyezi. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan később kilépett.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `receiveAll.js` nevű fájlt, és illessze be az alábbi kódot.
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
3. Adja meg a kapcsolati karakterláncot és az Event hub nevét a fenti kódban, valamint az Azure-Blob Storage kapcsolati karakterláncát.
4. Ezután futtassa a parancsot egy parancssorban a fájl végrehajtásához `node receiveAll.js`.

Gratulálunk! Az Event hub eseményeit az Event Processor Host használatával fogadta el. Ez a művelet az Event hub alapértelmezett fogyasztói csoportjának összes partíciójának eseményeit fogadja.

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
- Tekintse meg a további Node. js-mintákat a GitHubon lévő [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) és [Event Processor-gazdagéphez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples)
