---
title: Az Azure IoT-központ és az esemény rács |} Microsoft Docs
description: Azure Event rács használatával indul el, a végrehajtandó műveleteket fordulhat elő, az IoT hubon alapján folyamatok.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: f187aa81ca519f2597657f01c2d7a630740b5348
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634311"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Az IoT-központ események reagálnak műveleteket - Preview esemény rács használatával

Az Azure IoT Hub integrálható Azure esemény rács, hogy eseményértesítések küldése más szolgáltatásokkal és alsóbb rétegbeli folyamatok indít. Konfigurálja az üzleti alkalmazások, hogy kritikus fontosságú eseményeket a megbízható, méretezhető és biztonságos módon reagálhasson az IoT Hub-események figyelésére. Például több műveleteket, például egy adatbázis frissítése, a jegy létrehozását, és kézbesíti az e-mailben értesítést, minden alkalommal, amikor egy új IoT eszköz regisztrálva van az IoT hub alkalmazás létrehozásához. 

[Azure esemény rács] [ lnk-eg-overview] egy teljes körűen felügyelt esemény útválasztási szolgáltatás által használt egy közzétételi-előfizetési modell. Esemény rács rendelkezik Azure-szolgáltatásokat, mint beépített támogatása [Azure Functions](../azure-functions/functions-overview.md) és [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), és webhookokkal használata-Azure szolgáltatásokhoz való miatti riasztás biztosíthat. Az eseménykezelők esemény rács támogató teljes listáját lásd: [megismerkedhet az Azure Event rács][lnk-eg-overview]. 

![Azure Event rács architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az esemény rács integráció a régiókban, ahol támogatott esemény rács található IoT-központok érhető el. Régiók legfrissebb listáját lásd: [megismerkedhet az Azure Event rács][lnk-eg-overview]. 

## <a name="event-types"></a>Esemény típusa

Az IoT-központ teszi közzé a következő esemény típusa: 

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Ha az eszköz regisztrálva van az IoT-központ közzé. |
| Microsoft.Devices.DeviceDeleted | Ha egy eszközt az IoT-központ töröl közzé. | 

Az Azure portálon vagy az Azure CLI segítségével konfigurálhatja a közzététel minden egyes IoT-központ az események. Tegyük fel, próbálja ki az oktatóanyag [e-mail értesítések küldése a Logic Apps segítségével Azure IoT Hub eseményekről](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Eseményséma

Az IoT-központ események szüksége az eszközök életciklusának változásait összes információkat tartalmaznak. Azonosíthatja, hogy az IoT-központ esemény ellenőrzésével kezdetű a eventType tulajdonság **Microsoft.Devices**. Esemény rács esemény tulajdonságai használatával kapcsolatos további információkért lásd: a [esemény rács esemény séma](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Eszköz létrehozott séma

A következő példa bemutatja a séma, esemény létrehozása egy eszköz: 

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

Az egyes tulajdonságok részletes ismertetését lásd: [Azure esemény rács esemény séma az IoT-központ](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Események szűrése

IoT Hub esemény-előfizetések szűrhetők az események esemény típusa és az eszköz neve alapján. Az esemény rács munkálatok tárgy szűrők alapján **előtag** és **utótag** megegyezik. A szűrő egy `AND` operátor szerinti szűrése, így az előfizető kézbesíti az eseményeket egy témát, amelyek megfelelnek az előtag és az utótag. 

Az IoT-események tárgya formátumot használja:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Események felhasználásához tartozó tippek

Alkalmazások, amelyek kezelik az IoT-központ események ajánlott eljárások kell követnie:

* Több előfizetés beállítható úgy, hogy útvonal események az azonos az eseménykezelő, ezért fontos, hogy nem azt feltételezik, hogy eseményeket adott forrásból származnak. Mindig ellenőrizze az üzenetet a témakörhöz, és győződjön meg arról, hogy az IoT hub várt származik. 
* Nem érdemes feltételezni, hogy kap az összes esemény-e a várt típusú. Mindig ellenőrizze a eventType az üzenet feldolgozása előtt.
* Üzenetek érkezésekor is, nem megfelelő sorrendben vagy késleltetéssel. Az etag mezőjét megértse, hogy az objektumok adatait naprakész.



## <a name="next-steps"></a>További lépések

* [Tekintse meg az IoT-központ események oktatóanyag](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [További tudnivalók az esemény rács][lnk-eg-overview]
* [Hasonlítsa össze a útválasztási IoT Hub-események és üzenetek][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md