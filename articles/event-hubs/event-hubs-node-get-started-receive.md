---
title: Események fogadása node.js-szel – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a cikk egy forgatókönyv Node.js-alkalmazás, amely az Azure Event Hubsból fogadja az eseményeket létrehozásához.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6986a5c40b51300b631f3fdce6c3a88b43fb5b11
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730883"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Események fogadása az Azure Event Hubs Node.js használatával

Az Azure Event Hubs szolgáltatás egy kiválóan méretezhető eseménykezelő rendszer, amely képes kezelni a több millió csatlakoztatott eszközök és más rendszerek által létrehozott hatalmas adatmennyiségek elemzését teszi az alkalmazások engedélyezése események száma másodpercenként. Adatoknak egy eseményközpontba való összegyűjtését, miután kap, és zpracovávat události használatával a folyamaton belüli kezelők vagy más elemzési rendszerek továbbítással. A feldolgozása előtt eseményadatokat az Azure Storage-ban vagy az Azure Data Lake Store is rögzíteni.  

Az Event Hubs kapcsolatos további információkért tekintse meg a [Event Hubs – áttekintés](event-hubs-about.md).

Ez az oktatóanyag bemutatja, hogyan események fogadása az event hubs az Azure [EventProcessorHost](event-hubs-event-processor-host.md) Node.js-alkalmazásokban. Az EventProcessorHost (EPH) segítségével hatékonyan események fogadása az event hubs az eseményközpont fogyasztói csoportja összes partíciójára fogadók létrehozásával. Ez az ellenőrzőpontok metaadatait a fogadott üzenetek rendszeres időközönként egy Azure Storage-blobba. Ez a megközelítés egyszerűen folytatja az üzenetek fogadása kitöltését egy későbbi időpontban.

Ez a rövid útmutató kódja megtalálható [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Események küldése az Event Hubsba Node.js használatával, lásd: Ez a cikk: [Események küldése az Azure Event Hubs használatával Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- NODE.js-verzió 8.x és a magasabb. Töltse le a legújabb LTS verziót [ https://nodejs.org ](https://nodejs.org). Ne használjon node.js régebbi LTS verzióját. 
- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-namespace-and-event-hub"></a>A névtér és az eseményközpont létrehozása
Az első lépés az Event Hubs-névtér létrehozása egy eseményközponttal az Azure portal használatával. Ha nem rendelkezik egy meglévő, ezek az entitások utasításait követve létrehozhat [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót
Az EventProcessorHost használatához egy Azure Storage-fiókot kell rendelkeznie. Az állapotadatokat, mint például a partíciók és az ellenőrzőpontok bérleteket az eseménystream között vannak megosztva az Azure Storage-tárolókat használ fogadók. Létrehozhat egy Azure Storage-fiókot a következő témakör utasításait követve [Ez a cikk](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>A Git-tárház klónozása
Töltse le vagy klónozza a [minta](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) a Githubról. 

## <a name="install-the-eventprocessorhost"></a>Az EventProcessorHost telepítése
Az EventProcessorHost az Event Hubs-modul telepítése. 

```shell
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Események fogadása EventProcessorHost használatával
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

## <a name="review-the-sample-code"></a>Tekintse át a mintakódot 
A következő mintakód segítségével események fogadása az event hubs node.js használatával. Manuálisan hozzon létre egy sampleEph.js fájlt, és futtatni egy eseményközpontba események fogadására. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

Ebben a rövid útmutatóban létrehozott egy Node.js-alkalmazás, amely a fogadott üzeneteket egy eseményközpontból. Hogyan küldhet eseményeket egy eseményközpontba Node.js használatával kapcsolatban lásd: [események küldése az event hubs - Node.js](event-hubs-node-get-started-send.md).

<!-- Links -->
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
