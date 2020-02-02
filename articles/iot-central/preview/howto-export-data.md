---
title: Az Azure IoT Central-beli adatszolgáltatások exportálása | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Event Hubsba, Azure Service Busba és az Azure-ba Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 612db9963b02e905c3a48d61a4f7a7ed6f832fba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939023"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Azure IoT Central-beli adatszolgáltatások exportálása (előzetes verzió)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az Azure **Event Hubs**, **Azure Service Bus**vagy **Azure Blob Storage** -példányokban tárolt adatai exportálásához. Az adatok JSON formátumúak, és tartalmazhatják a telemetria, az eszköz adatait és az eszköz sablonjának adatait. Az exportált adatértékek használata:

- Meleg elérésű elemzések és elemzések. Ez a beállítás magában foglalja az egyéni szabályok beindítását a Azure Stream Analyticsban, az egyéni munkafolyamatok aktiválását a Azure Logic Appsban, vagy átadja a Azure Functions át.
- A Microsoft Power BI-ban a ritka elérésű elemzések, például a Azure Machine Learning vagy a hosszú távú trendek elemzése során betanítási modellek.

> [!Note]
> Ha bekapcsolja a folyamatos adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adott adatot. Jelenleg nem lehet lekérni az adatgyűjtési időt, amikor a folyamatos adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a folyamatos adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

A IoT Central alkalmazásban rendszergazdának kell lennie, vagy az adatexportálási engedélyekkel kell rendelkeznie.

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
    > Ha nem látja az adatexportálást a bal oldali ablaktáblán, akkor nincs engedélye az adatexportálás konfigurálására az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válassza ki az **azure Event Hubs**, **Azure Service Bus**vagy az **Azure Blob Storage** egyikét az Exportálás céljaként. Az alkalmazáson keresztüli exportálások maximális száma öt.

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data/export-new2.png)

4. A legördülő listában válassza ki a **Event Hubs névteret**, **Service Bus névteret**, a **Storage-fiók névterét**, vagy **adjon meg egy kapcsolatok karakterláncot**.

    - A IoT Central alkalmazással megegyező előfizetésben csak a Storage-fiókok, a Event Hubs névterek és a Service Bus névterek láthatók. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.
    - A hét napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. A hét napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új Event hub létrehozása](media/howto-export-data/export-eh.png)

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

8. A folyamatos adatexportálás bekapcsolásához győződjön **meg**arról, hogy az **engedélyezve** van-e a váltógomb. Kattintson a **Mentés** gombra.

9. Néhány perc elteltével az adatai megjelennek a kiválasztott célhelyen.

## <a name="export-contents-and-format"></a>Tartalom és formátum exportálása

Az exportált telemetria-adatok teljes egészében tartalmazzák az eszköz által IoT Central küldött üzenetet, nem csak a telemetria értékeket. Az exportált eszközökhöz tartozó adatok az összes eszköz tulajdonságainak és metaadatainak változásait tartalmazzák, az exportált eszközök pedig az összes eszközosztály változásait tartalmazzák.

Event Hubs és Service Bus esetében az adatexportálás közel valós idejű. Az információk a Body (törzs) tulajdonságban találhatók, és JSON formátumúak (lásd alább példákat).

Blob Storage esetében percenként egyszer exportálja az adatmennyiséget, és minden olyan fájlt, amely a legutóbbi exportált fájl óta változást tartalmaz. Az exportált adatfájlok JSON formátumú három mappába kerülnek. A Storage-fiók alapértelmezett elérési útjai a következők:

