---
title: Az Azure Event Grid-sémát a IoT Hub |} A Microsoft Docs
description: Az esemény sémája formátum és az IoT Hub tulajdonságainak referenciaoldala
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: e770beb0470b54d8e13493bca4790323b2e96ce1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393186"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Az Azure IoT hub Event Grid-eseménysémája

Ez a cikk a séma és az Azure IoT Hub-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md). 

Mintaszkriptek és oktatóanyagok listáját lásd: [az IoT Hub-eseményforrás](event-sources.md#iot-hub).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Az Azure IoT Hub a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Amikor regisztrál egy eszközt egy IoT hubra közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszköz IoT hubról törlik közzé. | 
| Microsoft.Devices.DeviceConnected | Ha egy eszköz csatlakozik az IoT hub közzé. |
| Microsoft.Devices.DeviceDisconnected | Ha egy eszköz nem kapcsolódik az IoT hub közzé. | 
| Microsoft.Devices.DeviceTelemetry | Ha egy telemetriai üzenetet küld az IoT hub közzé. |

Eszköz telemetriai események kivételével minden eszköz események általánosan elérhetők az Event Grid által támogatott összes régióban. Eszköz telemetriai esemény nyilvános előzetes verzióban érhető el, és érhető el minden régióban, kivéve az USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Nyugat-Európa, [Azure Government](/azure-government/documentation-government-welcome.md), [Azure China 21Vianet](/azure/china/china-welcome.md), és [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>Példa esemény

A séma DeviceConnected és DeviceDisconnected események ugyanazzal a struktúrával rendelkeznek. Ezt az eseményt a minta egy esemény jelenik meg, ha egy eszköz csatlakozik az IoT hub sémája mutatja be:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

A DeviceTelemetry esemény jelenik meg, ha a telemetriai adatokat a rendszer egy IoT hubot. Az alábbiakban látható egy példa az esemény sémája.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

A séma DeviceCreated és DeviceDeleted események ugyanazzal a struktúrával rendelkeznek. Ezt az eseményt a minta egy esemény jelenik meg, ha regisztrál egy eszközt egy IoT hubra sémája mutatja be:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Az összes esemény legfelső szintű ugyanazokat az adatokat tartalmazza: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | string | Az esemény egyedi azonosítója. |
| topic | string | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| subject | string | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | string | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | string | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| data | objektum | Az IoT Hub eseményadatokat.  |
| dataVersion | string | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | string | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Minden IoT Hub-események az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| hubName | string | Ahol az eszköz létrehozása vagy törlése az IoT Hub nevét. |
| deviceId | string | Az eszköz egyedi azonosítója. Ez a karakterlánc kis-és nagybetűket legfeljebb 128 karakter hosszú, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Az objektum tartalmát minden egyes esemény-közzétevő eltérőek. 

A **csatlakoztatott eszköz** és **eszköz leválasztott** IoT Hub-események, az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| moduleId | string | A modul egyedi azonosítója. Ez a mező egy modul eszközök csak a kimenet. Ez a karakterlánc kis-és nagybetűket legfeljebb 128 karakter hosszú, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objektum | Kapcsolat Eszközállapot-esemény adatokat
| sequenceNumber | string | Egy szám, amely segít jelezheti, hogy az eszköz csatlakoztatva van, vagy az eszköz sorrendben események leválasztása. Legutóbbi esemény egy megfelelő sorszám lesz ez nagyobb, mint az előző esemény. Ez a szám több mint 1 változhat, de szigorúan növekszik. Lásd: [sorszáma használata](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

A **Eszköztelemetria** IoT Hub event, az objektum tartalmazza az eszköz a felhőbe irányuló üzenetet [IoT hub üzenet formátuma](../iot-hub/iot-hub-devguide-messages-construct.md) és tulajdonságai a következők:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Törzs | string | Az eszközről az üzenet tartalma. |
| properties | string | Alkalmazástulajdonságok olyan felhasználó által definiált karakterláncok az üzenet adható hozzá. Ezek a mezők kitöltése nem kötelező. |
| Rendszertulajdonságok | string | [A Rendszertulajdonságok](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) tartalmát és az üzenetek forrás segít azonosítani. A contentType állítsa a JSON és contentEncoding UTF-8 üzenet rendszer tulajdonságai párbeszédpanelen állítsa be az eszköz telemetriai üzenetet egy érvényes JSON formátumban kell lennie. Ha nincs beállítva, majd az IoT Hub ír üzeneteket base 64 kódolású formátumban.  |

A **létre az eszköz** és **eszköz törlése** IoT Hub-események, az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| twin | objektum | Az ikereszközök, amely a felhőalapú alkalmazások eszköz metaadatainak ábrázolása kapcsolatos információk. | 
| deviceID | string | Az ikereszközök egyedi azonosítója. | 
| etag | string | Egy érvényesítő egy ikereszköz-frissítések konzisztencia biztosításához. Minden egyes etag garantáltan ikereszköz minden egyedi. |  
| deviceEtag| string | Egy érvényesítő frissítések egy eszközjegyzékébe konzisztencia biztosításához. Minden egyes deviceEtag garantáltan eszközjegyzék minden egyedi. |
| status | string | Az ikereszközök e engedélyezése vagy letiltása. | 
| statusUpdateTime | string | Az utolsó ikereszköz Eszközállapot ISO8601 időbélyegzője frissítése. |
| connectionState | string | Hogy az eszköz van csatlakoztatva, vagy csatlakoztatva. | 
| lastActivityTime | string | Legutóbbi tevékenység ISO8601 időbélyegét. | 
| cloudToDeviceMessageCount | egész szám | Felhőalapú erre az eszközre küldött eszköz üzenetek száma. | 
| authenticationType | string | Ehhez az eszközhöz használt hitelesítés típusa: vagy `SAS`, `SelfSigned`, vagy `CertificateAuthority`. |
| x509Thumbprint | string | Az ujjlenyomat egy egyedi értéket a x509 tanúsítvány, általában használt egy adott tanúsítvány található a tanúsítványtárolóban. Az ujjlenyomat dinamikusan hozzuk létre az SHA1 algoritmusok használatával, és a tanúsítvány ténylegesen nem létezik. | 
| primaryThumbprint | string | Elsődleges ujjlenyomat a x509 a tanúsítványt. |
| secondaryThumbprint | string | Másodlagos ujjlenyomat a x509 a tanúsítványt. | 
| version | egész szám | Egész szám, amely értéke eggyel mindegyik az eszköz ideje ikereszköz frissül. |
| desired | objektum | Csak az alkalmazás háttér-írják, és olvassa el az eszköz tulajdonságok egy részét. | 
| reported | objektum | Csak az eszköz által írt, és az alkalmazás háttér által beolvasott tulajdonságok egy részét. |
| lastUpdated | string | Az utolsó ikereszköz eszköztulajdonság ISO8601 időbélyegzője frissítése. | 

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Hogyan működik együtt az IoT Hub és az Event Griddel kapcsolatos további információkért lásd: [reagálhat rájuk az IoT Hub Event Grid használatával a műveletek indítása](../iot-hub/iot-hub-event-grid.md).
