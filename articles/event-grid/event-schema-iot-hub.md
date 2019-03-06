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
ms.openlocfilehash: 5fcd7c10002e7e1ae9683fdd89d3af14a1500050
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449189"
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
| id | sztring | Az esemény egyedi azonosítója. |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| tárgy | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| adat | objektum | Az IoT Hub eseményadatokat.  |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Minden IoT Hub-események az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| HubName | sztring | Ahol az eszköz létrehozása vagy törlése az IoT Hub nevét. |
| deviceId | sztring | Az eszköz egyedi azonosítója. Ez a karakterlánc kis-és nagybetűket legfeljebb 128 karakter hosszú, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

Az objektum tartalmát minden egyes esemény-közzétevő eltérőek. A **csatlakoztatott eszköz** és **eszköz leválasztott** IoT Hub-események, az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| moduleId | sztring | A modul egyedi azonosítója. Ez a mező egy modul eszközök csak a kimenet. Ez a karakterlánc kis-és nagybetűket legfeljebb 128 karakter hosszú, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| deviceConnectionStateEventInfo | objektum | Kapcsolat Eszközállapot-esemény adatokat
| sequenceNumber | sztring | Egy szám, amely segít jelezheti, hogy az eszköz csatlakoztatva van, vagy az eszköz sorrendben események leválasztása. Legutóbbi esemény egy megfelelő sorszám lesz ez nagyobb, mint az előző esemény. Ez a szám több mint 1 változhat, de szigorúan növekszik. Lásd: [sorszáma használata](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Az objektum tartalmát minden egyes esemény-közzétevő eltérőek. A **létre az eszköz** és **eszköz törlése** IoT Hub-események, az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| ikereszköz | objektum | Az ikereszközök, amely a felhőalapú alkalmazások eszköz metaadatainak ábrázolása kapcsolatos információk. | 
| deviceID | sztring | Az ikereszközök egyedi azonosítója. | 
| ETag | sztring | Egy érvényesítő egy ikereszköz-frissítések konzisztencia biztosításához. Minden egyes etag garantáltan ikereszköz minden egyedi. |  
| deviceEtag| sztring | Egy érvényesítő frissítések egy eszközjegyzékébe konzisztencia biztosításához. Minden egyes deviceEtag garantáltan eszközjegyzék minden egyedi. |
| status | sztring | Az ikereszközök e engedélyezése vagy letiltása. | 
| statusUpdateTime | sztring | Az utolsó ikereszköz Eszközállapot ISO8601 időbélyegzője frissítése. |
| connectionState | sztring | Hogy az eszköz van csatlakoztatva, vagy csatlakoztatva. | 
| lastActivityTime | sztring | Legutóbbi tevékenység ISO8601 időbélyegét. | 
| cloudToDeviceMessageCount | egész szám | Felhőalapú erre az eszközre küldött eszköz üzenetek száma. | 
| authenticationType | sztring | Ehhez az eszközhöz használt hitelesítés típusa: vagy `SAS`, `SelfSigned`, vagy `CertificateAuthority`. |
| x509Thumbprint | sztring | Az ujjlenyomat egy egyedi értéket a x509 tanúsítvány, általában használt egy adott tanúsítvány található a tanúsítványtárolóban. Az ujjlenyomat dinamikusan hozzuk létre az SHA1 algoritmusok használatával, és a tanúsítvány ténylegesen nem létezik. | 
| primaryThumbprint | sztring | Elsődleges ujjlenyomat a x509 a tanúsítványt. |
| secondaryThumbprint | sztring | Másodlagos ujjlenyomat a x509 a tanúsítványt. | 
| version | egész szám | Egész szám, amely értéke eggyel mindegyik az eszköz ideje ikereszköz frissül. |
| kívánt | objektum | Csak az alkalmazás háttér-írják, és olvassa el az eszköz tulajdonságok egy részét. | 
| jelentett | objektum | Csak az eszköz által írt, és az alkalmazás háttér által beolvasott tulajdonságok egy részét. |
| lastUpdated | sztring | Az utolsó ikereszköz eszköztulajdonság ISO8601 időbélyegzője frissítése. | 

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Hogyan működik együtt az IoT Hub és az Event Griddel kapcsolatos további információkért lásd: [reagálhat rájuk az IoT Hub Event Grid használatával a műveletek indítása](../iot-hub/iot-hub-event-grid.md).