- Telemetria: _{Container}/{app-ID}/telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök: _{Container}/{app-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök sablonjai: _{Container}/{app-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásával tallózhat a Azure Portalban, ha a fájlra navigál, és a **blob szerkesztése** lapot választja.


## <a name="telemetry"></a>Telemetria

Event Hubs és Service Bus esetén a rendszer gyorsan exportál egy új üzenetet, miután IoT Central fogadja az üzenetet az eszközről, és minden exportált üzenet tartalmazza az eszköz JSON formátumban küldött teljes üzenetét.

Blob Storage esetében az üzenetek kötegelt és percenkénti exportálása történik. Az exportált fájlok ugyanazt a formátumot használják, mint a blob Storage-ba [IoT hub üzenet-útválasztás](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) által exportált üzenet-fájlok. 

> [!NOTE]
> Blob Storage esetén győződjön meg arról, hogy az eszközök `contentType: application/JSON` és `contentEncoding:utf-8` (vagy `utf-16``utf-32`) üzeneteket küldenek. Példaként tekintse meg a [IoT hub dokumentációját](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

A telemetria küldő eszközt az eszköz azonosítója jelöli (lásd a következő részeket). Az eszközök nevének beszerzéséhez exportálja az eszközöket, és korrelálja az egyes üzeneteket az **connectionDeviceId** , amely megfelel az eszköz **deviceId** -beli értékének.

Ez egy példa, amely egy Event hub-vagy Service Bus-üzenetsor vagy-témakörben érkezett.

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

Ez egy példa a blob Storage-ba exportált rekordra:

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

## <a name="devices"></a>Eszközök

A pillanatképben szereplő minden üzenet vagy rekord az eszköz és az eszköz és a felhő tulajdonságai egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az érintett műveletek közé tartoznak az alábbiak:

- az eszköz `id` IoT Central
- az eszköz `displayName`
- Eszköz sablonjának azonosítója `instanceOf`
- `simulated` jelző, igaz, ha az eszköz szimulált eszköz
- `provisioned` jelző, igaz, ha az eszköz ki lett építve
- `approved` jelző, igaz, ha az eszköz jóvá lett hagyva az adatküldéshez
- Tulajdonságértékek
- `properties` az eszköz és a felhő tulajdonságai értékeit is beleértve

A törölt eszközök nincsenek exportálva. Jelenleg nincsenek mutatók a törölt eszközök exportált üzeneteiben.

Event Hubs és Service Bus esetén az eszköz adatait tartalmazó üzeneteket a rendszer közel valós időben küldi el az Event hub-nak vagy Service Bus üzenetsor vagy témakörnek, ahogy az IoT Central jelenik meg. 

Blob Storage esetében az utolsó írás óta minden változást tartalmazó új pillanatképet percenként egyszer exportálunk.

Ez egy példa az eszközök és tulajdonságok adatainak az Event hub-ban vagy a Service Bus-üzenetsor vagy-témakörben:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
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
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Ez egy példa az eszközöket és a tulajdonságokat tartalmazó pillanatképre Blob Storage. Az exportált fájlok rekordokban egyetlen sort tartalmaznak.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Eszközök sablonjai

Minden üzenet-vagy pillanatkép-rekord egy közzétett sablon egy vagy több módosítását jelöli a legutóbbi exportált üzenet óta. Az egyes üzenetekben vagy rekordokban küldött információk a következők:

- `id`, amely megfelel az eszközön a fenti streamek `instanceOf`ének
- az eszköz sablonjának `displayName`
- Az eszköz `capabilityModel` beleértve annak `interfaces`ét, valamint a telemetria, a tulajdonságokat és a parancsok definícióit
- `cloudProperties` definíciók
- Felülbírálások és kezdeti értékek, a `capabilityModel`

A törölt eszközöket a rendszer nem exportálja. Jelenleg nincsenek mutatók a törölt eszközök sablonjaihoz tartozó exportált üzenetekben.

Event Hubs és Service Bus esetén az eszköz sablonjának adatait tartalmazó üzeneteket a rendszer közel valós időben küldi el az Event hub-nak vagy Service Bus üzenetsor vagy témakörnek, ahogy az IoT Central jelenik meg. 

Blob Storage esetében az utolsó írás óta minden változást tartalmazó új pillanatképet percenként egyszer exportálunk.

Ez egy példaként szolgáló üzenet az Event hub vagy a Service Bus üzenetsor vagy témakör eszköz sablonjainak adatait illetően:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
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
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Ez egy példa az eszközöket és a tulajdonságokat tartalmazó pillanatképre Blob Storage. Az exportált fájlok rekordokban egyetlen sort tartalmaznak.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Adatformátum-változási Megjegyzés

> [!Note]
> Ez a változás nem érinti a telemetria adatfolyam-adatformátumát. Csak az eszközök és az eszközökön tárolt adatfolyamok vannak hatással.

Ha az előnézeti alkalmazásban már van egy meglévő adatexportálás az *eszközök* és az *eszköz sablonjainak* bekapcsolásával, az exportálást az **2020. június 30-ig**kell frissítenie. Ez az Azure Blob Storage, az Azure Event Hubs és a Azure Service Bus exportálására vonatkozik.

A 2020. február 3-án kezdődően az eszközökön és az eszközök sablonjain lévő összes új Exportálás a fent ismertetett adatformátummal fog rendelkezni. Az ezt megelőzően létrehozott összes exportálás a régi adatformátumban marad a 2020. június 30-ig, majd azt követően, hogy az Exportálás automatikusan átkerül az új adatformátumba. Az új adatformátum megegyezik a IoT Central nyilvános API-ban található [eszköz](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [eszköz tulajdonság](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), [eszköz felhő tulajdonság](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) és [eszköz sablon](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) objektumaival. 
 
Az **eszközök**esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:
- az eszköz `@id` el lett távolítva, `deviceId` átnevezve a következőre: `id` 
- `provisioned` jelző hozzáadása az eszköz kiépítési állapotának leírásához
- `approved` jelző hozzáadása az eszköz jóváhagyási állapotának leírásához
- `properties`, beleértve az eszköz és a felhő tulajdonságait, a nyilvános API-ban található entitásoknak felel meg

Az **eszközök sablonjai**esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:

- az eszköz sablonjának `@id` átnevezve `id`
- az eszköz sablonjának `@type` átnevezve `types`re, és most egy tömb

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Eszközök (a formátum a 2020. február 3. után elavult)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Eszközök sablonjai (a formátum a 2020. február 3. után elavult)
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

Most, hogy tudja, hogyan exportálhatja adatait az Azure Event Hubsba, Azure Service Busba és az Azure Blob Storageba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [A Azure Functions elindítása](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
