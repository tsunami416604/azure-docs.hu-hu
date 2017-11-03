---
title: "Egy eszköz szimulálása Azure IoT oldala (Windows) |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet Windows Azure IoT peremhálózati használatával létrehozni a szimulált eszköz által küldött telemetriai adatokat az IoT-központ az Azure IoT peremhálózati átjárón keresztül."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Segítségével Azure IoT peremhálózati eszköz-felhő üzenetek a szimulált eszköz (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>A minta futtatásához

A **build.cmd** parancsfájlt hoz létre a kimenetét a **build** mappa a helyi példánya a **iot-edge** tárház. A kimenetet a mintában használt négy IoT peremhálózati modulok tartalmazza.

A build parancsfájl a következő fájlokat hozza létre:

* **Logger.dll** a a **build\\modulok\\naplózó\\Debug** mappát.
* **iothub.dll** a a **build\\modulok\\IOT hubbal\\Debug** mappát.
* **identitás\_map.dll** a a **build\\modulok\\identitymap\\Debug** mappát.
* **Szimulált\_device.dll** a a **build\\modulok\\szimulált\_eszköz\\Debug** mappát.

Az elérési utak használata a **modul elérési útján** értékei, ahogy az a szimulált\_eszköz\_felhő\_feltöltése\_win JSON beállításfájlt.

A szimulált\_eszköz\_felhő\_feltöltés minta tart az elérési út egy JSON-konfigurációs fájl parancssori argumentumként. A következő példa JSON-fájl megtalálható a következő SDK tárház **minták\\szimulált\_eszköz\_felhő\_feltöltése\_minta\\src\\ Szimulált\_eszköz\_felhő\_feltöltése\_win.json**. A konfigurációs fájl, kivéve, ha módosítja a build parancsfájlt helyezze el az IoT peremhálózati modulok vagy a minta végrehajtható fájlok alapértelmezettől eltérő helyeket működik.

> [!NOTE]
> A modul elérési útvonalai képest a könyvtár ahol a szimulált\_eszköz\_felhő\_feltöltése\_sample.exe helyezkedik el. A minta JSON-konfigurációs fájl az alapértelmezett az aktuális munkakönyvtárban a "deviceCloudUploadGatewaylog.log" írásakor.

Egy szövegszerkesztőben nyissa meg a fájlt **minták\\szimulált\_eszköz\_felhő\_feltöltése\\src\\szimulált\_eszköz\_felhő\_feltöltése\_win.json** a helyi példánya a **iot-edge** tárházba. Ez a fájl az IoT-Edge modulok konfigurálja a minta átjáró:

* A **IOT hubbal** modul csatlakozik az IoT hub. Konfigurálja az IoT hub adatküldéshez. Pontosabban, állítsa be a **IoTHubName** értékre az IoT hub nevét, és állítsa a **IoTHubSuffix** egy érték **azure-devices.net**. Állítsa be a **átviteli** egyik: **HTTP**, **AMQP**, vagy **MQTT**. Jelenleg csak **HTTP** közösen használja az összes eszközre küldött üzenetek egy TCP-kapcsolatot. Ha az érték **AMQP**, vagy **MQTT**, az átjáró egy külön TCP-kapcsolatot az IoT-központ az egyes eszközök tart fenn.
* A **leképezési** modul szimulált eszköz MAC-címet rendel az IoT-központ eszközazonosítók. Állítsa be a **deviceId** az IoT hub felvette a két eszköz azonosítóinak értékeit. Állítsa be a **deviceKey** a kulcsokhoz, a két eszközök értékeket.
* A **BLE1** és **BLE2** modulokra a szimulált eszköz. Vegye figyelembe, hogyan modul MAC-címek felel meg a címeket a **leképezési** modul.
* A **naplózó** modul fájlban naplózza a átjáró tevékenységeket.
* A **modul elérési útján** az alábbi példában látható módon értékei a könyvtár viszonyítva ahol a szimulált\_eszköz\_felhő\_feltöltése\_sample.exe helyezkedik el.
* A **hivatkozások** tömb a JSON-fájl alján csatlakozik a **BLE1** és **BLE2** modulok a **leképezési** modul, és a **leképezési** modult a **IOT hubbal** modul. Emellett biztosítja, hogy az összes üzenet által naplózott a **naplózó** modul.

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

A konfigurációs fájl módosításainak mentése.

A minta futtatásához:

1. A parancssorban navigáljon a **build** mappa a helyi példánya a **iot-edge** tárház.
2. Futtassa az alábbi parancsot:
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Használhatja a [eszköz explorer] [ lnk-device-explorer] vagy [IOT hubbal-explorer] [ lnk-iothub-explorer] az üzeneteket, amelyek az IoT-központ fogadja az átjáró figyelésére. Például az IOT hubbal-Explorerben figyelheti eszköz-a-felhőbe küldött üzeneteket a következő parancsot:

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Következő lépések

IoT peremhálózati tájékozottabbak kapnak, és néhány kódpéldák kísérletezhet, látogasson el az alábbi fejlesztői oktatóanyagok és erőforrások:

* [Eszköz-felhő üzeneteket küldeni egy fizikai eszközről IoT oldala][lnk-physical-device]
* [Az Azure IoT él][lnk-iot-edge]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Az IoT-megoldásból az alapoktól biztonságos mentése][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
