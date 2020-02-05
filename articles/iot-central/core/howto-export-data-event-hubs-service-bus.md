---
title: Exportálja adatait az Azure Event Hubsba és Azure Service Busba | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Event Hubs és Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 84539c4cd5e4e1712c28ab70e9afc259e8d6d04b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990062"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportálja adatait az Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az adatai saját **Azure-Event Hubsba**és **Azure Service Bus** példányokra való exportálására. A saját céljára exportálhatja a **méréseket**, az **eszközöket** **és az eszközöket, így a** meleg elérési utat és az elemzést is elvégezheti. Ez magában foglalja az egyéni szabályok beindítását a Azure Stream Analyticsban, az egyéni munkafolyamatok aktiválását Azure Logic Appsekben, illetve az adatátalakítást és a Azure Functionson keresztüli átadását. 

> [!Note]
> Ha ismét bekapcsolja a folyamatos adatexportálást, az adott pillanattól kezdve csak az adott adatot kapja meg. Jelenleg nem lehet lekérni az adatgyűjtési időt, amikor a folyamatos adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a folyamatos adatexportálást.


## <a name="prerequisites"></a>Előfeltételek

- A IoT Central-alkalmazásban rendszergazdának kell lennie

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Ha nem rendelkezik meglévő Event Hubs/Service Bus az exportáláshoz, a következő lépésekkel hozhat létre egyet:

### <a name="create-event-hubs-namespace"></a>Event Hubs névtér létrehozása

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)olvashat.
2. Válasszon egy előfizetést. 

    > [!Note] 
    > Mostantól exportálhat más előfizetésekre is, amelyek nem egyeznek meg a standard IoT Central alkalmazásával. Ebben az esetben kapcsolati sztringet fog használni.
3. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

### <a name="create-service-bus-namespace"></a>Service Bus névtér létrehozása

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . [Azure Service Bus dokumentációban](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)bővebben is olvashat.
2. Válasszon egy előfizetést. 

    > [!Note] 
    > Mostantól exportálhat más előfizetésekre is, amelyek nem egyeznek meg a standard IoT Central alkalmazásával. Ebben az esetben kapcsolati sztringet fog használni.

3. Nyissa meg a Service Bus névteret, és a felül található **+ üzenetsor** vagy **+ témakör** használatával hozzon létre egy üzenetsor vagy témakört az exportáláshoz.


## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy rendelkezik egy Event Hubs/Service Bus céllal, hogy exportálja az adatexportálást, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához. 

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali ablaktáblán válassza a **folyamatos adatexportálás**lehetőséget.

    > [!Note]
    > Ha nem látja a folyamatos adatexportálást a bal oldali ablaktáblán, Ön nem rendszergazda az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válasszon ki egy **Azure-Event Hubs** vagy **Azure Service Bus** az Exportálás célhelye. 

    > [!NOTE] 
    > Az alkalmazások exportálásának maximális száma öt. 

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data/export-new2.png)

4. A legördülő listában válassza ki a **Event Hubs névtér/Service Bus névteret**. A lista utolsó elemét is kiválaszthatja, amely a **kapcsolatok karakterláncát adja meg**. 

    > [!NOTE] 
    > A Storage-fiókok/Event Hubs névterek/Service Bus névterek a **IoT Central alkalmazással megegyező előfizetésben**jelennek meg. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.

    > [!NOTE] 
    > A 7 napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. Ennek az az oka, hogy a 7 napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új CDE-esemény hub létrehozása](media/howto-export-data/export-eh.png)

5. Választható Ha a **kapcsolódási karakterlánc megadása**lehetőséget választotta, a rendszer egy új mezőt jelenít meg a kapcsolódási karakterlánc beillesztéséhez. A következőhöz tartozó kapcsolódási karakterlánc lekérése:
    - Event Hubs vagy Service Bus, lépjen a Azure Portal névtér elemére.
        - A **Beállítások**területen válassza a **megosztott elérési szabályzatok** elemet.
        - Válassza ki az alapértelmezett **RootManageSharedAccessKey** , vagy hozzon létre egy újat
        - Az elsődleges vagy a másodlagos kapcsolatok karakterláncának másolása
 
6. Válassza ki az Event hub/üzenetsor vagy a témakört a legördülő listából.

7. Az **exportálni kívánt adat**területen adja meg az exportálandó adattípusokat **, ha a**típust be értékre állítja.

8. A folyamatos adatexportálás bekapcsolásához győződjön meg arról, hogy az **adatexportálási** váltógomb be van **kapcsolva**. Kattintson a **Mentés** gombra.

9. Néhány perc elteltével az adatai megjelennek a választott célhelyen.


## <a name="data-format"></a>Adatformátum

A mérések, az eszközök és az eszközök sablonjainak adatai exportálva lesznek az Event hub-ba, vagy Service Bus üzenetsor vagy témakör a közel valós időben. Az exportált mérési adatok teljes egészében tartalmazzák az eszközök által IoT Central küldött üzenetet, nem csak a mérések értékeit. Az exportált eszközök az összes eszköz tulajdonságainak és beállításainak módosításait tartalmazzák, az exportált sablonok pedig az összes eszközosztály változásait tartalmazzák. Az exportált érték a "Body" tulajdonságban van, és JSON formátumú.

