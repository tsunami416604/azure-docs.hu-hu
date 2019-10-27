---
title: Az Azure IoT Central-beli adatszolgáltatások exportálása | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Event Hubsba, Azure Service Busba és az Azure-ba Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: bc78f11d4f61c46e2ad4f7143fe8b3af6762b0b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950417"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Azure IoT Central-beli adatszolgáltatások exportálása (előzetes verzió)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az Azure Event Hubs, Azure Service Bus vagy Azure Blob Storage-példányokban tárolt adatai exportálásához. Az adatok JSON formátumúak, és tartalmazhatják a telemetria, az eszköz adatait és az eszköz sablonjának adatait. Az exportált adatértékek használata:

- Meleg elérésű elemzések és elemzések. Ez a beállítás magában foglalja az egyéni szabályok beindítását a Azure Stream Analyticsban, az egyéni munkafolyamatok aktiválását a Azure Logic Appsban, vagy átadja a Azure Functions át.
- A Microsoft Power BI-ban a ritka elérésű elemzések, például a Azure Machine Learning vagy a hosszú távú trendek elemzése során betanítási modellek.

> [!Note]
> Ha bekapcsolja a folyamatos adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adott adatot. Jelenleg nem lehet lekérni az adatgyűjtési időt, amikor a folyamatos adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a folyamatos adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

A IoT Central-alkalmazásban rendszergazdának kell lennie

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

A folyamatos adatexportálás konfigurálása előtt az Exportálás céljának léteznie kell.

### <a name="create-event-hubs-namespace"></a>Event Hubs névtér létrehozása

Ha nem rendelkezik meglévő Event Hubs-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](../../event-hubs/event-hubs-create.md)olvashat.

2. Válasszon egy előfizetést. Az olyan egyéb előfizetésekre is exportálhatja az adatait, amelyek nem ugyanabban az előfizetésben találhatók, mint az utólagos elszámolású IoT Central alkalmazás. Ebben az esetben kapcsolati sztringet használ.

3. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

### <a name="create-service-bus-namespace"></a>Service Bus névtér létrehozása

Ha nem rendelkezik meglévő Service Bus-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). [Azure Service Bus dokumentációban](../../service-bus-messaging/service-bus-create-namespace-portal.md)bővebben is olvashat.
2. Válasszon egy előfizetést. Az olyan egyéb előfizetésekre is exportálhatja az adatait, amelyek nem ugyanabban az előfizetésben találhatók, mint az utólagos elszámolású IoT Central alkalmazás. Ebben az esetben kapcsolati sztringet használ.

3. Nyissa meg a Service Bus névteret, és a felül található **+ üzenetsor** vagy **+ témakör** használatával hozzon létre egy üzenetsor vagy témakört az exportáláshoz.

Ha a Service Bus exportálás célhelyként választja, akkor a várólisták és a témakörök nem rendelkezhetnek a munkamenetek és az ismétlődő észlelések engedélyezésével. Ha ezek bármelyike engedélyezve van, néhány üzenet nem érkezik meg a várólistán vagy a témakörben.

### <a name="create-storage-account"></a>Storage-fiók létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) létrehozása vagy [Azure Data Lake Storage v2 Storage-fiókok](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Ha egy Azure Data Lake Storage v2 Storage-fiókba exportálja az adatexportálást, a **BlobStorage** kell választania **.**
    - Az egyes előfizetésekhez tartozó Storage-fiókokba exportálhatja az adatait, mint az utólagos elszámolású IoT Central alkalmazás esetében. Ebben az esetben kapcsolati sztringet fog használni.

2. Hozzon létre egy tárolót a Storage-fiókban. Nyissa meg a Storage-fiókját. A **blob szolgáltatás**alatt válassza a **Tallózás Blobok**lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy van egy célhelye az adatexportáláshoz, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához.

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali panelen válassza az **adatexportálás**elemet.

    > [!Note]
    > Ha nem látja az adatexportálást a bal oldali ablaktáblán, nem Ön az alkalmazás rendszergazdája. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válassza ki az **azure Event Hubs**, **Azure Service Bus**vagy az **Azure Blob Storage** egyikét az Exportálás céljaként. Az alkalmazáson keresztüli exportálások maximális száma öt.

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data-pnp/export-new2.png)

