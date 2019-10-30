---
title: Reagálás Blob Storage modul eseményeire – Azure Event Grid IoT Edge | Microsoft Docs
description: Reagálás Blob Storage modul eseményeire
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 851b5607ad5413cd1a594f788cb294ee7790e8eb
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043171"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Oktatóanyag: válaszadás Blob Storage eseményekre IoT Edge (előzetes verzió)
Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az Azure Blob Storaget a IoT modulban, amely Event Grid közzétevőként fog működni a Blobok létrehozásával és a Blobok törlésével kapcsolatos események Event Grid.  

A IoT Edge Azure Blob Storageának áttekintését lásd: [azure blob Storage on IoT Edge](../../iot-edge/how-to-store-data-blob.md) és annak szolgáltatásai.

> [!WARNING]
> Az Azure Blob Storage on IoT Edge Integration with Event Grid előzetes verzióban érhető el

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) , ha még nem rendelkezik ilyennel. 
* **Azure IoT hub és IoT Edge eszköz** – kövesse a [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows rendszerű eszközök](../../iot-edge/quickstart.md) gyors üzembe helyezésének lépéseit, ha még nem rendelkezik ilyennel.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge modul üzembe helyezése

A modulokat többféleképpen is telepítheti egy IoT Edge eszközre, és mindegyik a IoT Edge Azure Event Grid. Ez a cikk azokat a lépéseket ismerteti, amelyekkel telepítheti a Event Gridt a Azure Portal IoT Edge.

>[!NOTE]
> Ebben az oktatóanyagban a Event Grid-modult az adatmegőrzés nélkül fogja telepíteni. Ez azt jelenti, hogy a jelen oktatóanyagban létrehozott összes témakört és előfizetést a modul újbóli üzembe helyezésekor törli a rendszer. Az adatmegőrzés beállításával kapcsolatos további információkért tekintse meg a következő cikkeket: állapot megtartása [Linuxon](persist-state-linux.md) vagy [az állapot megőrzése a Windowsban](persist-state-windows.md). Éles számítási feladatokhoz javasoljuk, hogy az Event Grid modult az adatmegőrzéssel telepítse.


