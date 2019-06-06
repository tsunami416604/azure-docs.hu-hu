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
ms.openlocfilehash: f08845dbf4168627d0198e81d2092a1fe56c6c89
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733869"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>IoT Hub-események reagálnak a műveletek indítása Event Grid használatával

Az Azure IoT Hub integrálható az Azure Event Griddel, hogy eseményértesítések küldése más szolgáltatásoknak, és alsóbb rétegbeli folyamatokat aktiválhat. Konfigurálja az üzleti alkalmazások, az IoT Hub-események figyelésére, úgy, hogy megbízható, méretezhető és biztonságos módon reagálhat a kritikus eseményeket. Ha például hozhat létre olyan alkalmazás, amely frissíti az adatbázist, létrehoz egy munkahelyi jegyet, és továbbítja az e-mailben értesítést minden alkalommal, amikor egy új IoT-eszköz regisztrálása az IoT hub.

[Az Azure Event Grid](../event-grid/overview.md) van egy teljes körűen felügyelt esemény-útválasztó szolgáltatás, amely egy közzétételi-feliratkozási modell. Az Azure-szolgáltatásokhoz hasonlóan beépített támogatással rendelkezik az Event Grid [Azure Functions](../azure-functions/functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), és nem Azure-szolgáltatások, webhookok segítségével közvetíti miatti riasztás. Az eseménykezelőket, amely támogatja az Event Grid teljes listáját lásd: [Azure Event Grid bemutatása](../event-grid/overview.md).

![Azure Event Grid-architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Event Grid-integrációt a régiókban, ahol az Event Grid támogatott található IoT-központok érhető el. Eszköz telemetriai események kivételével minden eszköz események általánosan elérhetők. Eszköz telemetriai esemény nyilvános előzetes verzióban érhető el, és érhető el minden régióban, kivéve az USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Nyugat-Európa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china/china-welcome), és [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). Régiók legfrissebb listáját lásd: [Azure Event Grid bemutatása](../event-grid/overview.md).

## <a name="event-types"></a>Eseménytípusok

Az IoT Hub közzéteszi a következő esemény típusa:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Amikor regisztrál egy eszközt egy IoT hubra közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszköz IoT hubról törlik közzé. |
| Microsoft.Devices.DeviceConnected | Ha egy eszköz csatlakozik az IoT hub közzé. |
| Microsoft.Devices.DeviceDisconnected | Ha egy eszköz nem kapcsolódik az IoT hub közzé. |
| Microsoft.Devices.DeviceTelemetry | Ha egy eszköz telemetriai üzenetet küld az IoT hub közzétett |

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

### <a name="device-telemetry-schema"></a>Eszköz Telemetriai séma

