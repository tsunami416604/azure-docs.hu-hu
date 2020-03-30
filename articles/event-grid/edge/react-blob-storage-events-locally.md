---
title: Reagálás a Blob Storage modul eseményeire – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Reagálás a Blob Storage modul eseményeire
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086603"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Oktatóanyag: Reagálás a Blob Storage-eseményekre az IoT Edge-en (előzetes verzió)
Ez a cikk bemutatja, hogyan telepítheti az Azure Blob Storage-t az IoT-modulon, amely eseményrács-közzétevőként működne a Blob létrehozása és a Blob-törlés eseményrácsra történő küldéséhez.  

Az Azure Blob Storage IoT Edge-en című témakörben olvashat az [IoT Edge-en](../../iot-edge/how-to-store-data-blob.md) és annak funkcióiról.

> [!WARNING]
> Az Azure Blob Storage az IoT Edge-en az Event Griddel való integráció előzetes verzióban érhető el

Az oktatóanyag befejezéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) ha még nem rendelkezik ilyen. 
* **Azure IoT Hub és IoT Edge-eszköz** – Kövesse a [linuxos](../../iot-edge/quickstart-linux.md) vagy [Windows-eszközökhöz](../../iot-edge/quickstart.md) való rövid útmutató lépéseit, ha még nem rendelkezik ilyentel.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge modul telepítése

A modulok at ioT Edge-eszközökre többféleképpen is üzembe helyezheti, és mindegyik az Azure Event Grid ben működik az IoT Edge-en. Ez a cikk ismerteti az Event Grid az Azure Portalon az IoT Edge üzembe helyezésének lépéseit.

>[!NOTE]
> Ebben az oktatóanyagban az Event Grid modult adatmegőrzés nélkül telepíti. Ez azt jelenti, hogy az ebben az oktatóanyagban létrehozott témakörök és előfizetések törlődnek a modul újratelepítésekor. Az adatmegőrzés beállításáról a következő cikkekben talál további információt: [Állapot megőrzése Linux alatt](persist-state-linux.md) vagy [Perzisztencia állapot a Windows rendszerben.](persist-state-windows.md) Éles számítási feladatok esetén azt javasoljuk, hogy az Event Grid modult adatmegőrzéssel telepítse.


### <a name="select-your-iot-edge-device"></a>Válassza ki az IoT Edge-eszközt

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. Keresse meg az IoT Hubot.
1. Válassza az **IoT Edge elemet** az **Automatikus eszközkezelés** szakasz menüjéből. 
1. Kattintson a céleszköz azonosítójára az eszközök listájából
1. Válassza **a Modulok beállítása**lehetőséget. Tartsa nyitva az oldalt. A következő szakaszban ismertetett lépésekkel folytatja.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. Az Azure Portalon van egy varázsló, amely végigvezeti a központi telepítési jegyzékfájl létrehozásán, ahelyett, hogy a JSON-dokumentum manuálislétrehozása.  Három lépésből áll: **Modulok hozzáadása**, **Útvonalak megadása**és A **telepítés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **Központi telepítési modulok csoportban** válassza **a Hozzáadás**
1. A legördülő listában szereplő modulok típusai közül válassza az **IoT Edge Module**
1. Adja meg a tároló nevét, lemezképét és tárolójának létrehozási beállításait:

   * **Név**: eventgridmodule
   * **Kép URI**-`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Tároló létrehozási beállításai:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
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

 1. Kattintson a **Mentés gombra**
 1. Folytassa a következő szakaszt az Azure Event Grid-előfizetői modul hozzáadásához, mielőtt együtt üzembe helyezne őket.

    >[!IMPORTANT]
    > Ebben az oktatóanyagban megtudhatja, hogy az Event Grid modul mindkét HTTP/HTTPs-kérelem engedélyezéséhez engedélyezze az ügyfélhitelesítésletiltva. Éles számítási feladatok esetén azt javasoljuk, hogy csak https-kérelmeket és az ügyfelek hitelesítése engedélyezve rendelkező előfizetők engedélyezése. Az Event Grid modul biztonságos konfigurálásáról a [Biztonság és hitelesítés](security-authentication.md)című témakörben talál további információt.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid-előfizető IoT Edge moduljának telepítése

Ez a szakasz bemutatja, hogyan telepíthet egy másik IoT-modult, amely eseménykezelőként működne, amelyhez események et lehet szállítani.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **Telepítési modulok csoportban** válassza a **Hozzáadás** újra lehetőséget. 
1. A legördülő listában szereplő modulok típusai közül válassza az **IoT Edge Module**
1. Adja meg a tároló nevét, lemezképét és tárolójának létrehozási beállításait:

   * **Név**: előfizető
   * **Kép URI**-`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Tároló létrehozási beállításai:** Nincs
