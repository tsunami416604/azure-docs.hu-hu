---
title: Azure IoT Hub és Eseményhálózat | Microsoft dokumentumok
description: Az Azure Event Grid használatával az IoT Hubban végrehajtott műveletek en alapuló folyamatokat indíthat el.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a67d90a0888c39938f07c294f8e161ce98fd945a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732499"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagálás az IoT Hub-eseményekre az Event Grid használatával műveletek et indíthat el

Az Azure IoT Hub integrálható az Azure Event Griddel, így eseményértesítéseket küldhet más szolgáltatásoknak, és elindíthatja az alsóbb rétegbeli folyamatokat. Állítsa be az üzleti alkalmazásokat az IoT Hub-események figyelésére, hogy megbízható, méretezhető és biztonságos módon reagálhasson a kritikus eseményekre.Például hozzon létre egy alkalmazást, amely frissíti az adatbázist, létrehoz egy munkajegyet, és egy e-mail értesítést minden alkalommal, amikor egy új IoT-eszköz regisztrálva van az IoT hub.

[Az Azure Event Grid](../event-grid/overview.md) egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi-előfizetési modellt használ. Az Event Grid beépített támogatást nyújt az Azure-szolgáltatásokhoz, például az [Azure Functionshez](../azure-functions/functions-overview.md) és az [Azure Logic Appsekhez,](../logic-apps/logic-apps-what-are-logic-apps.md)és webhookok használatával eseményriasztásokat tud kézbesíteni a nem Azure-szolgáltatásokhoz. Az Event Grid által támogatott eseménykezelők teljes listáját az [Azure Event Grid bemutatkozása](../event-grid/overview.md)című témakörben található.