Eszköz telemetriai üzenetet egy érvényes JSON formátumban kell lennie a contentType JSON értékre és az üzenetben UTF-8 beállítása contentEncoding [Rendszertulajdonságok](iot-hub-devguide-routing-query-syntax.md#system-properties). Ha nincs beállítva, majd az IoT Hub ír üzeneteket base 64 kódolású formátumban.

Gazdagabbá teheti eszköz telemetriai esemény előtt az Event Gridbe Event Grid, a végpont kiválasztásával. További információkért lásd: [üzenet végrehajtott Információbeolvasás áttekintése](iot-hub-message-enrichments-overview.md).

Az alábbi példa bemutatja egy eszköz telemetriai esemény sémája:

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

Az IoT Hub event előfizetések szűrhetők az események, eseménytípus, a tartalmát és a tulajdonos, amely az eszköz neve alapján.

Event Grid lehetővé teszi [szűrés](../event-grid/event-filtering.md) esemény típusa, a tulajdonosok és az adatok a tartalomhoz. Az Event Grid-előfizetés létrehozásakor kiválaszthatja a kijelölt IoT-események előfizetni. Az Event Grid során tárgy szűrőket alapján **megkezdi a** (előtag) és **ér véget a** (utótag) megegyezik. A szűrő egy `AND` operátor szerinti szűrése, így az előfizető kézbesíti az eseményeket egy témát, amelyek megfelelnek az előtagot és utótagot.

IoT-események tárgya a formátumot használja:

```json
devices/{deviceId}
```

Event Grid lehetővé teszi az egyes eseményeket, többek között az adatok tartalmának attribútumok szűréshez is. Ez lehetővé teszi, hogy válassza ki, milyen események kézbesítése a telemetriai adatok üzenet tartalma alapján. Lásd: [Speciális szűrés](../event-grid/event-filtering.md#advanced-filtering) példákat.

Nem telemetriai események DeviceConnected, DeviceDisconnected, DeviceCreated és DeviceDeleted hasonlóan az Event Grid-szűrés használható az előfizetés létrehozásakor. Telemetriai események mellett az Event Gridben, Szűrés felhasználók is szűrheti az ikereszközök, az üzenet tulajdonságait és a törzs útválasztási állapotüzenet-lekérdezés keresztül. Létrehozunk egy alapértelmezett [útvonal](iot-hub-devguide-messages-d2c.md) az IoT Hub, az eszköz telemetriai Event Grid-előfizetés alapján. Ez az egyetlen útvonal az Event Grid-előfizetések mindegyikét képes kezelni. A telemetriai adatok elküldése előtt szűrik az üzeneteket, frissítheti a [útválasztási lekérdezés](iot-hub-devguide-routing-query-syntax.md). Vegye figyelembe, hogy útválasztási lekérdezés az üzenettörzs alkalmazhatók, csak ha a törzs JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Korlátozások a csatlakoztatott eszközök és az eszköz leválasztott események

Csatlakoztatott eszközök és az eszköz választva eseményeinek kapni, meg kell nyitnia a D2C hivatkozás vagy C2D hivatkozásra az eszközhöz. Ha az eszköz MQTT protokoll használ, az IoT Hub a hivatkozás megnyitásához C2D fogja megőrizni. Az AMQP és megnyithatja a C2D hivatkozás meghívásával a [aszinkron API fogadása](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

A D2C kapcsolat meg nyitva, ha telemetriát küld. Ha az eszköz kapcsolata van villogó, mely azt jelenti, hogy az eszköz kapcsolódik, és gyakran bontja a kapcsolatot nem küld minden kapcsolat egyetlen állapot egyelőre teszi közzé a kapcsolat állapota, amelyek egy pillanatkép készül a percenként. Egy IoT Hub esetleges leállás mi pedig közzétesszük az eszköz kapcsolati állapotát, amint a szolgáltatáskimaradás elhárítása után felett van. Ha az eszköz kapcsolata megszakad, hogy a szolgáltatáskimaradás közben, az eszköz leválasztott esemény közzéteszi 10 percen belül.

## <a name="tips-for-consuming-events"></a>Tippek az események felhasználásához

Alkalmazások, amelyek kezelik az IoT Hub-események követendő ajánlott eljárások:

* Több előfizetés is beállítható úgy, hogy az azonos eseménykezelő, így nem érdemes feltételezni, hogy vannak-e események egy adott forrásból származó események átirányítása. Mindig ellenőrizze annak érdekében, hogy az IoT hub várt származik az üzenet témakörben találhatók.

* Nem érdemes feltételezni, hogy az összes esemény jelenhet meg-e a várt típusok. Mindig ellenőrizze az esemény típusa, az üzenet feldolgozása előtt.

* Üzenetek érkezésekor is, üzemen kívüli vagy késleltetéssel. Az etag mező segítségével megismerheti az adatok és objektumok naprakész-e az eszköz létrehozva és törölt eszközök eseményeket.

## <a name="next-steps"></a>További lépések

* [Próbálja ki az IoT Hub-események oktatóanyag](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](iot-hub-how-to-order-connection-state-events.md)

* [További információ az Event Grid](../event-grid/overview.md)

* [Hasonlítsa össze az IoT Hub-események és az üzenetek közötti különbségek](iot-hub-event-grid-routing-comparison.md)
