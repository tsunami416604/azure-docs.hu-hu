---
title: NODE.js-szel – Azure Event Hubs használatával események küldéséhez és fogadásához |} A Microsoft Docs
description: Ez a cikk egy forgatókönyv Node.js-alkalmazás, amely az Azure Event Hubsból küldi az eseményeket létrehozásához.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539344"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Események küldése vagy események fogadása az Azure Event Hubs Node.js használatával

Az Azure Event Hubs szolgáltatás a egy Big Data-platform- és esemény-feldolgozó szolgáltatás, amely képes fogadni és másodpercenként több millió folyamatán. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag leírja, hogyan hozhat létre a Node.js-alkalmazások eseményeket küld vagy események fogadása az event hub.

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- NODE.js-verzió 8.x és a magasabb. Töltse le a legújabb LTS verziót [ https://nodejs.org ](https://nodejs.org).
- A Visual Studio Code-ot (ajánlott) vagy bármely más IDE
- **Event Hubs-névtér és eseményközpont létrehozása**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban. Ezt követően a kapcsolati sztring lekérése az eseményközpont-névtér kövesse a cikkben szereplő: [Kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében használja a kapcsolati karakterláncot.


### <a name="install-npm-package"></a>Telepítse az npm-csomag
Telepítése a [npm-csomag az Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), nyisson meg egy parancssort, amelynek `npm` annak elérési úton, módosítsa a könyvtárat a mappára, ahol szeretné a mintákban rendelkezik, és futtassa a parancsot

```shell
npm install @azure/event-hubs
```

Telepítése a [npm-csomag az Event Processor Host](https://www.npmjs.com/package/@azure/event-processor-host)futtassa az alábbi parancs helyett

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Események küldése

Ez a szakasz bemutatja, hogyan hozhat létre egy Node.js-alkalmazás, amely elküldi az eseményeket egy eseményközpontba. 

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com). 
2. Hozzon létre egy fájlt nevű `send.js` , és illessze be az alábbi kódot oda.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. Adja meg a fenti kód a kapcsolati karakterláncot, és az Eseményközpont neve
4. Ezután futtassa a parancsot `node send.js` hajtsa végre a fájlt a parancssorban. Ez 100 eseményeket küld az Event Hubs

Gratulálunk! Események küldött egy eseményközpontba.


## <a name="receive-events"></a>Események fogadása

Ez a szakasz bemutatja, hogyan hozhat létre egy Node.js-alkalmazás, amely fogadja az eseményeket az eseményközpontban az alapértelmezett felhasználói csoport egyetlen partícióról. 

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com). 
2. Hozzon létre egy fájlt nevű `receive.js` , és illessze be az alábbi kódot oda.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. Adja meg a kapcsolati karakterláncot, és az Eseményközpont nevével, a fenti kódban.
4. Ezután futtassa a parancsot `node receive.js` hajtsa végre a fájlt a parancssorban. Ez eseményeit fogja megkapni a partíciók az Eseményközpont alapértelmezett felhasználói csoport egyik

Gratulálunk! Sikeresen fogadott események eseményközpontból.

## <a name="receive-events-using-event-processor-host"></a>Események fogadása Event Processor Host használatával

Ez a szakasz bemutatja, hogyan események fogadása az event hubs az Azure [EventProcessorHost](event-hubs-event-processor-host.md) Node.js-alkalmazásokban. Az EventProcessorHost (EPH) segítségével hatékonyan események fogadása az event hubs az eseményközpont fogyasztói csoportja összes partíciójára fogadók létrehozásával. Ez az ellenőrzőpontok metaadatait a fogadott üzenetek rendszeres időközönként egy Azure Storage-blobba. Ez a megközelítés egyszerűen folytatja az üzenetek fogadása kitöltését egy későbbi időpontban.

1. Nyissa meg a kedvenc szerkesztőjében, például [Visual Studio Code](https://code.visualstudio.com). 
2. Hozzon létre egy fájlt nevű `receiveAll.js` , és illessze be az alábbi kódot oda.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Adja meg a kapcsolati karakterláncot, és az Eseményközpont nevével együtt kapcsolati karakterláncot a fenti kód egy Azure Blob Storage
4. Ezután futtassa a parancsot `node receiveAll.js` hajtsa végre a fájlt a parancssorban.

Gratulálunk! Sikeresen fogadott események eseményközpontból Event Processor Host használatával. Ez eseményeit fogja megkapni az alapértelmezett felhasználói csoport, az eseményközpont összes partíciója

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkciók és az Azure Event Hubs terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
- Tekintse meg az egyéb Node.js-minták [az Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) és [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) a Githubon