![Azure Event Grid architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Event Grid-integráció érhető el az IoT-központok található régiókban, ahol Event Grid támogatott. A régiók legújabb listáját az [Azure Event Grid bemutatkozása](../event-grid/overview.md)című témakörben található.

## <a name="event-types"></a>Eseménytípusok

Az IoT Hub a következő eseménytípusokat teszi közzé:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Akkor közzétéve, ha egy eszköz regisztrálva van egy IoT-központhoz. |
| Microsoft.Devices.DeviceDeleted | Akkor közzétéve, ha egy eszközt törölnek egy IoT-központból. |
| Microsoft.Devices.DeviceConnected | Akkor jelenik meg, ha egy eszköz egy IoT-központhoz csatlakozik. |
| Microsoft.Devices.DeviceLeed | Akkor közzétéve, ha egy eszköz le van választva egy IoT-központról. |
| Microsoft.Devices.DeviceTelemetria | Akkor közzétéve, amikor egy eszköz telemetriai üzenetét elküldik egy IoT-központnak |

Az Azure Portalon vagy az Azure CLI-ben konfigurálhatja, hogy mely eseményeket tegye közzé az egyes IoT-központokból. Például próbálja meg az oktatóanyag [E-mail értesítések küldése az Azure IoT Hub-események logic apps használatával.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

## <a name="event-schema"></a>Eseményséma

Az IoT Hub-események tartalmazzák az összes olyan információt, amely az eszköz életciklusában bekövetkező változásokra való válaszadáshoz szükséges. Az IoT Hub-események et úgy azonosíthatja, hogy ellenőrzi, hogy az eventType tulajdonság a **Microsoft.Devices**programmal kezdődik-e. Az Event Grid eseménytulajdonságainak használatáról az [Eseményrács eseménysémájában](../event-grid/event-schema.md)talál további információt.

### <a name="device-connected-schema"></a>Eszközhöz csatlakoztatott séma

A következő példa egy csatlakoztatott eszközséma látható:

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

### <a name="device-telemetry-schema"></a>Eszköztelemetriai séma

Az eszköztelemetriai üzenetnek érvényes JSON formátumban kell lennie, és a contentType **beállítása alkalmazás/json,** a contentEncoding pedig **UTF-8-ra** van állítva az [üzenetrendszer tulajdonságaiközött.](iot-hub-devguide-routing-query-syntax.md#system-properties) Mindkét tulajdonság nem szokja a kis- és nagybetűket. Ha a tartalomkódolás nincs beállítva, majd az IoT Hub alap 64 kódolású formátumban írja az üzeneteket.

Az eseményrácsban való közzétételük előtt gazdagíthatja az eszköz telemetriai eseményeit, ha a végpontot Eseményrácsként választja ki. További információt az [Üzenetgazdagodások – áttekintés című témakörben talál.](iot-hub-message-enrichments-overview.md)

A következő példa egy eszköz telemetriai eseményének sémáját mutatja be:

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

### <a name="device-created-schema"></a>Eszköz létrehozott séma

A következő példa egy létrehozott eszköz sémáját mutatja be:

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

Az egyes tulajdonok részletes leírását az [Azure Event Grid-eseményséma az IoT Hub.](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Események szűrése

Az IoT Hub esemény-előfizetések szűrheti események eseménytípus, adattartalom és a tárgy, amely az eszköz neve.

Az Event Grid lehetővé teszi az eseménytípusok, a témák és az adattartalom [szűrését.](../event-grid/event-filtering.md) Az Event Grid-előfizetés létrehozása közben dönthet úgy, hogy előfizet a kiválasztott IoT-eseményekre. Az Event Grid ben a **tárgyszűrők** a Begins With (előtag) és **a Végződik** (utótag) egyezések alapján működnek. A szűrő `AND` operátort használ, így az előtagnak és utótagnak megfelelő tanusszal rendelkező események az előfizetőhöz kerülnek.

Az IoT-események tárgya a következő formátumot használja:

```json
devices/{deviceId}
```

Az Event Grid lehetővé teszi az egyes események attribútumainak szűrését is, beleértve az adattartalmat is. Ez lehetővé teszi, hogy válassza ki, milyen események kézbesítve a telemetriai üzenet tartalma. A példák megtekintéséhez tekintse meg a [speciális szűrést.](../event-grid/event-filtering.md#advanced-filtering) A telemetriai üzenet törzsének szűréséhez be kell állítania a contentType-ot **alkalmazás/json** ra és contentEncoding-t **UTF-8-ra** az [üzenetrendszer tulajdonságaiközött.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties) Mindkét tulajdonság nem szokja a kis- és nagybetűket.

A nem telemetriai események, például a DeviceConnected, DeviceDisconnected, DeviceCreated és DeviceDeleted, az Event Grid szűrés az előfizetés létrehozásakor használható. Telemetriai események, az Event Grid szűrése mellett a felhasználók is szűrheti az eszköz twins, üzenet tulajdonságai és törzse az üzenet-útválasztási lekérdezésen keresztül. 

Amikor előfizet telemetriai eseményekre az Event Griden keresztül, az IoT Hub létrehoz egy alapértelmezett üzenetútvonalat az adatforrástípusú eszközüzenetek eseményrácsba küldéséhez. Az üzenetek útválasztásáról az [IoT Hub üzenetútválasztáscímű](iot-hub-devguide-messages-d2c.md)témakörében talál további információt. Ez az útvonal az IoT Hub > üzenetútválasztás a portálon lesz látható. Csak egy útvonal at Event Grid jön létre, függetlenül atagépi eseményekhez létrehozott EG-előfizetések száma. Így ha több előfizetésre van szüksége különböző szűrőkkel, használhatja a VAGY operátort ezekben a lekérdezésekben ugyanazon az útvonalon. Az útvonal létrehozása és törlése az Eseményrácson keresztüli telemetriai események előfizetésével szabályozható. Az IoT-központ üzenetútválasztásával nem hozhat létre és nem törölhet útvonalat az Eseményrácshoz.

Ha a telemetriai adatok elküldése előtt szűrni szeretné az üzeneteket, frissítheti az [útválasztási lekérdezést.](iot-hub-devguide-routing-query-syntax.md) Vegye figyelembe, hogy az útválasztási lekérdezés csak akkor alkalmazható az üzenettörzsre, ha a törzs JSON. A contentType-ot **alkalmazás/json** ra, a contentEncoding-t pedig **UTF-8-ra** kell állítania az [üzenetrendszer tulajdonságai](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)között.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Csatlakoztatott és nem csatlakoztatott eszközökhöz kapcsolódó események korlátozásai

Az eszközkapcsolat állapoteseményeinek fogadásához az eszköznek vagy "D2C Telemetriai adatok küldése" vagy "C2D üzenetfogadási üzenet" műveletet kell végeznie az Iot Hubbal. Ne feledje azonban, hogy ha egy eszköz AMQP protokollt használ az Iot Hubhoz való csatlakozáshoz, ajánlott "C2D receive message" műveletet végezni, ellenkező esetben a kapcsolatállapot-értesítések néhány perccel késhetnek. Ha az eszköz MQTT protokollt használ, az IoT Hub nyitva tartja a C2D-hivatkozást. Az AMQP esetében megnyithatja a C2D-kapcsolatot a [Receive Async API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), az IoT Hub C# SDK vagy [az AMQP eszközügyfél](iot-hub-amqp-support.md#device-client)meghívásával.

A D2C-kapcsolat meg van nyitva, ha telemetriai adatokat küld. 

Ha az eszközkapcsolat villog, ami azt jelenti, hogy az eszköz gyakran csatlakozik és bontja a kapcsolatot, nem küldünk minden egyes kapcsolati állapotot, hanem rendszeres pillanatképen közzétesszük az aktuális kapcsolatállapotát, amíg a villódzás nem folytatódik. Ugyanaz a kapcsolatállapot-esemény fogadása különböző sorszámokkal vagy különböző kapcsolatállapot-eseményekkel egyaránt azt jelenti, hogy az eszköz kapcsolati állapota megváltozott.

## <a name="tips-for-consuming-events"></a>Tippek az események használatához

Az IoT Hub-eseményeket kezelő alkalmazásoknak az alábbi javasolt eljárásokat kell követniük:

* Több előfizetés konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa, ezért ne feltételezze, hogy az események egy adott forrásból származnak. Mindig ellenőrizze az üzenet témakörét, hogy megbizonyosodjon arról, hogy a várt IoT hubról származik.

* Ne feltételezze, hogy minden kapott esemény a várt típusú. Az üzenet feldolgozása előtt mindig ellenőrizze az eventType típust.

* Az üzenetek nem sorrendben vagy késleltetés után érkezhetnek. Az etag mező segítségével megtudhatja, hogy az objektumokkal kapcsolatos adatai naprakészek-e az eszköz által létrehozott vagy az eszköz által törölt eseményekhez.

## <a name="next-steps"></a>További lépések

* [Próbálja ki az IoT Hub-események oktatóanyagát](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](iot-hub-how-to-order-connection-state-events.md)

* [További információ az Eseményrácsról](../event-grid/overview.md)

* [Az IoT Hub-események és az üzenetek útválasztása közötti különbségek összehasonlítása](iot-hub-event-grid-routing-comparison.md)

* [Megtudhatja, hogy miként valósíthatja meg az IoT térbeli elemzéseket az IoT-térbeli elemzés megvalósításához az Azure Maps használatával](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
