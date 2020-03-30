---
title: Az Azure IoT Central adatainak exportálása | Microsoft dokumentumok
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure Event Hubs, az Azure Service Bus és az Azure Blob Storage szolgáltatásba
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157908"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>IoT-adatok exportálása az Azure-beli célállomásokra

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk bemutatja, hogyan használhatja az Azure IoT Central folyamatos adatexportálási szolgáltatását az **Azure Event Hubs,** **az Azure Service Bus**vagy az Azure Blob storage-példányokba történő exportálásához. **Azure Blob storage** Az adatok exportálása JSON formátumban történik, és tartalmazhatják a telemetriai adatokat, az eszközadatokat és az eszközsablon-információkat. Az exportált adatokat a következő célokra használja:

- Meleg út elemzési és elemzési adatok. Ez a beállítás magában foglalja az egyéni szabályok aktiválását az Azure Stream Analytics-ben, egyéni munkafolyamatok aktiválását az Azure Logic Apps-ben, vagy az Azure Functions átalakítását.
- Hidegút-elemzések, például az Azure Machine Learning betanítási modelljei vagy a Microsoft Power BI hosszú távú trendelemzése.

> [!Note]
> Ha bekapcsolja a folyamatos adatexportálást, attól a pillanattól kezdve csak az adatokat kapja meg. Jelenleg az adatok nem olvashatók be egy olyan időpontban, amikor a folyamatos adatexportálás ki volt kapcsolva. Több előzményadat megőrzéséhez kapcsolja be a folyamatos adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

Az IoT Central alkalmazás rendszergazdájának kell lennie, vagy adatexportálási engedélyekkel kell rendelkeznie.

## <a name="set-up-export-destination"></a>Exportálási cél beállítása

A folyamatos adatexportálás konfigurálása előtt az exportcélnak léteznie kell.

### <a name="create-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Ha nem rendelkezik meglévő Event Hubs névtérrel, amelybe exportálhat, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret az Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.EventHub) További információ az [Azure Event Hubs-dokumentumokban.](../../event-hubs/event-hubs-create.md)

2. Válasszon előfizetést. Adatokat exportálhat más előfizetések, amelyek nem ugyanabban az előfizetésben, mint az IoT Central alkalmazás. Ebben az esetben kapcsolati karakterlánc használatával csatlakozik.

3. Hozzon létre egy eseményközpontot az Event Hubs névterében. Lépjen a névtérbe, és válassza a **+ Event Hub** a tetején egy eseményközpont-példány létrehozásához.

### <a name="create-service-bus-namespace"></a>Service Bus-névtér létrehozása

Ha nincs meglévő Service Bus-névtér, amelybe exportálhatna, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus-névteret az Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) További információ az [Azure Service Bus-dokumentumokban](../../service-bus-messaging/service-bus-create-namespace-portal.md)található.
2. Válasszon előfizetést. Adatokat exportálhat más előfizetések, amelyek nem ugyanabban az előfizetésben, mint az IoT Central alkalmazás. Ebben az esetben kapcsolati karakterlánc használatával csatlakozik.

3. Nyissa meg a Service Bus névterét, és válassza a **+ Várólista** vagy **+ Témakör** lehetőséget a tetején, ha várólistát vagy témakört szeretne exportálni.

Ha a Service Bus-t választja exportálási célként, a várólistákban és témakörökben nem engedélyezve lehet a munkamenetek vagy a duplikáltelem-észlelés. Ha ezen beállítások bármelyike engedélyezve van, egyes üzenetek nem érkeznek meg a várólistába vagy a témakörbe.

### <a name="create-storage-account"></a>Storage-fiók létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal, amelybe exportálhatja, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új tárfiókot az Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) További információ az új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) vagy [az Azure Data Lake Storage v2 storage-fiókok](../../storage/blobs/data-lake-storage-quickstart-create-account.md)létrehozásáról. Az adatexportálás csak olyan tárfiókokba írhat adatokat, amelyek támogatják a blokkblobokat. Az alábbi lista az ismert kompatibilis típusú tárfiókokat tartalmazza: 

    |Teljesítményszint|Fiók típusa|
    |-|-|
    |Standard|Általános célú V2|
    |Standard|Általános célú V1|
    |Standard|Blob Storage|
    |Prémium|Blob-tároló blokkolása|

2. Hozzon létre egy tárolót a tárfiókban. Nyissa meg a tárfiókot. A **Blob Service csoportban**válassza **a Blobok tallózása**lehetőséget. Új tároló létrehozásához válassza a **+ Tároló** lehetőséget a tetején.

## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy rendelkezik az adatok exportálásához szükséges célállomással, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához.

1. Jelentkezzen be az IoT Central alkalmazásba.

2. A bal oldali ablaktáblában válassza az **Adatexportálás**lehetőséget.

    > [!Note]
    > Ha a bal oldali ablaktáblában nem látható az Adatexportálás, akkor nincs engedélye az adatok exportálásának konfigurálásához az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Válassza a **+ Új** gombot a jobb felső sarokban. Válasszon egyet az **Azure Event Hubs**, **az Azure Service Bus**vagy az Azure Blob **storage** közül az exportálás célhelyeként. Az exportok maximális száma kérelemenként öt.

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data/new-export-definition.png)

