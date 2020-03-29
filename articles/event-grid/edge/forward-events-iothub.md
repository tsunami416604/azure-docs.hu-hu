---
title: Eseményrács eseményeinek továbbítása az IoTHubra – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Eseményrács-események továbbítása az IoTHubra
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844700"
---
# <a name="tutorial-forward-events-to-iothub"></a>Oktatóanyag: Események továbbítása az IoTHubra

Ez a cikk végigvezeti az Event Grid-események továbbításához szükséges összes lépést más IoT Edge-modulok, az IoTHub útvonalak használatával. Ezt a következő okok miatt teheti meg:

* Továbbra is használja a már meglévő befektetéseket az edgeHub útválasztásával
* Az összes eseményt csak az IoT Hubon keresztül szeretné irányítani egy eszközről

Az oktatóanyag befejezéséhez meg kell értenie a következő fogalmakat:

- [Eseményrács – fogalmak](concepts.md)
- [IoT edge hub](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag befejezéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) ha még nem rendelkezik ilyen. 
* **Azure IoT Hub és IoT Edge-eszköz** – Kövesse a lépéseket a gyors indítás [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows eszközök,](../../iot-edge/quickstart.md) ha még nem rendelkezik ilyen.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Témakör létrehozása

Egy esemény közzétevőjeként létre kell hoznia egy eseményrács-témakört. A témakör egy végpontra hivatkozik, ahol a közzétevők eseményeket küldhetnek.

1. Hozzon létre topic4.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. A témakör létrehozásához futtassa a következő parancsot. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Futtassa a következő parancsot a témakör sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témában közzétett eseményekre. Ahhoz, hogy bármilyen eseményt megkapzon, létre kell hoznia egy Eseményrács-előfizetést egy érdeklődésre számot tartó témában.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription4.json az alábbi tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > A `endpointType` megadott, hogy `edgeHub`az előfizető . A `outputName` adja meg a kimenetet, amelyen az Event Grid modul útvonal események, amelyek megfelelnek az előfizetés edgeHub. Például a fenti előfizetésnek megfelelő események `/messages/modules/eventgridmodule/outputs/sampleSub4`a alkalmazásba kerül.
2. Futtassa a következő parancsot az előfizetés létrehozásához. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Futtassa a következő parancsot az előfizetés sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Szegélyelosztó útvonal beállítása

Frissítse a peremhálózati központ útvonalát az esemény-előfizetés eseményeinek továbbításához az IoTHubra az alábbiak szerint:

1. Bejelentkezés az [Azure Portalra](https://ms.portal.azure.com)
1. Nyissa meg az **IoT-központot.**
1. Válassza ki az **IoT Edge parancsot** a menüből
1. Válassza ki a céleszköz azonosítóját az eszközök listájából.
1. Válassza **a Modulok beállítása**lehetőséget.
1. Válassza a **Tovább** gombot és az útvonalak szakaszt.
1. Az útvonalakon adjon hozzá egy új útvonalat

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Például:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > A fenti útvonal továbbítja az ehhez az előfizetéshez az IoT hubra való továbbításhoz egyeztetett eseményeket. Az Edge [hub útválasztási](../../iot-edge/module-composition.md) szolgáltatásaival tovább szűrheti és továbbíthatja az Event Grid-eseményeket más IoT Edge-modulokhoz.

## <a name="setup-iot-hub-route"></a>Az IoT Hub-útvonal beállítása

Tekintse meg az [IoT Hub útválasztási oktatóanyag](../../iot-hub/tutorial-routing.md) egy útvonalat az IoT hubról, hogy az Event Grid modulból továbbított események megtekintéséhez. Használja `true` a lekérdezést, hogy az oktatóanyag egyszerű legyen.  



## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event4.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Az esemény közzétételéhez futtassa a következő parancsot:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Az esemény kézbesítésének ellenőrzése

Tekintse meg az IoT Hub [útválasztási oktatóanyag](../../iot-hub/tutorial-routing.md) az események megtekintéséhez szükséges lépéseket.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* Futtassa a következő parancsot a témakör és az összes előfizetésének törléséhez a peremhálózaton:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Törölje az IoTHub-útválasztás felhőben való beállítása közben létrehozott erőforrásokat is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy eseményrács-témakört, a peremhálózati hub-előfizetést és a közzétett eseményeket. Most, hogy már ismeri a peremhálózati hubra való előrelépés alapvető lépéseit, tekintse meg az alábbi cikkeket:

* Az Azure Event Grid IoT Edge-en való használatával kapcsolatos problémák elhárításáról a [Hibaelhárítási útmutató](troubleshoot.md)című témakörben talál.
* [Peremhálózati](../../iot-edge/module-composition.md) hubútvonal-szűrők használata események particionálásához
* Az Event Grid modul perzisztenciájának beállítása [linuxon](persist-state-linux.md) vagy [Windowsrendszeren](persist-state-windows.md)
* Az ügyfélhitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
* Események továbbítása az Azure Event Grid be a felhőben az [oktatóanyag](forward-events-event-grid-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózaton](monitor-topics-subscriptions.md)