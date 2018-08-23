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
ms.date: 08/17/2018
ms.author: kgremban
ms.openlocfilehash: 4bb33eae53d31701b66d13cb4e810b1a0b8a4b0b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058412"
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

## <a name="example-event"></a>Példa esemény

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Az összes esemény legfelső szintű ugyanazokat az adatokat tartalmazza: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az esemény egyedi azonosítója. |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| adatok | objektum | Az IoT Hub eseményadatokat.  |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum tartalmát minden egyes esemény-közzétevő eltérőek. Az IoT Hub-események az objektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| HubName | sztring | Ahol az eszköz létrehozása vagy törlése az IoT Hub nevét. |
| deviceId | sztring | Az eszköz egyedi azonosítója. Ez a karakterlánc kis-és nagybetűket legfeljebb 128 karakter hosszú, és támogatja az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | sztring | A művelet ISO8601 időbélyegét. |
| opType | sztring | Ez a művelet az IoT Hub által megadott esemény típusa: vagy `DeviceCreated` vagy `DeviceDeleted`.
| ikereszköz | objektum | Az ikereszközök, amely a felhőalapú represenation alkalmazás eszköz metaadatainak kapcsolatos információk. | 
| deviceID | sztring | Az ikereszközök egyedi azonosítója. | 
| Az ETag | sztring | Az ikereszközök tartalmát leíró adatokkal részét. Minden egyes etag garantáltan ikereszköz minden egyedi. | 
| status | sztring | Az ikereszközök e engedélyezése vagy letiltása. | 
| statusUpdateTime | sztring | Az utolsó ikereszköz Eszközállapot ISO8601 időbélyegzője frissítése. |
| connectionState | sztring | Hogy az eszköz van csatlakoztatva, vagy csatlakoztatva. | 
| lastActivityTime | sztring | Legutóbbi tevékenység ISO8601 időbélyegét. | 
| cloudToDeviceMessageCount | egész szám | Felhőalapú erre az eszközre küldött eszköz üzenetek száma. | 
| authenticationType | sztring | Ehhez az eszközhöz használt hitelesítés típusa: vagy `SAS`, `SelfSigned`, vagy `CertificateAuthority`. |
| x509Thumbprint | sztring | Az ujjlenyomat egy egyedi értéket a x509 tanúsítvány, általában használt egy adott tanúsítvány található a tanúsítványtárolóban. Az ujjlenyomat dinamikusan hozzuk létre az SHA1 algoritmusok használatával, és a tanúsítvány ténylegesen nem létezik. | 
| primaryThumbprint | sztring | Elsődleges ujjlenyomat a x509 a tanúsítványt. |
| secondaryThumbprint | sztring | Másodlagos ujjlenyomat a x509 a tanúsítványt. | 
| verzió: | egész szám | Egész szám, amely értéke eggyel mindegyik az eszköz ideje ikereszköz frissül. |
| kívánt | objektum | Csak az alkalmazás háttér-írják, és olvassa el az eszköz tulajdonságok egy részét. | 
| jelentett | objektum | Csak az eszköz által írt, és az alkalmazás háttér által beolvasott tulajdonságok egy részét. |
| lastUpdated | sztring | Az utolsó ikereszköz eszköztulajdonság ISO8601 időbélyegzője frissítése. | 

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Hogyan működik együtt az IoT Hub és az Event Griddel kapcsolatos további információkért lásd: [reagálhat rájuk az IoT Hub Event Grid használatával a műveletek indítása](../iot-hub/iot-hub-event-grid.md).