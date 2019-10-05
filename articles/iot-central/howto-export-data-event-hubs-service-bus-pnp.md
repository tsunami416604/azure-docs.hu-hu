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
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973216"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Exportálja adatait az Azure IoT Centralban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az adatai saját **Azure-Event Hubsba**és **Azure Service Bus** példányokra való exportálására. A **telemetria**, az **eszközöket**és az eszközök **sablonjait** saját célra is exportálhatja a meleg elérési út elemzése és elemzése céljából. Ez magában foglalja az egyéni szabályok beindítását a Azure Stream Analyticsban, az egyéni munkafolyamatok aktiválását a Azure Logic Appsban, vagy a Azure Functions átadását az átalakításhoz.

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
    > Mostantól exportálhat más előfizetésekre is, amelyek **nem egyeznek** meg az utólagos elszámolású IoT Central alkalmazása során. Ebben az esetben kapcsolati sztringet fog használni.
3. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

### <a name="create-service-bus-namespace"></a>Service Bus névtér létrehozása

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . [Azure Service Bus dokumentációban](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)bővebben is olvashat.
2. Válasszon egy előfizetést. 

    > [!Note] 
    > Mostantól exportálhat más előfizetésekre is, amelyek **nem egyeznek** meg az utólagos elszámolású IoT Central alkalmazása során. Ebben az esetben kapcsolati sztringet fog használni.

3. Nyissa meg a Service Bus névteret, és a felül található **+ üzenetsor** vagy **+ témakör** használatával hozzon létre egy üzenetsor vagy témakört az exportáláshoz.


## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy rendelkezik egy Event Hubs/Service Bus céllal, hogy exportálja az adatexportálást, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához. 

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali menüben válassza az **adatexportálás**elemet.

    > [!Note]
    > Ha nem látja az adatexportálást a bal oldali menüben, nem Ön az alkalmazás rendszergazdája. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válasszon ki egy **Azure-Event Hubs** vagy **Azure Service Bus** az Exportálás célhelye. 

    > [!NOTE] 
    > Az alkalmazások exportálásának maximális száma öt. 

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data-pnp/export-new2.png)

4. A legördülő listában válassza ki a **Event Hubs névtér/Service Bus névteret**. A lista utolsó elemét is kiválaszthatja, amely a **kapcsolatok karakterláncát adja meg**. 

    > [!NOTE] 
    > A Storage-fiókok/Event Hubs névterek/Service Bus névterek a **IoT Central alkalmazással megegyező előfizetésben**jelennek meg. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.

    > [!NOTE] 
    > A 7 napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. Ennek az az oka, hogy a 7 napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új CDE-esemény hub létrehozása](media/howto-export-data-pnp/export-eh.png)

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

A telemetria, az eszközök és az eszköz sablonjainak adatait exportálja az Event hub-ba, vagy Service Bus üzenetsor vagy témakör a közeljövőben. Az exportált telemetria-adatok teljes egészében tartalmazzák az eszköz által IoT Central küldött üzenetet, nem csak a telemetria értékeket. Az exportált eszközökhöz tartozó adatok az összes eszköz tulajdonságainak és metaadatainak változásait tartalmazzák, az exportált eszközök pedig az összes eszközosztály változásait tartalmazzák. Az exportált érték a "Body" tulajdonságban van, és JSON formátumú.

> [!NOTE]
> Service Bus exportálási célhelyként való kiválasztásakor a várólisták és a témakörök **nem rendelkezhetnek a munkamenetek és az ismétlődő észlelések engedélyezésével**. Ha ezek bármelyike engedélyezve van, néhány üzenet nem érkezik meg a várólistán vagy a témakörben.

### <a name="telemetry"></a>Telemetria

A rendszer gyorsan exportál egy új üzenetet, miután IoT Central fogadja az üzenetet az eszközről. Event Hubs és Service Bus összes exportált üzenete teljes üzenetet tartalmaz, amelyet az eszköz JSON formátumban küldött a "Body" tulajdonságban. 

> [!NOTE]
> A telemetria küldő eszközöket az eszközök azonosítói jelölik (lásd a következő részeket). Az eszközök nevének beszerzéséhez exportálja az eszközöket, és korrelálja az egyes messsage a **connectionDeviceId** , amely megfelel az eszközhöz tartozó üzenet **deviceId** értékének.

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

### <a name="devices"></a>Eszközök

Az eszköz adatait tartalmazó üzeneteket a rendszer néhány percenként egyszer elküldi az Event hub-nak vagy Service Bus üzenetsor vagy témakörnek. Ez azt jelenti, hogy minden percben egy köteg üzenet érkezik a
- Új, hozzáadott eszközök
- Módosított tulajdonságértékeket használó eszközök

Minden üzenet az eszköz egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az egyes üzenetekben küldendő információk a következők:
- @no__t – az eszköz 0 IoT Central
- az eszköz @no__t – 0
- @no__t – 0 a [Device kiépítési szolgáltatásból](https://aka.ms/iotcentraldocsdps)
- Eszköz sablonjának adatai
- Tulajdonságok értékei

> [!NOTE]
> Az utolsó köteg óta törölt eszközök nem lesznek exportálva. Jelenleg nincsenek mutatók a törölt eszközök exportált üzeneteiben.
>
> Az eszközök sablonja, amelyhez az egyes eszközök tartoznak, egy eszköz-sablon azonosítója jelöli. Az eszköz sablonjának beolvasásához ügyeljen arra, hogy az eszköz sablonjának adatmennyiségét is exportálja.

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

### <a name="device-templates"></a>Eszközsablonok

A rendszer néhány percenként egyszer elküldi az eszköz-sablonok adatait tartalmazó üzeneteket az Event hub-nak vagy Service Bus üzenetsor vagy témakör számára. Ez azt jelenti, hogy minden percben egy köteg üzenet érkezik a
- Új vagy verziószámmal felvett vagy telepített eszközök
- Megváltoztatott capabilityModels, cloudProperties, felülbírálásokkal és kezdeti értékekkel rendelkező eszközök sablonjai

Minden üzenet a legutóbbi exportált üzenet óta egy vagy több módosítást jelképez az eszközön. Az egyes üzenetekben küldendő információk a következők:
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- A `capabilityModel` eszköz, beleértve a `interfaces`, valamint a telemetria, a tulajdonságok és a parancsok definícióit
- @no__t – 0 definíció
- Felülbírálások és kezdeti értékek, a `capabilityModel` vonallal

> [!NOTE]
> Az eszköz sablonjai törölve lettek az utolsó köteg exportálása óta. Jelenleg nincsenek mutatók a törölt eszközök sablonjaihoz tartozó exportált üzenetekben.

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

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan exportálhatja adatait az Azure Event Hubsba és Azure Service Busba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [A Azure Functions elindítása](howto-trigger-azure-functions.md)