4. A legördülő listában jelölje ki az **Event Hubs névteret**, **a Service Bus-névteret**, **a Tárfiók névterét**, vagy **adja meg a kapcsolati karakterláncot.**

    - Csak az IoT Central alkalmazással azonos előfizetésben láthatja a Storage-fiókokat, az Event Hubs-névtereket és a Service Bus-névtereket. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **a Kapcsolati karakterlánc megadása lehetőséget,** és olvassa el az 5.
    - Az ingyenes díjszabási csomaggal létrehozott alkalmazások esetében a folyamatos adatexportálás konfigurálásának egyetlen módja egy kapcsolati karakterlánc. Az ingyenes díjcsomagban lévő alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új eseményközpont létrehozása](media/howto-export-data/export-event-hub.png)

5. (Nem kötelező) Ha **a Kapcsolati karakterlánc megadása**lehetőséget választotta, egy új mező jelenik meg a kapcsolati karakterlánc beillesztéséhez. A kapcsolati karakterlánc beszerezése a következőhöz:
    - Event Hubs vagy Service Bus, nyissa meg a névteret az Azure Portalon.
        - A **Beállítások csoportban**válassza a **Közös hozzáférési házirendek lehetőséget.**
        - Válassza ki az alapértelmezett **RootManageSharedAccessKey-t,** vagy hozzon létre egy újat
        - Az elsődleges vagy másodlagos kapcsolati karakterlánc másolása
    - Tárfiók, nyissa meg a Storage-fiók az Azure Portalon:
        - A **Beállítások csoportban**válassza az **Access-billentyűk lehetőséget.**
        - A key1 kapcsolati karakterlánc vagy a key2 kapcsolati karakterlánc másolása

6. Válasszon egy eseményközpontot, várólistát, témakört vagy tárolót a legördülő listából.

7. Az **Exportálandó adatok**csoportban válassza ki az exportálandó adattípusokat a típus **Be**beállításával.

8. A folyamatos adatexportálás bekapcsolásához győződjön meg arról, hogy az **Engedélyezve** váltás be van **kapcsolva.** Kattintson a **Mentés** gombra.

9. Néhány perc múlva az adatok megjelennek a kiválasztott úti célban.

## <a name="export-contents-and-format"></a>Tartalom és formátum exportálása

Exportált telemetriai adatok tartalmazza a teljes üzenetet az eszközök küldött IoT Central, nem csak a telemetriai értékek magukat. Az exportált eszközök adatai az összes eszköz tulajdonságainak és metaadatainak változásait tartalmazzák, az exportált eszközsablonok pedig az összes eszközsablon módosításait.

Az Event Hubs és a Service Bus esetében az adatok exportálása közel valós időben történik. Az adatok a törzs tulajdonságában helyezkednek el, és JSON formátumban vannak (a példákat lásd alább).

A Blob Storage esetében az adatok percenként egyszer exportálódnak, és minden fájl tartalmazza a legutóbbi exportált fájl óta végrehajtott módosítások kötegét. Az exportált adatok három mappába kerülnek JSON formátumban. A tárfiók alapértelmezett elérési útjai a következők:

- Telemetriai adatok: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Eszközök: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Eszközsablonok: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Az Azure Portalon az exportált fájlok között tallózhat, ha a fájlra navigál, és a Blob szerkesztése lapot **választja.**


## <a name="telemetry"></a>Telemetria

Az Event Hubs és a Service Bus esetében az új üzenet exportálása gyorsan történik, miután az IoT Central megkapja az üzenetet egy eszközről, és minden egyes exportált üzenet tartalmazza a törzs tulajdonságában JSON formátumban küldött teljes üzenetet.

A Blob Storage esetében az üzenetek kötegelése és exportálása percenként egyszer lesz. Az exportált fájlok ugyanazt a formátumot használják, mint az [IoT Hub üzenet-útválasztás](../../iot-hub/tutorial-routing.md) a blob storage által exportált üzenetfájlokat. 

> [!NOTE]
> A Blob Storage esetében győződjön meg `contentType: application/JSON` arról, hogy az eszközök olyan üzeneteket küldenek, amelyek rendelkeznek és `contentEncoding:utf-8` (vagy `utf-16`, `utf-32`) rendelkeznek. Tekintse meg az [IoT Hub dokumentációját](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) egy példa.

Az eszközt, amely elküldte a telemetriai képviseli az eszköz azonosítója (lásd a következő szakaszok). Az eszközök nevének leválasztásához exportálja az eszközadatokat, és korrelálja az egyes üzeneteket az eszközüzenet **deviceId** azonosítójának megfelelő **connectionDeviceId** használatával.

Ez egy példa üzenet egy eseményközpontban vagy a Service Bus várólistában vagy témakörben.

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

Ez egy példa rekord exportált blob storage:

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

