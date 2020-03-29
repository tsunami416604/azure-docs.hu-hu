---
title: Közzététel, feliratkozás eseményekre a felhőben - Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Közzététel, feliratkozás a felhőbeli eseményekre a Webhook segítségével az IoT Edge Event Grid jével
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844589"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Oktatóanyag: Közzététel, feliratkozás eseményekre a felhőben

Ez a cikk végigvezeti az események közzétételéhez és az Eseményekre való feliratkozáshoz szükséges összes lépést az IoT Edge-en. Ez az oktatóanyag használja, és az Azure-függvény, mint az Eseménykezelő. További céltípusokról [az eseménykezelők](event-handlers.md).

A folytatás előtt tekintse meg [az Eseményrács-fogalmak című](concepts.md) témakört.

## <a name="prerequisites"></a>Előfeltételek 
Az oktatóanyag befejezéséhez a következőkre lesz szüksége:

* **Azure-előfizetés** – Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) ha még nem rendelkezik ilyen. 
* **Azure IoT Hub és IoT Edge-eszköz** – Kövesse a lépéseket a gyors indítás [Linux](../../iot-edge/quickstart-linux.md) vagy [Windows eszközök,](../../iot-edge/quickstart.md) ha még nem rendelkezik ilyen.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure-függvény létrehozása az Azure Portalon

Kövesse az [oktatóanyagban](../../azure-functions/functions-create-first-azure-function.md) ismertetett lépéseket egy Azure-függvény létrehozásához. 

Cserélje le a kódrészletet a következő kódra:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Az új függvényben válassza a **Függvény URL-jének beírása** a jobb felső sarokban lehetőséget, válassza az alapértelmezett (**Funkcióbillentyű**) lehetőséget, majd a **Másolás**lehetőséget. Az oktatóanyag későbbi részében a függvény URL-értékét fogja használni.

> [!NOTE]
> Tekintse meg az [Azure Functions](../../azure-functions/functions-overview.md) dokumentációjában további minták és oktatóanyagok az Eseményekre való reagálás egy EventGrid eseményindítók használatával.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

Egy esemény közzétevőjeként létre kell hoznia egy eseményrács-témakört. A témakör egy végpontra hivatkozik, ahová a közzétevők eseményeket küldhetnek.

1. Hozzon létre topic2.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. A témakör létrehozásához futtassa a következő parancsot. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Futtassa a következő parancsot a témakör sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Példa a kimenetre:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Esemény-előfizetés létrehozása

Az előfizetők regisztrálhatnak a témában közzétett eseményekre. Ahhoz, hogy bármilyen eseményt megkapna, az előfizetőknek létre kell hozniuk egy Eseményrács-előfizetést egy érdekes témában.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Hozzon létre subscription2.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > Az **endpointType** azt adja meg, hogy az előfizető webhook.  A **endpointUrl** megadja azt az URL-címet, amelyen az előfizető figyeli az eseményeket. Ez az URL-cím a korábban beállított Azure-függvénymintának felel meg.
2. Futtassa a következő parancsot az előfizetés létrehozásához. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Futtassa a következő parancsot az előfizetés sikeres létrehozásának ellenőrzéséhez. A 200 OK HTTP-állapotkódot vissza kell adni.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Példa a kimenetre:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Esemény közzététele

1. Hozzon létre event2.json a következő tartalommal. A hasznos adattal kapcsolatos részletekért tekintse meg [az API dokumentációját.](api.md)

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Az esemény közzétételéhez futtassa a következő parancsot

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Az esemény kézbesítésének ellenőrzése

Megtekintheti az Azure Portalon szállított esemény a **funkció figyelése** lehetőség alatt.

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

* A témakör és az összes előfizetéstörléséhez futtassa a következő parancsot

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Törölje az Azure Portalon létrehozott Azure-függvényt.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy eseményrács-témakört, előfizetést és közzétett eseményeket. Most, hogy már ismeri az alapvető lépéseket, olvassa el az alábbi cikkeket:

* Az Azure Event Grid IoT Edge-en való használatával kapcsolatos problémák elhárításáról a [Hibaelhárítási útmutató](troubleshoot.md)című témakörben talál.
* Előfizetés létrehozása/frissítése [szűrőkkel.](advanced-filtering.md)
* Az Event Grid modul perzisztenciájának beállítása [linuxon](persist-state-linux.md) vagy [Windowsrendszeren](persist-state-windows.md)
* Az ügyfélhitelesítés konfigurálásához kövesse a [dokumentációt](configure-client-auth.md)
* Események továbbítása az Azure Event Grid be a felhőben az [oktatóanyag](forward-events-event-grid-cloud.md) követésével
* [Témakörök és előfizetések figyelése a peremhálózaton](monitor-topics-subscriptions.md)
