---
title: Közzététel, feliratkozás eseményekre helyileg - Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Közzététel, feliratkozás helyi eseményekre a Webhook segítségével az IoT Edge Eseményrácshasználatával
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281001"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Oktatóanyag: Közzététel, feliratkozás helyi eseményekre

Ez a cikk végigvezeti az események közzétételéhez és az Eseményekre való feliratkozáshoz szükséges összes lépésen az IoT Edge-en.

> [!NOTE]
> Az Azure Event Grid témaköreiről és előfizetéseiről az [Event Grid fogalmai című témakörben olvashat bővebben.](concepts.md)

## <a name="prerequisites"></a>Előfeltételek 
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

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
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
    > Ebben az oktatóanyagban az Event Grid modult úgy telepíti, hogy az ügyfélhitelesítés le van tiltva. Éles számítási feladatok esetén azt javasoljuk, hogy engedélyezze az ügyfél hitelesítését. Az Event Grid modul biztonságos konfigurálásáról a [Biztonság és hitelesítés](security-authentication.md)című témakörben talál további információt.
    > 
    > Ha egy Azure virtuális gép peremhálózati eszközként, adjon hozzá egy bejövő port szabály, amely lehetővé teszi a bejövő forgalmat a 4438-as porton. A szabály hozzáadásáról a [Portok megnyitása virtuális géphez](../../virtual-machines/windows/nsg-quickstart-portal.md)című témakörben talál útmutatást.
    

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
1. Kattintson a **Tovább** gombra az útvonalak szakasz folytatásához

 ### <a name="setup-routes"></a>Útvonalak beállítása

Az alapértelmezett útvonalak megtartása, majd a Tovább gombra a véleményezési szakasz folytatásához válassza a **Tovább** gombot.

### <a name="submit-the-deployment-request"></a>A központi telepítési kérelem elküldése

1. A véleményezési szakasz a JSON telepítési jegyzékfájlját jeleníti meg, amely az előző szakaszban megadott beállítások alapján jött létre. Győződjön meg arról, hogy mindkét modul: **eventgridmodule** és **előfizető** szerepel a JSON. 
1. Tekintse át a központi telepítési adatokat, majd válassza a **Küldés lehetőséget.** A központi telepítés elküldése után **device** visszatér az eszközlapra.
1. A **Modulok szakaszban**ellenőrizze, hogy mind az **eventgrid,** mind **az előfizetői** modulok szerepelnek-e a listában. Ellenőrizze, hogy a **Telepítésben megadott** és az eszköz által **jelentett** oszlopok **értéke Igen.**

    Eltarthat néhány percig, amíg a modul elindul az eszközön, és majd jelenteni kell az IoT Hubnak. Frissítse a lapot a frissített állapot megtekintéséhez.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

Egy esemény közzétevőjeként létre kell hoznia egy eseményrács-témakört. Az Azure Event Gridben egy témakör egy végpontra hivatkozik, ahol a közzétevők eseményeket küldhetnek.

1. Hozzon létre topic.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Eseményrács-témakör létrehozásához futtassa a következő parancsot. Ellenőrizze, hogy látja-e, `200 OK`hogy a HTTP-állapotkód a.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Futtassa a következő parancsot a témakör sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témában közzétett eseményekre. Ahhoz, hogy bármilyen eseményt megkapjon, létre kell hoznia egy Event Grid-előfizetést egy érdeklődésre számot tartó témakörhöz.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [AZ API dokumentációját](api.md)

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
    > Az **endpointType** tulajdonság azt adja meg, hogy az előfizető **Webhook**.  A **endpointUrl** megadja azt az URL-címet, amelyen az előfizető figyeli az eseményeket. Ez az URL-cím a korábban üzembe helyezett Azure-előfizetői mintának felel meg.
2. Futtassa a következő parancsot a témakör előfizetésének létrehozásához. Ellenőrizze, hogy látja-e, `200 OK`hogy a HTTP-állapotkód a.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Futtassa a következő parancsot az előfizetés sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Esemény közzétételéhez futtassa a következő parancsot.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Az esemény kézbesítésének ellenőrzése

1. SSH vagy RDP az IoT Edge virtuális gépbe.
1. Ellenőrizze az előfizetői naplókat:

    Windows rendszerben futtassa a következő parancsot:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Linux on futtassa a következő parancsot:

    ```sh
    sudo docker logs subscriber
    ```

    Példa a kimenetre:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetéstörléséhez.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Törölje az előfizetői modult az IoT Edge-eszközről.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy eseményrács-témakört, előfizetést és közzétett eseményeket. Most, hogy már ismeri az alapvető lépéseket, olvassa el az alábbi cikkeket: 

- Az Azure Event Grid IoT Edge-en való használatával kapcsolatos problémák elhárításáról a [Hibaelhárítási útmutató](troubleshoot.md)című témakörben talál.
- Előfizetés létrehozása/frissítése [szűrőkkel.](advanced-filtering.md)
- Az Event Grid modul tartalommegőrzésének engedélyezése [Linuxvagy](persist-state-linux.md) [Windows rendszeren](persist-state-windows.md)
- Az ügyfélhitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
- Események továbbítása az Azure Functions a felhőben az [oktatóanyag](pub-sub-events-webhook-cloud.md) követésével
- [Reagálás a Blob Storage-eseményekre az IoT Edge-en](react-blob-storage-events-locally.md)
- [Témakörök és előfizetések figyelése a peremhálózaton](monitor-topics-subscriptions.md)

