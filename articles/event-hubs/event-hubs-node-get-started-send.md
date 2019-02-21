---
title: NODE.js-szel – Azure Event Hubs használatával események küldése |} A Microsoft Docs
description: Ez a cikk egy forgatókönyv Node.js-alkalmazás, amely az Azure Event Hubsból küldi az eseményeket létrehozásához.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447718"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Események küldése az Azure Event Hubs Node.js használatával

Az Azure Event Hubs szolgáltatás a egy Big Data-platform- és esemény-feldolgozó szolgáltatás, amely képes fogadni és másodpercenként több millió folyamatán. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba, a Node.js-ben írt alkalmazások.

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- NODE.js-verzió 8.x és a magasabb. Töltse le a legújabb LTS verziót [ https://nodejs.org ](https://nodejs.org).
- A Visual Studio Code-ot (ajánlott) vagy bármely más IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban.

A kapcsolati sztring lekérése az eseményközpont-névtér kövesse a cikkben szereplő: [Kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében használja a kapcsolati karakterláncot.

## <a name="clone-the-sample-git-repository"></a>Klónozza a mintatárházat Git
Klónozza a mintatárházat Git [GitHub](https://github.com/Azure/azure-event-hubs-node) a gépen. 

## <a name="install-nodejs-package"></a>Node.js-csomag telepítése
Telepítse a Node.js-csomagot az Azure Event hubs a gépen. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>A Git-tárház klónozása
Töltse le vagy klónozza a [minta](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) a Githubról. 

## <a name="send-events"></a>Események küldése
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


## <a name="review-the-sample-code"></a>Tekintse át a mintakódot 
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

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban az elküldött üzeneteket az eseményközpontba Node.js használatával. Események fogadása az eseményközpontok a Node.js használatával kapcsolatban lásd: [események fogadása az event hubs – Node.js](event-hubs-node-get-started-receive.md)

Figyelmébe más Node.js-minták az Event Hubs az [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