4. A legördülő listában válassza ki a **Event Hubs névteret**, **Service Bus névteret**, a **Storage-fiók névterét**, vagy **adjon meg egy kapcsolatok karakterláncot**.

    - A IoT Central alkalmazással megegyező előfizetésben csak a Storage-fiókok, a Event Hubs névterek és a Service Bus névterek láthatók. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.
    - A hét napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. A hét napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új Event hub létrehozása](media/howto-export-data-pnp/export-eh.png)

5. Választható Ha a **kapcsolódási karakterlánc megadása**lehetőséget választotta, a rendszer egy új mezőt jelenít meg a kapcsolódási karakterlánc beillesztéséhez. A következőhöz tartozó kapcsolódási karakterlánc lekérése:
    - Event Hubs vagy Service Bus, lépjen a Azure Portal névtér elemére.
        - A **Beállítások**területen válassza a **megosztott elérési szabályzatok** elemet.
        - Válassza ki az alapértelmezett **RootManageSharedAccessKey** , vagy hozzon létre egy újat
        - Az elsődleges vagy a másodlagos kapcsolatok karakterláncának másolása
    - Storage-fiók, nyissa meg a Azure Portal Storage-fiókját:
        - A **Beállítások**területen válassza a **hozzáférési kulcsok** elemet.
        - Másolja a key1-vagy a key2-kapcsolatok karakterláncát

6. Válassza ki az Event hub, a várólista, a témakör vagy a tároló elemet a legördülő listából.

7. Az **exportálni**kívánt adat területen válassza ki az exportálandó adattípusokat **, ha a**típust be értékre állítja.

8. A folyamatos adatexportálás bekapcsolásához győződjön meg arról, hogy az **adatexportálási** váltógomb be van **kapcsolva**. Kattintson a **Mentés** gombra.

9. Néhány perc elteltével az adatai megjelennek a kiválasztott célhelyen.

## <a name="data-format"></a>Adatformátum

A rendszer az adatexportálást az Event hub-ba vagy Service Bus üzenetsor vagy témakörhöz közel valós időben.

A rendszer percenként egyszer exportálja az adatait a Storage-fiókjába, és minden olyan fájllal, amely a legutóbbi exportált fájl óta változást tartalmaz. Az exportált adatfájlok JSON formátumú három mappába kerülnek. A Storage-fiók alapértelmezett elérési útjai a következők:

- Telemetria: _{Container}/{app-ID}/telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök: _{Container}/{app-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök sablonjai: _{Container}/{app-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásával tallózhat a Azure Portalban, ha a fájlra navigál, és a **blob szerkesztése** lapot választja.

Az exportált telemetria-adatok teljes egészében tartalmazzák az eszköz által IoT Central küldött üzenetet, nem csak a telemetria értékeket. Az exportált eszközökhöz tartozó adatok az összes eszköz tulajdonságainak és metaadatainak változásait tartalmazzák, az exportált eszközök pedig az összes eszközosztály változásait tartalmazzák. Az exportált érték a Body (törzs) tulajdonságban van, és JSON formátumú.

### <a name="telemetry"></a>Telemetria

A rendszer gyorsan exportál egy új üzenetet, miután IoT Central fogadja az üzenetet az eszközről.

- Event Hubs és Service Bus összes exportált üzenete tartalmazza az eszköz JSON formátumban küldött teljes üzenetét.
- A blob Storage-ban exportált fájlok ugyanazt a formátumot használják, mint a blob Storage-ba [IoT hub üzenet-útválasztás](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) által exportált üzenetek. Győződjön meg arról, hogy az eszközök `contentType: application/JSON` és `contentEncoding:utf-8` (vagy `utf-16`, `utf-32`) üzeneteket küldenek. Példaként tekintse meg a [IoT hub dokumentációját](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

A telemetria küldő eszközöket az eszközök azonosítói jelölik (lásd a következő részeket). Az eszközök nevének beszerzéséhez exportálja az eszközöket, és korrelálja az egyes üzeneteket az **connectionDeviceId** , amely megfelel az eszköz **deviceId** -beli értékének.

Az alábbi példa egy üzenetet jelenít meg az Event hub-ban vagy Service Bus-várólistában vagy-témakörben fogadott telemetria-információkról.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Az alábbi példa egy, a blob Storage-ban JSON formátumú rekordot mutat be:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Eszközök

Az eszköz adatait tartalmazó üzeneteket a rendszer néhány percenként egyszer elküldi az Event hub-nak vagy Service Bus üzenetsor vagy témakörnek. Az új Pillanatképek percenként egyszer íródnak a blob Storage-ba. Minden üzenet vagy pillanatkép a következőkkel kapcsolatos információkat tartalmazza:

- Új, hozzáadott eszközök
- Módosított tulajdonságértékeket használó eszközök

A pillanatképben szereplő minden üzenet vagy rekord az eszköz egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az információk tartalma:

- az eszköz `@id` IoT Central
- az eszköz `name`
- `deviceId` a [Device kiépítési szolgáltatásból](https://aka.ms/iotcentraldocsdps)
- Eszköz sablonjának adatai
- Tulajdonságértékek

Az utolsó köteg óta törölt eszközök nem lesznek exportálva. Jelenleg nincsenek mutatók a törölt eszközök exportált üzeneteiben.

Az eszközök sablonja, amelyhez az egyes eszközök tartoznak, egy eszköz-sablon azonosítója jelöli. Az eszköz sablonjának beolvasásához ügyeljen arra, hogy az eszköz sablonjának adatmennyiségét is exportálja.

Az alábbi példa egy üzenetet jelenít meg az Event hub-ban vagy Service Bus üzenetsor vagy témakörben található eszköz adatainak:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. A következő példa JSON formátumú rekordot mutat be.

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Eszközök sablonjai

A rendszer néhány percenként egyszer elküldi az eszköz-sablonok adatait tartalmazó üzeneteket az Event hub-nak vagy Service Bus üzenetsor vagy témakör számára. Az új Pillanatképek percenként egyszer írhatók a blob Storage-ba. Ezért néhány percen belül egy köteg üzenet érkezik a következő információkkal:

A rendszer néhány percenként egyszer elküldi az eszköz-sablon adatait tartalmazó üzeneteket az Event hub vagy Service Bus üzenetsor vagy témakör számára. Az új Pillanatképek percenként egyszer íródnak a blob Storage-ba. Minden üzenet vagy pillanatkép a következőkkel kapcsolatos információkat tartalmazza:

- Új vagy verziószámmal felvett vagy telepített eszközök
- Módosított képességi modellel, felhő tulajdonságaival, felülbírálásokkal és kezdeti értékekkel rendelkező eszközök sablonjai

Minden üzenet-vagy pillanatkép-rekord egy eszköz egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az egyes üzenetekben vagy rekordokban küldött információk a következők:

- az eszköz sablonjának `@id`
- az eszköz sablonjának `name`
- az eszköz sablonjának `version`
- Az eszköz `capabilityModel` beleértve annak `interfaces`ét, valamint a telemetria, a tulajdonságokat és a parancsok definícióit
- `cloudProperties` definíciók
- Felülbírálások és kezdeti értékek, a `capabilityModel`

Az eszköz sablonjai törölve lettek az utolsó köteg exportálása óta. Jelenleg nincsenek mutatók a törölt eszközök sablonjaihoz tartozó exportált üzenetekben.

Az alábbi példa egy eszközosztály-üzenetet mutat be az Event hub-ban vagy Service Bus üzenetsor vagy témakörben:

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
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
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. A következő példa JSON formátumú rekordot mutat be.

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan exportálhatja adatait az Azure Event Hubsba és Azure Service Busba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [A Azure Functions elindítása](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
