---
title: Az Azure IoT Hub és az Event Grid |} A Microsoft Docs
description: Azure Event Grid használatával aktiválhat folyamatokat alapján műveleteket, amelyeket egy IoT hubot.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 068e9a3379bd2762455aade1761592fa70a09a20
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144378"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>IoT Hub-események reagálnak a műveletek indítása Event Grid használatával

Az Azure IoT Hub integrálható az Azure Event Griddel, hogy eseményértesítések küldése más szolgáltatásoknak, és alsóbb rétegbeli folyamatokat aktiválhat. Konfigurálja az üzleti alkalmazások, az IoT Hub-események figyelésére, úgy, hogy megbízható, méretezhető és biztonságos módon reagálhat a kritikus eseményeket. Például készítsen egy alkalmazás például egy adatbázis frissítésével, egy a jegy létrehozása és e-mail-értesítés kézbesítése minden alkalommal, amikor egy új IoT-eszköz regisztrálva van az IoT hub több művelet végrehajtásához. 

[Az Azure Event Grid] [ lnk-eg-overview] van egy teljes körűen felügyelt esemény-útválasztó szolgáltatás, amely egy közzétételi-feliratkozási modell. Az Azure-szolgáltatásokhoz hasonlóan beépített támogatással rendelkezik az Event Grid [Azure Functions](../azure-functions/functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), és nem Azure-szolgáltatások, webhookok segítségével közvetíti miatti riasztás. Az eseménykezelőket, amely támogatja az Event Grid teljes listáját lásd: [Azure Event Grid bemutatása][lnk-eg-overview]. 

![Azure Event Grid-architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Event Grid-integrációt a régiókban, ahol az Event Grid támogatott található IoT-központok érhető el. Régiók legfrissebb listáját lásd: [Azure Event Grid bemutatása][lnk-eg-overview]. 

## <a name="event-types"></a>Eseménytípusok

Az IoT Hub közzéteszi a következő esemény típusa: 

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Amikor regisztrál egy eszközt egy IoT hubra közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszköz IoT hubról törlik közzé. | 
| Microsoft.Devices.DeviceConnected | Ha egy eszköz csatlakozik az IoT hub közzé. | 
| Microsoft.Devices.DeviceDisconnected | Ha egy eszköz nem kapcsolódik az IoT hub közzé. | 
Fontos megjegyezni, hogy az eszköz csatlakoztatva, és eszköz választva eseményeinek hamarosan engedélyezve lesz a kelet-Kanada és kelet-USA régióban.

Az Azure Portalon vagy az Azure CLI használatával konfigurálása események közzététele az egyes IoT hubról. Tegyük fel, próbálja meg az oktatóanyag [Logic Apps használata az Azure IoT Hub-események küldése e-mailes értesítést](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Eseményséma

IoT Hub-események kell reagálni az igények változásaira az eszköz életciklusának minden információt tartalmaznak. Az IoT Hub esemény ellenőrzésével kezdetű az esemény típusa tulajdonság azonosíthatja **Microsoft.Devices**. Event Grid-esemény tulajdonságai használatával kapcsolatos további információkért lásd: a [Event Grid-esemény séma](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Csatlakoztatott eszköz-séma

Az alábbi példa bemutatja a séma csatlakoztatott eszköz esemény: 

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-created-schema"></a>Eszköz létrehozva sémájának

Az alábbi példa bemutatja a séma, esemény létrehozott eszköz: 

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
      "deviceEtag":"null",
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

Minden egyes tulajdonság egy részletes ismertetését lásd: [az IoT Hub az Azure Event Grid eseménysémája](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Események szűrése

Az IoT Hub eseményelőfizetések események esemény típusa és az eszköz neve alapján szűrhetők. Az Event Grid során tárgy szűrőket alapján **megkezdi a** (előtag) és **ér véget a** (utótag) megegyezik. A szűrő egy `AND` operátor szerinti szűrése, így az előfizető kézbesíti az eseményeket egy témát, amelyek megfelelnek az előtagot és utótagot. 

IoT-események tárgya a formátumot használja:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Korlátozások a csatlakoztatott eszközök és az eszköz leválasztott események

Csatlakoztatott eszközök és az eszköz választva eseményeinek kapni, meg kell nyitnia a D2C hivatkozás vagy C2D hivatkozásra az eszközhöz. Ha az eszköz MQTT protokoll használ, az IoT Hub a hivatkozás megnyitásához C2D fogja megőrizni. Az AMQP hívása úgy is megnyithatja a C2D hivatkozásra a [aszinkron API fogadása](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). A D2C kapcsolat meg nyitva, ha telemetriát küld. Ha az eszköz kapcsolati villogó van, vagyis az eszköz kapcsolódik, és gyakran megszakad, nem küldünk minden egyetlen kapcsolati állapot, de a kapcsolat állapota pillanatfelvétel percenként fog közzétenni. Egy IoT Hub esetleges leállás mi pedig közzétesszük az eszköz kapcsolati állapotát, amint a szolgáltatáskimaradás elhárítása után felett van. Ha az eszköz kapcsolata megszakad, hogy a szolgáltatáskimaradás közben, az eszköz leválasztott esemény közzéteszi 10 percen belül.

## <a name="tips-for-consuming-events"></a>Tippek az események felhasználásához

Alkalmazások, amelyek kezelik az IoT Hub-események követendő ajánlott eljárások:

* Több előfizetés is beállítható úgy, hogy az azonos eseménykezelő események átirányítása ezért fontos, hogy nem azt feltételezik, hogy eseményeket adott forrásból származnak. Mindig ellenőrizze annak érdekében, hogy az IoT hub várt származik az üzenet témakörben találhatók. 
* Nem érdemes feltételezni, hogy az összes esemény jelenhet meg-e a várt típusok. Mindig ellenőrizze az esemény típusa, az üzenet feldolgozása előtt.
* Üzenetek érkezésekor is, üzemen kívüli vagy késleltetéssel. Az etag mező segítségével tisztában azzal, ha az adatok és objektumok naprakész.

## <a name="next-steps"></a>További lépések

* [Próbálja ki az IoT Hub-események oktatóanyag](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Ismerje meg, hogyan eszköz csatlakoztatva, és kapcsolódik az események sorrendjének](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* [További információ az Event Grid][lnk-eg-overview]
* [Hasonlítsa össze az IoT Hub-események és az üzenetek közötti különbségek][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md