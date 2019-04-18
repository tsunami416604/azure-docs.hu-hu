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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677901"
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
- Klónozás a [minta GitHub-adattárban](https://github.com/Azure/azure-event-hubs-node) a gépen. 


## <a name="send-events"></a>Események küldése
Ez a szakasz bemutatja, hogyan hozhat létre egy Node.js-alkalmazás, amely elküldi az eseményeket egy eseményközpontba. 

### <a name="install-nodejs-package"></a>Node.js-csomag telepítése
Telepítse a Node.js-csomagot az Azure Event hubs a gépen. 

```shell
npm install @azure/event-hubs
```

Ha a Git-tárház még nem klónozható, az előfeltételek között ismertetett módon, töltse le a [minta](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) a Githubról. 

Az SDK-val rendelkeznie klónozta tartalmaz több minták azt mutatják be, hogyan küldhet eseményeket egy eseményközpontba node.js használatával. Ez a rövid a **simpleSender.js** példa. Figyelje meg a fogadott események, nyisson meg egy másik terminál, és események fogadása használatával a [mintát kapni](event-hubs-node-get-started-receive.md).

1. Nyissa meg a projekt a Visual Studio Code-ot. 
2. Hozzon létre egy fájlt **.env** alatt a **ügyfél** mappát. Másolja és illessze be a minta környezeti változókat a **sample.env** a gyökérmappában.
3. Az eseményközpont kapcsolati karakterláncával, a eseményközpont neve és a storage-végpont konfigurálása. Az eseményközpont kapcsolati karakterlánc beszerzésével kapcsolatban [kapcsolati sztring lekérése](event-hubs-create.md#create-an-event-hubs-namespace).
4. Az Azure CLI-t, navigáljon a **ügyfél** mappa elérési útja. Csomópont-csomagok telepítéséhez, és állítsa össze a projektet a következő parancsok futtatásával:

    ```shell
    npm i
    npm run build
    ```
5. Indítsa el az események küldése a következő parancs futtatásával: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Tekintse át a mintakódot 
Tekintse át a mintakódot a simpleSender.js fájlban, az eseményeket küld egy eseményközpontnak.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Fontos, hogy állítsa be a környezeti változókat a parancsfájl futtatása előtt. A parancssorban a következő példában látható módon konfigurálhatja őket, vagy a [dotenv csomag](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Események fogadása
Ez az oktatóanyag bemutatja, hogyan események fogadása az event hubs az Azure [EventProcessorHost](event-hubs-event-processor-host.md) Node.js-alkalmazásokban. Az EventProcessorHost (EPH) segítségével hatékonyan események fogadása az event hubs az eseményközpont fogyasztói csoportja összes partíciójára fogadók létrehozásával. Ez az ellenőrzőpontok metaadatait a fogadott üzenetek rendszeres időközönként egy Azure Storage-blobba. Ez a megközelítés egyszerűen folytatja az üzenetek fogadása kitöltését egy későbbi időpontban.

Ez a rövid útmutató kódja megtalálható [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>A Git-tárház klónozása
Töltse le vagy klónozza a [minta](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) a Githubról. 

### <a name="install-the-eventprocessorhost"></a>Az EventProcessorHost telepítése
Az EventProcessorHost az Event Hubs-modul telepítése. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Események fogadása EventProcessorHost használatával
Az SDK-val rendelkeznie klónozta tartalmaz több minták azt mutatják be, hogyan események fogadása az event hubs Node.js használatával. Ez a rövid a **singleEPH.js** példa. Figyelje meg a fogadott események, nyisson meg egy másik terminál, és események a [küldjön mintául szolgáló](event-hubs-node-get-started-send.md).

1. Nyissa meg a projekt a Visual Studio Code-ot. 
2. Hozzon létre egy fájlt **.env** alatt a **processzor** mappát. Másolja és illessze be a minta környezeti változókat a **sample.env** a gyökérmappában.
3. Az eseményközpont kapcsolati karakterláncával, a eseményközpont neve és a storage-végpont konfigurálása. Az eseményközpont a kapcsolati karakterlánc másolhatja **kapcsolati karakterlánc – elsődleges** kulcsot **RootManageSharedAccessKey** az Event Hub oldalon az Azure Portalon. Részletes lépéseiért lásd: [kapcsolati sztring lekérése](event-hubs-create.md#create-an-event-hubs-namespace).
4. Az Azure CLI-t, navigáljon a **processzor** mappa elérési útja. Csomópont-csomagok telepítéséhez, és állítsa össze a projektet a következő parancsok futtatásával:

    ```shell
    npm i
    npm run build
    ```
5. Események fogadása az event processzor gazdagéppel a következő parancs futtatásával:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Tekintse át a mintakódot 
A következő mintakód segítségével események fogadása az event hubs node.js használatával. Manuálisan hozzon létre egy sampleEph.js fájlt, és futtatni egy eseményközpontba események fogadására. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Fontos, hogy állítsa be a környezeti változókat a parancsfájl futtatása előtt. Ez a parancssorban a következő példában látható módon konfigurálhatja, vagy a [dotenv csomag](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

További minták annak [Itt](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkciók és az Azure Event Hubs terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)
- Figyelmébe más Node.js-minták az Event Hubs az [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
