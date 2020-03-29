---
title: Peremhálózati események továbbítása az Event Grid felhőbe – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Peremesemények továbbítása az Event Grid felhőbe
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844717"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Oktatóanyag: Események továbbítása az Event Grid felhőbe

Ez a cikk végigvezeti az összes olyan lépést, amely a peremhálózati események nek az Azure-felhőben az Event Gridbe való továbbításához szükséges. Ezt a következő okok miatt teheti meg:

* Reagáljon a felhőben lévő peremeseményekre.
* Események továbbítása az Event Grid a felhőben, és használja az Azure Event Hubs vagy az Azure Storage-várólisták puffer események feldolgozása előtt a felhőben.

 Az oktatóanyag befejezéséhez ismernie kell az Event Grid-fogalmakat [a peremhálózaton](concepts.md) és az [Azure-ban.](../concepts.md) További céltípusokról [az eseménykezelők](event-handlers.md). 

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag befejezéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) ha még nem rendelkezik ilyen. 
* **Azure IoT Hub és IoT Edge-eszköz** – Kövesse a lépéseket a gyors indítás [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows eszközök,](../../iot-edge/quickstart.md) ha még nem rendelkezik ilyen.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Eseményrács-témakör és -előfizetés létrehozása a felhőben

Hozzon létre egy eseményrács-témakört és előfizetést a felhőben [az oktatóanyag](../custom-event-quickstart-portal.md)követésével. Megjegyzés `topicURL`a `sasKey`, `topicName` és az oktatóanyag későbbi részében használt újonnan létrehozott témakörről.

Ha például az USA `testegcloudtopic` nyugati részén létrehozott egy témakört, az értékek a következőkre néznek:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Témakör neve**:`testegcloudtopic`
* **SasKey:** A témakör **AccessKey** alatt érhető el. Használja **a key1 gombot1.**

## <a name="create-event-grid-topic-at-the-edge"></a>Eseményrács-témakör létrehozása a peremhálózaton

1. Hozzon létre topic3.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. A témakör létrehozásához futtassa a következő parancsot. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Futtassa a következő parancsot a témakör sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Eseményrács-előfizetés létrehozása a peremhálózaton

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription3.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > A **endpointUrl** határozza meg, hogy az Event Grid témakör URL-címét a felhőben. A **sasKey** az Event Grid felhőtémakörének kulcsára hivatkozik. A **témakörnév** értéke az összes kimenő esemény eseményrácsba történő bélyegzőjéhez lesz használva. Ez akkor lehet hasznos, ha eseményrácsos tartománytémakörbe kerül. Az Event Grid tartományi témakörről további információt az [Eseménytartományok című témakörben talál.](../event-domains.md)

    Például:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Futtassa a következő parancsot az előfizetés létrehozásához. A 200 OK HTTP-állapotkódot vissza kell adni.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Futtassa a következő parancsot az előfizetés sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Esemény közzététele a peremhálózaton

1. Hozzon létre event3.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg az [API dokumentációját.](api.md)

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Futtassa az alábbi parancsot:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Peremhálózati esemény ellenőrzése a felhőben

A felhőtémakör által közvetített események megtekintéséről az [oktatóanyagban](../custom-event-quickstart-portal.md)olvashat.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* A témakör és az összes előfizetéstörléséhez futtassa a következő parancsot

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Törölje a felhőben létrehozott témaköröket és előfizetéseket (Azure Event Grid) is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban közzétett egy eseményt a szélén, és továbbította az Event Grid az Azure-felhőben. Most, hogy már ismeri az Event Grid felhőben való továbbtovábbításának alapvető lépéseit:

* Az Azure Event Grid IoT Edge-en való használatával kapcsolatos problémák elhárításáról a [Hibaelhárítási útmutató](troubleshoot.md)című témakörben talál.
* Események továbbítása az IoTHubra az [oktatóanyag](forward-events-iothub.md) követésével
* Események továbbítása a felhőben lévő Webhookba az [oktatóanyag](pub-sub-events-webhook-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózaton](monitor-topics-subscriptions.md)