A pillanatképben lévő minden egyes üzenet vagy rekord egy eszköz, valamint annak eszköz- és felhőtulajdonságainak egy vagy több módosítását jelöli a legutóbbi exportált üzenet óta. Az érintett műveletek közé tartoznak az alábbiak:

- `id`a készülék et az IoT Central
- `displayName`a készülék
- Eszközsablon-azonosító`instanceOf`
- `simulated`jelző, igaz, ha az eszköz szimulált eszköz
- `provisioned`jelző, igaz, ha az eszköz ki van építve
- `approved`jelző, igaz, ha az eszközt jóváhagyták az adatok küldésére
- Tulajdonság értékek
- `properties`beleértve az eszköz- és felhőtulajdonságok értékeit

A törölt eszközök nem lesznek exportálva. Jelenleg a törölt eszközök exportált üzeneteiben nincsenek jelzők.

Az Event Hubs és a Service Bus esetében az eszközadatokat tartalmazó üzeneteket az eseményközpont vagy a Service Bus-várólista vagy témakör közel valós időben küldi el, ahogy az az IoT Centralban megjelenik. 

A Blob Storage esetében egy új pillanatkép, amely tartalmazza az összes módosítást, mivel az utolsó írott exportált percenként egyszer.

Ez egy példaüzenet az eseményközpont vagy a Service Bus várólistájában vagy témakörében lévő eszközökről és tulajdonságokadatairól:

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

Ez egy példa pillanatkép, amely eszközöket és tulajdonságokat tartalmaz a Blob Storage-ban. Az exportált fájlok rekordonként egyetlen sort tartalmaznak.

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

## <a name="device-templates"></a>Eszközsablonok

Minden egyes üzenet- vagy pillanatképrekord egy vagy több módosított eszközsablont jelent a legutóbbi exportált üzenet óta. Az egyes üzenetekben vagy rekordokban küldött információk a következőket tartalmazzák:

- `id`eszközsablont, amely `instanceOf` megfelel a fenti eszközfolyamnak
- `displayName`az eszközsablon
- Az `capabilityModel` eszköz, `interfaces`beleértve a , és a telemetriai, tulajdonságok és parancsok definíciók
- `cloudProperties`Meghatározások
- Felülírja és a kezdeti értékeket, a`capabilityModel`

A törölt eszközsablonok at nem exportálja a program. Jelenleg a törölt eszközsablonok exportált üzeneteiben nincsenek jelzők.

Az Event Hubs és a Service Bus esetében az eszközsablon-adatokat tartalmazó üzeneteket az eseményközpont vagy a Service Bus-várólista vagy témakör közel valós időben küldi el, ahogy az az IoT Centralban megjelenik. 

A Blob Storage esetében egy új pillanatkép, amely tartalmazza az összes módosítást, mivel az utolsó írott exportált percenként egyszer.

Ez egy példaüzenet az eszközsablonok adatairól az eseményközpontban vagy a Service Bus várólistájában vagy témakörében:

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

Ez egy példa pillanatkép, amely eszközöket és tulajdonságokat tartalmaz a Blob Storage-ban. Az exportált fájlok rekordonként egyetlen sort tartalmaznak.

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
## <a name="data-format-change-notice"></a>Adatformátum-módosítási értesítés

> [!Note]
> A telemetriai adatformátumot ez a módosítás nem befolyásolja. Ez csak az eszközöket és az eszközsablonokat érinti.

Ha van egy meglévő adatexportálásazol az előzetes verziójú alkalmazásban, és az *Eszközök* és *eszköz sablonok* streamek be vannak kapcsolva, **2020.** Ez az Azure Blob Storage, az Azure Event Hubs és az Azure Service Bus exportálására vonatkozik.

2020. február 3-tól minden új exportálás az Eszközök és Eszköz sablonokkal rendelkező alkalmazásokban a fent leírt adatformátummal fog rendelkezni. Az ezt megelőzően létrehozott összes export 2020. Az új adatformátum megegyezik az [eszköz](https://docs.microsoft.com/rest/api/iotcentral/devices/get), [eszköz tulajdonság](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties), eszköz [felhő tulajdonság](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) és eszköz [sablon](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) objektumok az IoT Central nyilvános API-t. 
 
**Az Eszközök**esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:
- `@id`eszköz eltávolítása, `deviceId` átnevezése:`id` 
- `provisioned`jelző tadunk hozzá az eszköz kiépítési állapotának leírásához
- `approved`jelző tadunk hozzá az eszköz jóváhagyási állapotának leírásához
- `properties`beleértve az eszköz- és felhőtulajdonságokat, megfelel a nyilvános API-ban lévő entitásoknak

**Az Eszközsablonok**esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:

- `@id`eszközsablon átnevezése:`id`
- `@type`az eszközsablon neve a `types`rendszerre van átnevezve, és most egy tömb

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Eszközök (2020. február 3-tól elavult formátum)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Eszközsablonok (a formátum 2020. február 3-tól elavult)
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
## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan exportálhatja az adatokat az Azure Event Hubs, az Azure Service Bus és az Azure Blob Storage szolgáltatásba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Hogyan hozzunk létre webhooks](./howto-create-webhooks.md)