### <a name="select-your-iot-edge-device"></a>IoT Edge eszköz kiválasztása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Navigáljon a IoT Hub.
1. Válassza a **IoT Edge** lehetőséget az **automatikus eszközkezelés** szakasz menüjében. 
1. Kattintson a céleszköz AZONOSÍTÓjának az eszközök listájáról
1. Válassza a **Modulok beállítása** lehetőséget. Tartsa meg a lapot. A következő szakaszban ismertetett lépésekkel folytathatja a lépéseket.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. A Azure Portal tartalmaz egy varázslót, amely végigvezeti az üzembe helyezési jegyzék létrehozásán, a JSON-dokumentum manuális létrehozása helyett.  Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása**és az **üzembe helyezés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** lehetőséget.
1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
1. Adja meg a tároló nevét, képét, tároló-létrehozási beállításait:

   * **Név**: eventgridmodule
   * **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Tároló-létrehozási beállítások**:

    ```json
        {
          "Env": [
           "inbound:serverAuth:tlsPolicy=enabled",
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Kattintson a **Mentés** gombra.
 1. Folytassa a következő szakasszal a Azure Functions modul hozzáadásához

    >[!IMPORTANT]
    > Ebben az oktatóanyagban a Event Grid modult fogja telepíteni a HTTP/HTTPs-kérések, az ügyfél-hitelesítés és a HTTP-előfizetők engedélyezése érdekében. Éles számítási feladatokhoz javasoljuk, hogy csak HTTPs-kérelmeket és-előfizetőket engedélyezzen engedélyezett ügyfél-hitelesítéssel. A Event Grid modul biztonságos konfigurálásával kapcsolatos további információkért lásd: [Biztonság és hitelesítés](security-authentication.md).
    

## <a name="deploy-azure-function-iot-edge-module"></a>Az Azure Function IoT Edge modul üzembe helyezése

Ebből a szakaszból megtudhatja, hogyan helyezheti üzembe a Azure Functions IoT modult, amely Event Grid előfizetőként fog működni, amely az események kézbesítésére szolgál.

>[!IMPORTANT]
>Ebben a szakaszban egy minta Azure-függvény alapú előfizető-modult fog üzembe helyezni. Természetesen bármely olyan egyéni IoT modul lehet, amely képes figyelni a HTTP POST-kérelmeket.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** újra lehetőséget. 
1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
1. Adja meg a tároló nevét, képét és tároló-létrehozási beállításait:

   * **Név**: előfizető
   * **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Tároló-létrehozási beállítások**:

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Kattintson a **Mentés** gombra.
1. Folytassa a következő szakasszal az Azure Blob Storage modul hozzáadásához

> [!NOTE]
> Blob Storage modul HTTP-n keresztül teszi közzé az eseményeket. Győződjön meg arról, hogy a Event Grid modul lehetővé teszi a HTTP-és HTTPS-kérelmeket a következő konfigurációval: `inbound:serverAuth:tlsPolicy=enabled`.

## <a name="deploy-azure-blob-storage-module"></a>Az Azure Blob Storage modul üzembe helyezése

Ebből a szakaszból megtudhatja, hogyan helyezheti üzembe az Azure Blob Storage modult, amely Event Grid közzétevőként és törölt eseményként fog működni.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **telepítési modulok** szakaszban válassza a **Hozzáadás** lehetőséget.
2. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**
3. Adja meg a tároló nevét, képét és tároló-létrehozási beállításait:

   * **Név**: azureblobstorageoniotedge
   * **Rendszerkép URI-ja**: MCR.microsoft.com/Azure-Blob-Storage:1.2.2-Preview
   * **Tároló-létrehozási beállítások**:

```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
```

4. Frissítse a vágólapra másolt JSON-t a következő információkkal:

   - Cserélje le a `<your storage account name>`t egy megjegyezhető névre. A fiókok nevének 3 – 24 karakter hosszúnak kell lennie, kisbetűkkel és számokkal. Nincsenek szóközök.

   - Cserélje le a `<your storage account key>`t egy 64 bájtos Base64-kulcsra. Létrehozhat egy kulcsot olyan eszközökkel, mint a [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Ezeket a hitelesítő adatokat fogja használni a blob Storage más modulokból való eléréséhez.

   - Cserélje le a `<event grid module name>`t a Event Grid modul nevére.
   - Cserélje le a `<storage mount>`t a tároló operációs rendszerének megfelelően.
     - Linux-tárolók esetén **a saját kötet:/blobroot**
     - Windows-tárolók esetén**a saját kötet: C:/BlobRoot**

5. Kattintson a **Mentés** gombra.
6. Az útvonalak szakasz folytatásához kattintson a **tovább** gombra.

 ### <a name="setup-routes"></a>Telepítési útvonalak

Tartsa meg az alapértelmezett útvonalakat, és kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="review-deployment"></a>Központi telepítés áttekintése

1. A felülvizsgálati szakasz megjeleníti a JSON-telepítési jegyzékfájlt, amelyet az előző szakaszban megadott beállítások alapján hoztak létre. Győződjön meg arról, hogy a következő négy modul jelenik meg: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **előfizető** és **azureblobstorageoniotedge** , amelyet az összes üzembe helyezett.
2. Tekintse át az üzembe helyezési adatokat, majd válassza a **Küldés**lehetőséget.

## <a name="verify-your-deployment"></a>Az üzemelő példány ellenőrzése

1. Miután elküldte az üzembe helyezést, térjen vissza az IoT hub IoT Edge lapjára.
2. Válassza ki azt a **IoT Edge eszközt** , amelyet a központi telepítéshez céloz, hogy megnyissa a részleteit.
3. Az eszköz részletei között ellenőrizze, hogy a eventgridmodule, az előfizetői és a azureblobstorageoniotedge modulok szerepelnek-e a **telepítésben** és az **eszköz által jelentett**módon.

   Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.

## <a name="publish-created-and-deleted-events"></a>Létrehozott és törölt események közzététele

1. Ez a modul automatikusan létrehozza a témakör **MicrosoftStorage**. Annak ellenőrzése, hogy létezik-e
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

2. Az előfizetők regisztrálhatnak a témakörben közzétett eseményekre. Ha bármilyen eseményt szeretne kapni, létre kell hoznia egy Event Grid-előfizetést a **MicrosoftStorage** témakörben.
    1. Hozza létre a blobsubscription. JSON fájlt a következő tartalommal. A hasznos adatokkal kapcsolatos részletekért tekintse meg az [API-dokumentációt](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A **endpointType** tulajdonság azt adja meg, hogy az előfizető egy **webhook**.  A **endpointUrl** meghatározza azt az URL-címet, amelyen az előfizető eseményeket figyel. Ez az URL-cím megfelel a korábban üzembe helyezett Azure Function-mintának.

    2. A következő parancs futtatásával hozzon létre egy előfizetést a témakörhöz. Ellenőrizze, hogy megjelenik-e a HTTP-állapotkód `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. A következő parancs futtatásával ellenőrizheti, hogy az előfizetés sikeresen létrejött-e. Az 200-es HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

    ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) letöltése és [a helyi tárolóhoz való kapcsolódása](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Esemény-kézbesítés ellenőrzése

### <a name="verify-blobcreated-event-delivery"></a>BlobCreated-esemény kézbesítésének ellenőrzése

1. Fájlokat tölthet fel a helyi tárhelyre a Azure Storage Explorer-ból, és a modul automatikusan közzéteszi a Create Events-t. 
2. Tekintse meg a létrehozási esemény előfizetői naplóit. Az [események kézbesítésének ellenőrzéséhez](pub-sub-events-webhook-local.md#verify-event-delivery) kövesse az alábbi lépéseket

    Példa a kimenetre:

    ```json
            Received event data [
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>BlobDeleted-esemény kézbesítésének ellenőrzése

1. Törölje a blobokat a helyi tárolóból Azure Storage Explorer használatával, és a modul automatikusan közzéteszi a törlési eseményeket. 
2. Tekintse meg az előfizetői naplókat a DELETE eseményhez. Az [események kézbesítésének ellenőrzéséhez](pub-sub-events-webhook-local.md#verify-event-delivery) kövesse az alábbi lépéseket

    Példa a kimenetre:
    
    ```json
            Received event data [
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

Gratulálunk! Elvégezte az oktatóanyagot. A következő szakaszokban részletes információkat talál az esemény tulajdonságairól.

### <a name="event-properties"></a>Esemény tulajdonságai

Az alábbi lista tartalmazza a támogatott események tulajdonságait, valamint azok típusait és leírásait. 

| Tulajdonság | Type (Típus) | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Event Grid megadja ezt az értéket. |
| Tulajdonos | sztring | Közzétevő által megadott elérési út az esemény tárgya számára. |
| EventType | sztring | Az eseményforrás egyik regisztrált eseménytípus. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | BLOB Storage-események |
| dataVersion | sztring | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | sztring | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Type (Típus) | Leírás |
| -------- | ---- | ----------- |
| api-t | sztring | Az eseményt kiváltó művelet. A következő értékek egyike lehet: <ul><li>BlobCreated – a megengedett értékek a következők: `PutBlob` és `PutBlockList`</li><li>BlobDeleted – megengedett értékek: `DeleteBlob`, `DeleteAfterUpload` és `AutoDelete`. <p>A `DeleteAfterUpload` esemény akkor jön létre, amikor a rendszer automatikusan törli a blobot, mert a deleteAfterUpload kívánt tulajdonság értéke TRUE (igaz). </p><p>`AutoDelete` esemény jön létre, amikor a rendszer automatikusan törli a blobot, mert a deleteAfterMinutes kívánt tulajdonság értéke lejárt.</p></li></ul>|
| ügyfélkérelem | sztring | ügyfél által megadott kérelem azonosítója a tárolási API-művelethez. Ez az azonosító használható az Azure Storage diagnosztikai naplóinak az "ügyfél-kérelem-azonosító" mezővel való összekapcsolására a naplókban, és az "x-MS-Client-Request-id" fejléc használatával megadható az ügyfelek kérései. Részletekért lásd: [naplózási formátum](/rest/api/storageservices/storage-analytics-log-format). |
| Kérelemazonosító | sztring | A szolgáltatás által generált kérelem azonosítója a tárolási API-művelethez. Felhasználható az Azure Storage diagnosztikai naplóinak a naplók "Request-ID-header" mezővel való összekapcsolására, és a rendszer az "x-MS-Request-id" fejlécben az API-hívás kezdeményezését adja vissza. Lásd: [naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | sztring | Az az érték, amelyet a műveletek feltételes végrehajtásához használhat. |
| contentType | sztring | A blobhoz megadott tartalomtípus. |
| contentLength | egész szám | A blob mérete bájtban megadva. |
| BlobType | sztring | A blob típusa. Az érvényes értékek: "BlockBlob" vagy "PageBlob". |
| url | sztring | A blob elérési útja. <br>Ha az ügyfél blobot REST API használ, akkor az URL-cím a következő struktúrát használja: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Ha az ügyfél egy Data Lake Storage-REST API használ, akkor az URL-cím a következő struktúrával rendelkezik: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |


## <a name="next-steps"></a>Következő lépések

Tekintse meg az alábbi cikkeket a Blob Storage dokumentációjában:

- [Blob Storage események szűrése](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Ajánlott eljárások Blob Storage események fogyasztásához](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Ebben az oktatóanyagban a Blobok Azure Blob Storageban történő létrehozásával vagy törlésével közzétette az eseményeket. Tekintse meg a többi oktatóanyagot, amelyből megtudhatja, hogyan továbbíthatja az eseményeket a felhőbe (Azure Event hub vagy Azure IoT Hub): 

- [Események továbbítása a Azure Event Gridba](forward-events-event-grid-cloud.md)
- [Események továbbítása az Azure IoT Hubba](forward-events-iothub.md)