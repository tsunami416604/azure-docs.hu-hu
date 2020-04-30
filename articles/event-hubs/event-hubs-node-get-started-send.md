---
title: Események küldése vagy fogadása az Azure Event Hubs a JavaScript használatával (örökölt)
description: Ez a cikk egy olyan JavaScript-alkalmazás létrehozásához nyújt útmutatást, amely az Azure Event Hubs a régi Azure/Event-hubok 2-es verziójú csomag használatával küld/fogad eseményeket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162616"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Gyors útmutató: események küldése vagy fogadása az Azure Event Hubs a JavaScript használatával@azure/event-hubs (2. verzió)
Ez a rövid útmutató bemutatja, hogyan hozhatók létre JavaScript-alkalmazások események küldésére és fogadására az Event hub-ból az Azure/Event-hubok 2. verziójú JavaScript-csomag használatával. 

> [!WARNING]
> Ez a rövid útmutató a régi Azure/Event-hubok 2-es verziójú csomagot használja. A csomag legújabb **5. verzióját** használó gyors útmutató: [események küldése és fogadása az Azure/eventhubs 5-ös verziójának használatával](get-started-node-send-v2.md). Ha át szeretné helyezni az alkalmazást a régi csomag használatával az új verzióra, tekintse meg az [Azure/eventhubs 1. verziójának 5-ös verziójára történő Migrálás útmutatóját](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Előfeltételek

Ha még nem ismeri az Azure Event Hubs-t, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- A Node. js 8. x vagy újabb verziója. Töltse le a legújabb LTS- [https://nodejs.org](https://nodejs.org)verziót innen:.
- Visual Studio Code (ajánlott) vagy bármely más IDE
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást, majd folytassa az oktatóanyag következő lépéseivel. Ezután szerezze be a kapcsolati karakterláncot az Event hub-névtérhez a következő cikk utasításait követve: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében a kapcsolatok karakterláncát kell használnia.


### <a name="install-npm-package"></a>NPM-csomag telepítése
A [Event Hubs NPM-csomagjának](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)telepítéséhez nyisson meg egy parancssort, amely `npm` az elérési útjában található, módosítsa a könyvtárat arra a mappára, ahol a mintákat használni szeretné, majd futtassa ezt a parancsot.

```shell
npm install @azure/event-hubs@2
```

Ha telepíteni szeretné az [NPM csomagot az Event Processor Host számára](https://www.npmjs.com/package/@azure/event-processor-host), futtassa inkább az alábbi parancsot.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Események küldése

Ebből a szakaszból megtudhatja, hogyan hozhat létre olyan JavaScript-alkalmazást, amely eseményeket küld az Event hub-nak. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `send.js` nevű fájlt, és illessze be az alábbi kódot. Az Event hub-névtér kapcsolati karakterláncának beszerzéséhez kövesse a következő cikk utasításait: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Adja meg a kapcsolati karakterláncot és az Event hub nevét a fenti kódban
4. Ezután futtassa a parancsot `node send.js` egy parancssorban a fájl végrehajtásához. A művelet 100 eseményt küld az Event hub-nak

Gratulálunk! Már elvégezte az események elküldése az Event hubhoz.


## <a name="receive-events"></a>Események fogadása

Ebből a szakaszból megtudhatja, hogyan hozhat létre olyan JavaScript-alkalmazást, amely az Event hub alapértelmezett fogyasztói csoportjának egyetlen partíciójában fogad eseményeket. 

1. Nyissa meg a kedvenc szerkesztőjét, például a [Visual Studio Code](https://code.visualstudio.com)-ot. 
2. Hozzon létre egy `receive.js` nevű fájlt, és illessze be az alábbi kódot.
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
3. Adja meg a kapcsolati karakterláncot és az Event hub nevét a fenti kódban.
4. Ezután futtassa a parancsot `node receive.js` egy parancssorban a fájl végrehajtásához. Ez a művelet az Event hub alapértelmezett fogyasztói csoportjának egyik partíciójában fogad eseményeket.

Gratulálunk! Most már fogadott eseményeket az Event hub-ból.

## <a name="receive-events-using-event-processor-host"></a>Események fogadása az Event Processor Host használatával

Ez a szakasz bemutatja, hogyan fogadhat eseményeket egy Event hub-ból az Azure [EventProcessorHost](event-hubs-event-processor-host.md) egy JavaScript-alkalmazásban való használatával. A EventProcessorHost (EF) segítséget nyújt az Event hub eseményeinek hatékony fogadásához azáltal, hogy az Event hub fogyasztói csoportjában lévő összes partíción létrehoz egy fogadót. A beérkező üzenetekre vonatkozó metaadatokat a Azure Storage Blob rendszeres időközönként áthelyezi. Ezzel a módszerrel egyszerűen folytathatja az üzenetek fogadását, ahonnan később kilépett.

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
4. Ezután futtassa a parancsot `node receiveAll.js` egy parancssorban a fájl végrehajtásához.

Gratulálunk! Az Event hub eseményeit az Event Processor Host használatával fogadta el. Ez a művelet az Event hub alapértelmezett fogyasztói csoportjának összes partíciójának eseményeit fogadja.

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
- Tekintse meg a GitHubon található [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) -és [Event Processor-gazdagép](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) további JavaScript-mintáit