1. Kattintson a **Mentés gombra**
1. Folytassa a következő szakaszsal az Azure Blob Storage modul hozzáadásához

## <a name="deploy-azure-blob-storage-module"></a>Az Azure Blob Storage modul telepítése

Ez a szakasz bemutatja, hogyan telepítheti az Azure Blob Storage modult, amely egy Event Grid-közzétevőként működik, és közzétesz blobot, és törölt eseményeket.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **Központi telepítési modulok csoportban** válassza **a Hozzáadás**
2. A legördülő listában szereplő modulok típusai közül válassza az **IoT Edge Module**
3. Adja meg a tároló nevét, lemezképét és tárolójának létrehozási beállításait:

   * **Név**: azureblobstorageoniotedge
   * **Kép URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Tároló létrehozási beállításai:**

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

   > [!IMPORTANT]
   > - A Blob Storage modul https és HTTP használatával is közzétehet eseményeket. 
   > - Ha engedélyezte az EventGrid ügyfélalapú hitelesítését, frissítse a EVENTGRID_ENDPOINT értékét a https `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`engedélyezéséhez, például: .
   > - Is adjon hozzá `AllowUnknownCertificateAuthority=true` egy másik környezeti változóak a fenti Json. Ha HTTPS-kapcsolaton keresztül beszél az EventGriddel, az **AllowUnknownCertificateAuthority** lehetővé teszi, hogy a tárolómodul megbízzon az önaláírt EventGrid-kiszolgálói tanúsítványokban.

