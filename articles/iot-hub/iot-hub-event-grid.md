---
title: Az Azure IoT Hub és az Event Grid |} A Microsoft Docs
description: Azure Event Grid használatával aktiválhat folyamatokat alapján műveleteket, amelyeket egy IoT hubot.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673853"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>IoT Hub-események reagálnak a műveletek indítása Event Grid használatával

Az Azure IoT Hub integrálható az Azure Event Griddel, hogy eseményértesítések küldése más szolgáltatásoknak, és alsóbb rétegbeli folyamatokat aktiválhat. Konfigurálja az üzleti alkalmazások, az IoT Hub-események figyelésére, úgy, hogy megbízható, méretezhető és biztonságos módon reagálhat a kritikus eseményeket. Ha például hozhat létre olyan alkalmazás, amely frissíti az adatbázist, létrehoz egy munkahelyi jegyet, és továbbítja az e-mailben értesítést minden alkalommal, amikor egy új IoT-eszköz regisztrálása az IoT hub. 

[Az Azure Event Grid](../event-grid/overview.md) van egy teljes körűen felügyelt esemény-útválasztó szolgáltatás, amely egy közzétételi-feliratkozási modell. Az Azure-szolgáltatásokhoz hasonlóan beépített támogatással rendelkezik az Event Grid [Azure Functions](../azure-functions/functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), és nem Azure-szolgáltatások, webhookok segítségével közvetíti miatti riasztás. Az eseménykezelőket, amely támogatja az Event Grid teljes listáját lásd: [Azure Event Grid bemutatása](../event-grid/overview.md). 

![Azure Event Grid-architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Event Grid-integrációt a régiókban, ahol az Event Grid támogatott található IoT-központok érhető el. Régiók legfrissebb listáját lásd: [Azure Event Grid bemutatása](../event-grid/overview.md). 

## <a name="event-types"></a>Eseménytípusok

Az IoT Hub közzéteszi a következő esemény típusa: 

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Amikor regisztrál egy eszközt egy IoT hubra közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszköz IoT hubról törlik közzé. |
| Microsoft.Devices.DeviceConnected | Ha egy eszköz csatlakozik az IoT hub közzé. |
| Microsoft.Devices.DeviceDisconnected | Ha egy eszköz nem kapcsolódik az IoT hub közzé. |

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

Minden egyes tulajdonság egy részletes ismertetését lásd: [az IoT Hub az Azure Event Grid eseménysémája](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Események szűrése

Az IoT Hub eseményelőfizetések események esemény típusa és az eszköz neve alapján szűrhetők. Az Event Grid során tárgy szűrőket alapján **megkezdi a** (előtag) és **ér véget a** (utótag) megegyezik. A szűrő egy `AND` operátor szerinti szűrése, így az előfizető kézbesíti az eseményeket egy témát, amelyek megfelelnek az előtagot és utótagot. 

IoT-események tárgya a formátumot használja:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Korlátozások a csatlakoztatott eszközök és az eszköz leválasztott események

Csatlakoztatott eszközök és az eszköz választva eseményeinek kapni, meg kell nyitnia a D2C hivatkozás vagy C2D hivatkozásra az eszközhöz. Ha az eszköz MQTT protokoll használ, az IoT Hub a hivatkozás megnyitásához C2D fogja megőrizni. Az AMQP és megnyithatja a C2D hivatkozás meghívásával a [aszinkron API fogadása](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

A D2C kapcsolat meg nyitva, ha telemetriát küld. Ha az eszköz kapcsolata van villogó, mely azt jelenti, hogy az eszköz kapcsolódik, és gyakran bontja a kapcsolatot nem küld minden egyetlen kapcsolat állapota egyelőre a kapcsolat állapota pillanatfelvétel percenként fog közzétenni. Egy IoT Hub esetleges leállás mi pedig közzétesszük az eszköz kapcsolati állapotát, amint a szolgáltatáskimaradás elhárítása után felett van. Ha az eszköz kapcsolata megszakad, hogy a szolgáltatáskimaradás közben, az eszköz leválasztott esemény közzéteszi 10 percen belül.

## <a name="tips-for-consuming-events"></a>Tippek az események felhasználásához

Alkalmazások, amelyek kezelik az IoT Hub-események követendő ajánlott eljárások:

* Több előfizetés is beállítható úgy, hogy az azonos eseménykezelő, így nem érdemes feltételezni, hogy vannak-e események egy adott forrásból származó események átirányítása. Mindig ellenőrizze annak érdekében, hogy az IoT hub várt származik az üzenet témakörben találhatók. 

* Nem érdemes feltételezni, hogy az összes esemény jelenhet meg-e a várt típusok. Mindig ellenőrizze az esemény típusa, az üzenet feldolgozása előtt.

* Üzenetek érkezésekor is, üzemen kívüli vagy késleltetéssel. Az etag mező segítségével tisztában azzal, ha az adatok és objektumok naprakész.

## <a name="next-steps"></a>További lépések

* [Próbálja ki az IoT Hub-események oktatóanyag](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](iot-hub-how-to-order-connection-state-events.md)
* [További információ az Event Grid](../event-grid/overview.md)
* [Hasonlítsa össze az IoT Hub-események és az üzenetek közötti különbségek](iot-hub-event-grid-routing-comparison.md)
