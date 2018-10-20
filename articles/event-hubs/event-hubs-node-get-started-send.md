---
title: Események küldése az Azure Event Hubs Node.js használatával |} A Microsoft Docs
description: Ismerkedjen meg az események küldése az Event Hubs Node.js használatával.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 14ea98b9d31bee08b962e8b3801ed507472ba692
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455793"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Események küldése az Azure Event Hubs Node.js használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba, a Node.js-ben írt alkalmazások.

> [!NOTE]
> Ebben a rövid útmutatóban egy mintát, letöltheti a [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), cserélje le `EventHubConnectionString` és `EventHubName` karakterláncot az eseményközpontja értékeire, és futtassa azt. A lépéseket követheti azt is megteheti, ebben az oktatóanyagban hozhat létre saját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- NODE.js-verzió 8.x és a magasabb. Töltse le a legújabb LTS verziót [ https://nodejs.org ](https://nodejs.org).
- A Visual Studio Code-ot (ajánlott) vagy bármely más IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban.

## <a name="clone-the-sample-git-repository"></a>Klónozza a mintatárházat Git
Klónozza a mintatárházat Git [Github](https://github.com/Azure/azure-event-hubs-node) a gépen. 

## <a name="install-nodejs-package"></a>Node.js-csomag telepítése
Telepítse a Node.js-csomagot az Azure Event hubs a gépen. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>A Git-tárház klónozása
Töltse le vagy klónozza a [minta](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) a Githubról. 

## <a name="send-events"></a>Események küldése
Az SDK-val rendelkeznie klónozta tartalmaz több minták azt mutatják be, hogyan küldhet eseményeket egy eseményközpontba node.js használatával. Ez a rövid a **simpleSender.js** példa. Figyelje meg a fogadott események, nyisson meg egy másik terminál, és események fogadása használatával a [mintát kapni](event-hubs-node-get-started-receive.md).

1. Nyissa meg a projekt a Visual Studio Code-ot. 
2. Hozzon létre egy fájlt **.env** alatt a **ügyfél** mappát. Másolja és illessze be a minta környezeti változókat a **sample.env** a gyökérmappában.
3. Az eseményközpont kapcsolati karakterláncával, a eseményközpont neve és a storage-végpont konfigurálása. Az eseményközpont a kapcsolati karakterlánc másolhatja **kapcsolati karakterlánc – elsődleges** kulcsot **RootManageSharedAccessKey** az Event Hub oldalon az Azure Portalon. Részletes lépéseiért lásd: [kapcsolati sztring lekérése](event-hubs-create.md#create-an-event-hubs-namespace).
4. Az Azure CLI-t, navigáljon a **ügyfél** mappa elérési útja. Csomópont-csomagok telepítéséhez, és állítsa össze a projektet a következő parancsok futtatásával:

    ```nodejs
    npm i
    npm run build
    ```
5. Indítsa el az események küldése a következő parancs futtatásával: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Tekintse át a mintakódot 
A következő mintakód segítségével eseményeket küld egy eseményközpontnak node.js használatával. Manuálisan hozzon létre egy sampleSender.js fájlt, majd futtassa eseményeket küld egy eseményközpontnak. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

Fontos, hogy állítsa be a környezeti változókat a parancsfájl futtatása előtt. Ez a parancssorban a következő példában látható módon konfigurálhatja, vagy a [dotenv csomag](https://www.npmjs.com/package/dotenv#dotenv). 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban az elküldött üzeneteket az eseményközpontba Node.js használatával. Események fogadása az eseményközpontok a Node.js használatával kapcsolatban lásd: [események fogadása az event hubs – Node.js](event-hubs-node-get-started-receive.md)

Figyelmébe más Node.js-minták az Event Hubs az [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
