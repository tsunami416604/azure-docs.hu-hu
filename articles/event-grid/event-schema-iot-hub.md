---
title: Az IoT-központ Azure esemény rács séma |} Microsoft Docs
description: Az esemény séma formátum és az IoT Hub tulajdonságainak referencialapja
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 812ca3ba546112f54a76319fda853d441ce34f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Az IoT-központ Azure esemény rács esemény séma

A cikkben a tulajdonságok és a séma Azure IoT Hub események. Egy esemény sémák bemutatása, lásd: [Azure esemény rács esemény séma](event-schema.md). 

## <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

Az Azure IoT Hub bocsát ki a következő esemény típusa:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Ha az eszköz regisztrálva van az IoT-központ közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszközt az IoT-központ töröl közzé. | 

## <a name="example-event"></a>Példa esemény

A séma DeviceCreated és DeviceDeleted események hasonló struktúrával rendelkezik. Ez a minta az esemény egy esemény jelenik meg, ha az eszköz regisztrálva van az IoT-központ sémája jeleníti meg:

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

Összes esemény azonos legfelső szintű adatokat tartalmazzák: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| A témakör | karakterlánc | A forrás teljes erőforrás elérési útja. Ez a mező nincs írható. Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | Az esemény tulajdonos közzétevő által megadott elérési útja. |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| adat | objektum | Az IoT-központ eseményadatok.  |
| dataVersion | karakterlánc | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok sémaverziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tartalma különböző minden esemény-közzétevő. Az IoT-központ események az objektum tartalmazza a következő tulajdonságokkal:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| hubName | karakterlánc | Az IoT-központot, ahol az eszköz létrehozott vagy törölt neve. |
| deviceId | karakterlánc | Az eszköz egyedi azonosítója. A kis-és nagybetűket karakterlánc legfeljebb 128 karakter hosszú lehet, és támogatja a ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket tartalmazhatja: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | karakterlánc | A művelet ISO8601 időbélyegző. |
| opType | karakterlánc | Az IoT-központ által ehhez a művelethez megadott Eseménytípus: vagy `DeviceCreated` vagy `DeviceDeleted`.
| a két | objektum | Az eszköz iker, amely az alkalmazás eszköz metaadat a felhő represenation kapcsolatos információkat. | 
| deviceID | karakterlánc | A két eszköz egyedi azonosítója. | 
| ETag | karakterlánc | Egy adat, amely az eszköz iker tartalmát írja le. Minden egyes etag garantáltan minden eszköz iker egyedi. | 
| status | karakterlánc | Hogy az eszköz iker engedélyezve vagy letiltva. | 
| statusUpdateTime | karakterlánc | Az utolsó két Eszközállapot ISO8601 időbélyegzője frissítése. |
| connectionState | karakterlánc | Hogy az eszköz van csatlakoztatva, vagy nincs csatlakoztatva. | 
| lastActivityTime | karakterlánc | A legutolsó tevékenység ISO8601 időbélyegző. | 
| cloudToDeviceMessageCount | egész szám | Felhő erre az eszközre küldött eszköz üzenetek száma. | 
| authenticationType | karakterlánc | Ehhez az eszközhöz használt hitelesítés típusa: vagy `SAS`, `SelfSigned`, vagy `CertificateAuthority`. |
| X509Thumbprint | karakterlánc | Az ujjlenyomat egy egyedi értéket a x509 tanúsítvány, egy adott tanúsítvány található a tanúsítványtárolóban általánosan használt. Az ujjlenyomat dinamikusan generálta az SHA1 algoritmusok, és a tanúsítvány fizikailag nem létezik. | 
| primaryThumbprint | karakterlánc | Elsődleges ujjlenyomata a x509 a tanúsítványt. |
| secondaryThumbprint | karakterlánc | Másodlagos ujjlenyomata a x509 a tanúsítványt. | 
| verzió: | egész szám | Egész szám, amely értéke eggyel minden idő az eszköz iker frissül. |
| kívánt | objektum | Csak az alkalmazás háttér-írják, és olvassa el az eszköz tulajdonságok része. | 
| jelentve | objektum | A tulajdonságokat, amelyeket csak az eszköz által írt, és az alkalmazás háttér-olvasni egy részét. |
| lastUpdated | karakterlánc | Az utolsó két eszköztulajdonságon ISO8601 időbélyegzője frissítése. | 

## <a name="next-steps"></a>További lépések

* Megismerkedhet az Azure Event rács, lásd: [Mi az az esemény rács?](overview.md)
* Hogy az IoT-központ és az esemény rács együttműködni, lásd: [műveleteket esemény rács használatával az IoT-központ események reagálnak](../iot-hub/iot-hub-event-grid.md).