4. Frissítse a másolt JSON-t a következő információkkal:

   - Cserélje `<your storage account name>` le egy nevet, hogy emlékszel. A fióknevek nek 3 és 24 karakter között kell lenniük, kisbetűkkel és számokkal. Nincsenek szóközök.

   - Cserélje `<your storage account key>` le egy 64 bájtos alap64 kulcsra. Hozhat létre egy kulcsot eszközökkel, mint [generateplus](https://generate.plus/en/base64?gp_base64_base[length]=64). Ezeket a hitelesítő adatokat fogja használni a blobstorage eléréséhez más modulokból.

   - Cserélje `<event grid module name>` le az Event Grid modul nevére.
   - Cserélje `<storage mount>` ki a tároló operációs rendszerének megfelelően.
     - Linux-tárolók esetén a **my-volume:/blobroot**
     - Windows-tárolók esetén saját**kötet:C:/BlobRoot**

5. Kattintson a **Mentés gombra**
6. Kattintson a **Tovább** gombra az útvonalak szakasz folytatásához

    > [!NOTE]
    > Ha egy Azure virtuális gép a peremhálózati eszköz, adjon hozzá egy bejövő port szabály, amely lehetővé teszi a bejövő forgalmat a gazdagép portok az ebben az oktatóanyagban használt: 4438, 5888, 8080 és 11002. A szabály hozzáadásáról a [Portok megnyitása virtuális géphez](../../virtual-machines/windows/nsg-quickstart-portal.md)című témakörben talál útmutatást.

### <a name="setup-routes"></a>Útvonalak beállítása

Az alapértelmezett útvonalak megtartása, majd a Tovább gombra a véleményezési szakasz folytatásához válassza a **Tovább** gombot.

### <a name="review-deployment"></a>Telepítés áttekintése

1. A véleményezési szakasz a JSON telepítési jegyzékfájlját jeleníti meg, amely az előző szakaszban megadott beállítások alapján jött létre. Győződjön meg arról, hogy a következő négy modul jelenik meg: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **előfizető** és **azureblobstorageoniotedge,** hogy minden üzembe helyezett.
2. Tekintse át a központi telepítési adatokat, majd válassza a **Küldés lehetőséget.**

## <a name="verify-your-deployment"></a>A telepítés ellenőrzése

1. Miután elküldte a központi telepítést, visszatér az IoT-központ IoT Peremhálózati lapjára.
2. Válassza ki az **IoT Edge-eszközt,** amelyet a központi telepítéssel célzott meg a részletek megnyitásához.
3. Az eszköz részleteiben ellenőrizze, hogy az eventgridmodule, az előfizető és az azureblobstorageoniotedge modulok a **központi telepítésben megadott** és **az eszköz által jelentett**ként vannak-e felsorolva.

   Eltarthat néhány percig, amíg a modul elindul az eszközön, és majd jelenteni kell az IoT Hubnak. Frissítse a lapot a frissített állapot megtekintéséhez.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated és BlobDeleted események közzététele

1. Ez a modul automatikusan létrehozza a **témakör MicrosoftStorage**. Annak ellenőrzése, hogy létezik-e
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

    > [!IMPORTANT]
    > - A HTTPS-folyamat, ha az ügyfél-hitelesítés sas-kulccsal keresztül engedélyezve van, majd a korábban megadott SAS-kulcsot fejlécként kell hozzáadni. Ezért a göndör kérés lesz:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - A HTTPS-folyamat esetében, ha az ügyfélhitelesítés tanúsítványon keresztül engedélyezve van, a curl kérelem a következő lesz:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Az előfizetők regisztrálhatnak a témában közzétett eseményekre. Ahhoz, hogy bármilyen eseményt megkapjon, létre **MicrosoftStorage** kell hoznia egy Event Grid-előfizetést a MicrosoftStorage-témakörhöz.
    1. Hozzon létre blobsubscription.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [AZ API dokumentációját](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Az **endpointType** tulajdonság azt adja meg, hogy az előfizető **Webhook**.  A **endpointUrl** megadja azt az URL-címet, amelyen az előfizető figyeli az eseményeket. Ez az URL-cím a korábban üzembe helyezett Azure-függvénymintának felel meg.

    2. Futtassa a következő parancsot a témakör előfizetésének létrehozásához. Ellenőrizze, hogy látja-e, `200 OK`hogy a HTTP-állapotkód a.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - A HTTPS-folyamat, ha az ügyfél-hitelesítés sas-kulccsal keresztül engedélyezve van, majd a korábban megadott SAS-kulcsot fejlécként kell hozzáadni. Ezért a göndör kérés lesz:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - A HTTPS-folyamat esetében, ha az ügyfélhitelesítés tanúsítványon keresztül engedélyezve van, a curl kérelem a következő lesz:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Futtassa a következő parancsot az előfizetés sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

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
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - A HTTPS-folyamat, ha az ügyfél-hitelesítés sas-kulccsal keresztül engedélyezve van, majd a korábban megadott SAS-kulcsot fejlécként kell hozzáadni. Ezért a göndör kérés lesz:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - A HTTPS-folyamat esetében, ha az ügyfélhitelesítés tanúsítványon keresztül engedélyezve van, a curl kérelem a következő lesz:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Töltse le [az Azure Storage Explorert,](https://azure.microsoft.com/features/storage-explorer/) és [csatlakoztassa a helyi tárhelyhez](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Az esemény kézbesítésének ellenőrzése

### <a name="verify-blobcreated-event-delivery"></a>BlobCreated eseménykézbesítés ének ellenőrzése

1. Fájlok feltöltése blokkblobként az Azure Storage Explorer helyi tárolóba, és a modul automatikusan közzéteszi a létrehozási eseményeket. 
2. Tekintse meg az előfizetői naplók at hozzon létre esemény. Kövesse az [esemény megjelenítésének ellenőrzéséhez szükséges lépéseket.](pub-sub-events-webhook-local.md#verify-event-delivery)

    Minta kimenete:

    ```json
            Received Event:
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
    ```

### <a name="verify-blobdeleted-event-delivery"></a>BlobDeleted-esemény kézbesítésének ellenőrzése

1. Blobok törlése a helyi tárolóból az Azure Storage Explorer használatával, és a modul automatikusan közzéteszi a törlési eseményeket. 
2. Nézze meg az előfizetői naplók at delete esemény. Kövesse az [esemény megjelenítésének ellenőrzéséhez szükséges lépéseket.](pub-sub-events-webhook-local.md#verify-event-delivery)

    Minta kimenete:
    
    ```json
            Received Event:
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
    ```

Gratulálunk! Befejezte az oktatóanyagot. A következő szakaszok az esemény tulajdonságainak részleteit ismertetik.

### <a name="event-properties"></a>Esemény tulajdonságai

Az alábbiakban a támogatott eseménytulajdonságok listáját, valamint azok típusát és leírását tartalmazza. 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Blob tárolási esemény adatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| api-t | sztring | Az eseményt kiváltó művelet. Ez az alábbi értékek egyike lehet: <ul><li>BlobCreated - engedélyezett `PutBlob` értékek: és`PutBlockList`</li><li>BlobDeleted - engedélyezett `DeleteBlob` `DeleteAfterUpload` értékek `AutoDelete`a , és . <p>Az `DeleteAfterUpload` esemény akkor jön létre, amikor a blob automatikusan törlődik, mert deleteAfterUpload kívánt tulajdonság értéke igaz. </p><p>`AutoDelete`esemény jön létre, ha blob automatikusan törlődik, mert deleteAfterMinutes kívánt tulajdonság értéke lejárt.</p></li></ul>|
| clientRequestId | sztring | a storage API-művelet hez egy ügyfél által biztosított kérelemazonosító. Ez az azonosító használható az Azure Storage diagnosztikai naplók kal való korrelációhoz a naplók "ügyfél-kérelem-azonosító" mezőjének használatával, és az "x-ms-client-request-id" fejléc használatával biztosítható az ügyfélkérelmekben. További információt a [Formátum naplózása (Log Format) (Formátum naplója) témakörben talál.](/rest/api/storageservices/storage-analytics-log-format) |
| Kérelemazonosító | sztring | Szolgáltatás által létrehozott kérelem azonosítója a tárolási API-művelethez. Az Azure Storage diagnosztikai naplóival korrelálhat a naplók "request-id-header" mezőjével, és az "x-ms-request-id" fejlécben api-hívás kezdeményezéséből származik. Lásd: [Naplóformátum](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | sztring | A műveletek feltételes végrehajtásához használható érték. |
| contentType típusú | sztring | A blobhoz megadott tartalomtípus. |
| contentLength (contentLength) | egész szám | A blob mérete bájtban. |
| blobType típusú | sztring | A blob típusa. Az érvényes értékek a "BlockBlob" vagy a "PageBlob". |
| url | sztring | A blob elérési útja. <br>Ha az ügyfél Blob REST API-t használ, akkor az URL-cím a következő struktúrával rendelkezik: * \<storage-account-name\>\<.blob.core.windows.net/ tárolónév\>/\<fájlnév\>*. <br>Ha az ügyfél Data Lake Storage REST API-t használ, akkor az URL-cím a következő struktúrával rendelkezik: * \<\>storage-account-name\<.dfs.core.windows.net/ fájlrendszer-név\>/\<fájlnév\>*. |


## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket a Blob Storage dokumentációjában:

- [Blob Storage-események szűrése](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [A Blob Storage-események fogyasztásának ajánlott módszerei](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Ebben az oktatóanyagban közzétett események használatával blobok egy Azure Blob Storage létrehozása vagy törlése. Tekintse meg a többi oktatóanyagot, amelyből megtudhatja, hogyan továbbíthat eseményeket a felhőbe (Azure Event Hub vagy az Azure IoT Hub): 

- [Események továbbítása az Azure Event Gridbe](forward-events-event-grid-cloud.md)
- [Események továbbítása az Azure IoT Hubba](forward-events-iothub.md)
