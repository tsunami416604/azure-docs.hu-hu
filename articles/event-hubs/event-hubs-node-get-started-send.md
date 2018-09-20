---
title: Események küldése az Azure Event Hubs Node.js használatával |} A Microsoft Docs
description: Ismerkedjen meg az események küldése az Event Hubs Node.js használatával.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 3497abdd571282974d85ebaa58d06b47cbe3b159
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368680"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Események küldése az Azure Event Hubs Node.js használatával

Az Azure Event Hubs szolgáltatás egy kiválóan méretezhető eseménykezelő rendszer, amely képes kezelni a több millió csatlakoztatott eszközök és más rendszerek által létrehozott hatalmas adatmennyiségek elemzését teszi az alkalmazások engedélyezése események száma másodpercenként. Adatoknak egy eseményközpontba való összegyűjtését, miután kap, és zpracovávat události használatával a folyamaton belüli kezelők vagy más elemzési rendszerek továbbítással.

Az Event Hubs kapcsolatos további információkért tekintse meg a [Event Hubs – áttekintés](event-hubs-about.md).

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba, a Node.js-ben írt alkalmazások. A Node.js Event Processor Host csomaggal események fogadására, lásd: [a megfelelő Receive cikk](event-hubs-node-get-started-receive.md).

Érhető el ebben a rövid kód [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- NODE.js-verzió 8.x és a magasabb. Töltse le a legújabb LTS verziót [ https://nodejs.org ](https://nodejs.org).
- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a virtuális gép létrehozásának megkezdése előtt.
- A Visual Studio Code-ot (ajánlott) vagy bármely más IDE

## <a name="create-a-namespace-and-event-hub"></a>A névtér és az eseményközpont létrehozása
Az első lépés az Event Hubs-névtér létrehozása egy eseményközponttal az Azure portal használatával. Ha nem rendelkezik egy meglévő, ezek az entitások utasításait követve létrehozhat [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](event-hubs-create.md).

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
3. Az eseményközpont kapcsolati karakterláncával, a eseményközpont neve és a storage-végpont konfigurálása. Az eseményközpont a kapcsolati karakterlánc másolhatja **kapcsolati karakterlánc – elsődleges** kulcsot **RootManageSharedAccessKey** az Event Hub oldalon az Azure Portalon. Részletes lépéseiért lásd: [kapcsolati sztring lekérése](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace).
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

## <a name="next-steps"></a>További lépések

További információ az Event Hubs a következő cikkekben talál:

* [Események fogadása Node.js használatával](event-hubs-node-get-started-receive.md)
* [Példák a Githubon](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