> [!NOTE]
> Service Bus exportálási célhelyként való kiválasztásakor a várólisták és a témakörök **nem rendelkezhetnek a munkamenetek és az ismétlődő észlelések engedélyezésével**. Ha ezek bármelyike engedélyezve van, néhány üzenet nem érkezik meg a várólistán vagy a témakörben.

### <a name="measurements"></a>Mérések

A rendszer gyorsan exportál egy új üzenetet, miután IoT Central fogadja az üzenetet az eszközről. Event Hubs és Service Bus összes exportált üzenete teljes üzenetet tartalmaz, amelyet az eszköz JSON formátumban küldött a "Body" tulajdonságban. 

> [!NOTE]
> A méréseket küldő eszközöket az eszközök azonosítói jelölik (lásd a következő részeket). Az eszközök nevének beszerzéséhez exportálja az eszközöket, és korrelálja az egyes messsage a **connectionDeviceId** , amely megfelel az eszközhöz tartozó üzenet **deviceId** értékének.

Az alábbi példa egy üzenetet jelenít meg az Event hub-ban vagy Service Bus-várólistában vagy-témakörben fogadott mérési adatmennyiségekről.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-02T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Eszközök

Az eszköz adatait tartalmazó üzeneteket a rendszer néhány percenként egyszer elküldi az Event hub-nak vagy Service Bus üzenetsor vagy témakörnek. Ez azt jelenti, hogy minden percben egy köteg üzenet érkezik a
- Új, hozzáadott eszközök
- Módosított tulajdonsággal rendelkező és beállított értékeket tartalmazó eszközök

Minden üzenet az eszköz egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az egyes üzenetekben küldendő információk a következők:
- az eszköz `id` IoT Central
- az eszköz `name`
- `deviceId` a [Device kiépítési szolgáltatásból](/azure/iot-central/core/howto-connect-nodejs)
- Eszköz sablonjának adatai
- Tulajdonságértékek
- Értékek beállítása

> [!NOTE]
> Az utolsó köteg óta törölt eszközök nem lesznek exportálva. Jelenleg nincsenek mutatók a törölt eszközök exportált üzeneteiben.
>
> Az eszközök sablonja, amelyhez az egyes eszközök tartoznak, egy eszköz-sablon azonosítója jelöli. Az eszköz sablonjának beolvasásához ügyeljen arra, hogy az eszköz sablonjának adatmennyiségét is exportálja.

Az alábbi példa egy üzenetet jelenít meg az Event hub-ban vagy Service Bus üzenetsor vagy témakörben található eszköz adatainak:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-02T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Eszközök sablonjai

A rendszer néhány percenként egyszer elküldi az eszköz-sablonok adatait tartalmazó üzeneteket az Event hub-nak vagy Service Bus üzenetsor vagy témakör számára. Ez azt jelenti, hogy minden percben egy köteg üzenet érkezik a
- Új, hozzáadott eszköz-sablonok
- Megváltoztatott mértékegységekkel, tulajdonsággal és beállítási definíciókkal rendelkező eszközök sablonjai

Minden üzenet a legutóbbi exportált üzenet óta egy vagy több módosítást jelképez az eszközön. Az egyes üzenetekben küldendő információk a következők:
- az eszköz sablonjának `id`
- az eszköz sablonjának `name`
- az eszköz sablonjának `version`
- Mérési adattípusok és minimális/maximális értékek
- Tulajdonság adattípusai és alapértelmezett értékei
- Az adattípusok és az alapértelmezett értékek beállítása

> [!NOTE]
> Az eszköz sablonjai törölve lettek az utolsó köteg exportálása óta. Jelenleg nincsenek mutatók a törölt eszközök sablonjaihoz tartozó exportált üzenetekben.

Az alábbi példa egy üzenetet jelenít meg az Event hub vagy Service Bus üzenetsor vagy témakör eszköz sablonjaival kapcsolatos adatainak:

```json
{ 
  "body":{ 
    "id":"<id>",
    "version":"1.0.0",
    "name":"<templateName>",
    "measurements":{ 
      "telemetry":{ 
        "humidity":{ 
          "dataType":"double",
          "name":"humidity"
        },
        "pressure":{ 
          "dataType":"double",
          "name":"pressure"
        },
        "temp":{ 
          "dataType":"double",
          "name":"temperature"
        }
      }
    },
    "properties":{ 
      "cloud":{ 
        "location":{ 
          "dataType":"string",
          "name":"Location"
        }
      },
      "device":{ 
        "dieNumber":{ 
          "dataType":"double",
          "name":"Die Number"
        }
      }
    },
    "settings":{ 
      "device":{ 
        "fanSpeed":{ 
          "dataType":"double",
          "name":"Fan Speed",
          "initialValue":0
        }
      }
    }
  },
  "annotations":{ 
    "iotcentral-message-source":"deviceTemplates",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":25315,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274985085
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":25315,
  "enqueuedTimeUtc":"2018-10-02T16:23:05.085Z",
  "offset":"<offset>"
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan exportálhatja adatait az Azure Event Hubsba és Azure Service Busba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [A Azure Functions elindítása](howto-trigger-azure-functions.md)
