---
title: Azure Event Grid-séma az IoT Hubhoz | Microsoft dokumentumok
description: Ez a cikk az Azure IoT Hub-események tulajdonságait és sémáját tartalmazza. Felsorolja a rendelkezésre álló eseménytípusokat, egy példaeseményt és az esemény tulajdonságait.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: kgremban
ms.openlocfilehash: cfbd46ad961bd1dc914bae98e761cd83d445ff88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513031"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Azure Event Grid eseménysémája az IoT Hubnak

Ez a cikk az Azure IoT Hub-események tulajdonságait és sémáját tartalmazza. Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md) 

A mintaparancsfájlok és oktatóanyagok listáját az [IoT Hub eseményforrásában](event-sources.md#iot-hub)láthatja.

## <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure IoT Hub a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Akkor közzétéve, ha egy eszköz regisztrálva van egy IoT-központhoz. |
| Microsoft.Devices.DeviceDeleted | Akkor közzétéve, ha egy eszközt törölnek egy IoT-központból. | 
| Microsoft.Devices.DeviceConnected | Akkor jelenik meg, ha egy eszköz egy IoT-központhoz csatlakozik. |
| Microsoft.Devices.DeviceLeed | Akkor közzétéve, ha egy eszköz le van választva egy IoT-központról. | 
| Microsoft.Devices.DeviceTelemetria | Telemetriai üzenet ioT hubra küldésekor közzétéve. |

Az eszköztelemetriai események kivételével minden eszközesemény általánosan elérhető az Event Grid által támogatott összes régióban. Az eszköztelemetriai esemény nyilvános előzetes verzióban érhető el, és az USA keleti régiói, az USA nyugati régiója, Nyugat-Európa, [az Azure Government](../azure-government/documentation-government-welcome.md), az Azure China [21Vianet](/azure/china/china-welcome)és az [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/)kivételével minden régióban elérhető.

## <a name="example-event"></a>Példa esemény

A DeviceConnected és a DeviceDisconnected események sémája azonos struktúrával rendelkezik. Ez a mintaesemény egy olyan esemény sémáját jeleníti meg, amely akkor jelenik meg, amikor egy eszköz egy IoT-központhoz csatlakozik:

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

Az Eszközelem-telemetriai esemény akkor kerül elő, amikor egy telemetriai eseményt küld egy IoT Hubnak. Az eseményhez egy mintaséma látható az alábbiakban.

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

A DeviceCreated és a DeviceDeleted események sémája azonos szerkezetű. Ez a mintaesemény egy olyan esemény sémáját jeleníti meg, amely akkor jelenik meg, ha egy eszköz regisztrálva van egy IoT-központhoz:

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

Minden esemény ugyanazokat a legfelső szintű adatokat tartalmazza: 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az esemény egyedi azonosítója |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| data | objektum | Az IoT Hub eseményadatai.  |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum az összes IoT Hub-esemény esetén a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| hubName | sztring | Annak az IoT Hubnak a neve, ahol az eszközt létrehozták vagy törölték. |
| deviceId | sztring | Az eszköz egyedi azonosítója. Ez a kis- és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú lehet, és támogatja `- : . + % _ # * ? ! ( ) , = @ ; $ '`az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket: . |

Az adatobjektum tartalma minden eseményközzétevő esetében eltérő. 

**Az Eszközcsatlakoztatva és** az **Eszközkapcsolattal leválasztott** IoT Hub-események esetében az adatobjektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| moduleId | sztring | A modul egyedi azonosítója. Ez a mező csak a moduleszközök kimenete. Ez a kis- és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú lehet, és támogatja `- : . + % _ # * ? ! ( ) , = @ ; $ '`az ASCII 7 bites alfanumerikus karaktereket, valamint a következő speciális karaktereket: . |
| deviceConnectionStateEventInfo | objektum | Eszközkapcsolat állapotának eseményadatai
| sequenceNumber (sequenceNumber) | sztring | Egy szám, amely jelzi az eszköz csatlakoztatott vagy leválasztott események sorrendjét. A legutóbbi esemény sorszáma magasabb lesz, mint az előző. Ez a szám több mint 1-vel változhat, de szigorúan növekszik. Tekintse meg [a sorszám használatát.](../iot-hub/iot-hub-how-to-order-connection-state-events.md) |

**Az Eszköztelemetriai** IoT Hub-esemény esetén az adatobjektum az eszközről a felhőbe irányuló üzenetet [IoT hub üzenetformátumban](../iot-hub/iot-hub-devguide-messages-construct.md) tartalmazza, és a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| body (Törzs) | sztring | Az üzenet tartalma az eszközről. |
| properties | sztring | Az alkalmazástulajdonságok felhasználó által definiált karakterláncok, amelyek hozzáadhatók az üzenethez. Ezek a mezők nem kötelezőek. |
| rendszertulajdonságok | sztring | [A rendszertulajdonságok](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) segítenek azonosítani az üzenetek tartalmát és forrását. Az eszköztelemetriai üzenetnek érvényes JSON formátumban kell lennie, a contentType beállítása JSON, a contentEncoding pedig UTF-8-ra van állítva az üzenetrendszer tulajdonságaiban. Ha ez nincs beállítva, majd az IoT Hub alap 64 kódolású formátumban írja az üzeneteket.  |

Az **Eszköz létrehozva** és **az Eszköz törölt** IoT Hub-események esetében az adatobjektum a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Twin | objektum | Az ikereszközről, amely az alkalmazáseszköz metaadatainak felhőalapú ábrázolása. | 
| Deviceid | sztring | Az ikereszköz egyedi azonosítója. | 
| Etag | sztring | Az ikereszköz frissítéseinek konzisztenciáját biztosító érvényesítő. Minden etag garantáltan egyedi eszközönként iker. |  
| deviceEtag| sztring | Az eszközbeállítás-jegyzék frissítéseinek konzisztenciáját biztosító érvényesítő. Minden deviceEtag garantáltan egyedi eszközbeállítási eszközönként. |
| status | sztring | Azt jelzi, hogy az ikereszköz engedélyezve van-e vagy le van-e tiltva. | 
| statusUpdateTime | sztring | Az utolsó ikereszköz-állapotfrissítés ISO8601 időbélyege. |
| connectionState | sztring | Azt jelzi, hogy az eszköz csatlakoztatva van-e vagy nincs-e csatlakoztatva. | 
| lastActivityTime | sztring | Az utolsó tevékenység ISO8601 időbélyege. | 
| cloudToDeviceMessageCount (felhőToDeviceMessageCount) | egész szám | Az eszközre küldött felhő-eszköz üzenetek száma. | 
| authenticationType | sztring | Az eszközhöz használt hitelesítési típus: vagy `SAS`, `SelfSigned`vagy `CertificateAuthority`. |
| x509Ujjlenyomat | sztring | Az ujjlenyomat az x509-es tanúsítvány egyedi értéke, amelyet általában egy tanúsítványtárolóban egy adott tanúsítvány keresésére használnak. Az ujjlenyomat dinamikusan jön létre az SHA1 algoritmus használatával, és fizikailag nem létezik a tanúsítványban. | 
| elsődlegesThumbprint | sztring | Az x509-es tanúsítvány elsődleges ujjlenyomata. |
| másodlagosHüvelykujj-nyomat | sztring | Az x509-es tanúsítvány másodlagos ujjlenyomata. | 
| version | egész szám | Olyan egész szám, amely minden alkalommal, amikor az ikereszköz frissül, eggyel növekszik. |
| Kívánt | objektum | A tulajdonságok egy részét, amely et csak az alkalmazás háttér-, és olvassa el az eszköz által. | 
| Jelentett | objektum | A tulajdonságok egy részét, amely et csak az eszköz írhatja, és az alkalmazás háttér-háttér-olvasni. |
| lastUpdated | sztring | Az eszköz ikertulajdonságának utolsó frissítésének ISO8601 időbélyege. | 

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Ha többet szeretne megtudni arról, hogyan működik együtt az IoT Hub és az Event Grid, olvassa [el az IoT Hub-események react to Event Grid használatával műveletek et.](../iot-hub/iot-hub-event-grid.md)