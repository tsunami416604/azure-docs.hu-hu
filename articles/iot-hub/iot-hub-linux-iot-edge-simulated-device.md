---
title: "Egy eszköz szimulálása Azure IoT oldala (Linux) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT peremhálózati Linux által küldött telemetriai adatokat az IoT-központ az IoT-Edge átjárón keresztül szimulált eszköz létrehozásához."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.openlocfilehash: 5349960373ae6815862c5f79a69dd6d5d9d624ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Segítségével Azure IoT peremhálózati eszköz-felhő üzenetek a szimulált eszköz (Linux)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>A minta futtatása

A **build.sh** szkript a kimenetét a **build** mappában hozza létre, az **iot-edge** adattár helyi példányában. A kimenetet a mintában használt négy IoT peremhálózati modulok tartalmazza.

A build script helyek a:

* **liblogger.so** a a **build/modulok/naplózó** mappát.
* **libiothub.so** a a **build/modulok/IOT hubbal** mappát.
* **lib\_identitás\_map.so** a a **build/modulok/identitymap** mappa.
* **libsimulated\_device.so** a a **build, modulok vagy szimulált\_eszköz** mappát.

Az elérési utak használata a **modul elérési útján** értékeket az alábbi JSON fájlban látható módon:

A szimulált\_eszköz\_felhő\_feltöltése\_minta tart az elérési út egy JSON-konfigurációs fájl parancssori argumentumként. A következő példa JSON-fájl megtalálható a következő SDK tárház **minták\\szimulált\_eszköz\_felhő\_feltöltése\_minta\\src\\ Szimulált\_eszköz\_felhő\_feltöltése\_minta\_lin.json**. A konfigurációs fájl, kivéve, ha módosítja a build parancsfájlt helyezze el az IoT peremhálózati modulok vagy a minta végrehajtható fájlok alapértelmezettől eltérő helyeket működik.

> [!NOTE]
> A modul elérési útvonalai képest a könyvtárat az futtató a szimulált\_eszköz\_felhő\_feltöltése\_minta végrehajtható fájl, nem a könyvtárat, amelyben a végrehajtható fájl található. A minta JSON-konfigurációs fájl az alapértelmezett az aktuális munkakönyvtárban a "deviceCloudUploadGatewaylog.log" írásakor.

Egy szövegszerkesztőben nyissa meg a fájlt **minták/szimulált\_eszköz\_felhő\_feltöltése\_minta/src/szimulált\_eszköz\_felhő\_feltöltése\_lin.json** a helyi példánya a **iot-edge** tárházba. Ez a fájl az IoT-Edge modulok konfigurálja a minta átjáró:

* A **IOT hubbal** modul csatlakozik az IoT hub. Konfigurálja az IoT hub adatküldéshez. Pontosabban, állítsa be a **IoTHubName** értékre az IoT hub nevét, és állítsa a **IoTHubSuffix** egy érték **azure-devices.net**. Állítsa be a **átviteli** egyik: **HTTP**, **AMQP**, vagy **MQTT**. Jelenleg csak **HTTP** közösen használja az összes eszközre küldött üzenetek egy TCP-kapcsolatot. Ha az érték **AMQP**, vagy **MQTT**, az átjáró egy külön TCP-kapcsolatot az IoT-központ az egyes eszközök tart fenn.
* A **leképezési** modul szimulált eszköz MAC-címet rendel az IoT Hub eszköz azonosítóját. Győződjön meg arról, hogy **deviceId** megegyeznek a két eszköz az IoT hub, és hogy a hozzáadott azonosítóit a **deviceKey** értékek a kulcsokat, a két eszközök tartalmazzák.
* A **BLE1** és **BLE2** modulokra a szimulált eszköz. Vegye figyelembe, hogyan a MAC-cím egyezik-e a címeket a **leképezési** modul.
* A **naplózó** modul fájlban naplózza a átjáró tevékenységeket.
* A **modul elérési útján** értékek, a példában látható módon azt feltételezik, hogy futtasson-e a mintát a **build** mappa a helyi példánya a **iot-edge** tárház.
* A **hivatkozások** tömb a JSON-fájl alján csatlakozik a **BLE1** és **BLE2** modulok a **leképezési** modul, és a **leképezési** modult a **IOT hubbal** modul. Emellett biztosítja, hogy az összes üzenet által naplózott a **naplózó** modul.

```json
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "01:01:01:01:01:01"
            }
          },
        {
            "name": "BLE2",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/simulated_device/libsimulated_device.so"
            }
            },
            "args": {
              "macAddress": "02:02:02:02:02:02"
            }
          },
        {
            "name": "Logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

A konfigurációs fájl módosításainak mentése.

A minta futtatásához:

1. A rendszerhéj navigáljon a **létrehozása, az iot-edge** mappát.
2. Futtassa az alábbi parancsot:
   
    ```sh
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Használhatja a [eszköz explorer] [ lnk-device-explorer] vagy [IOT hubbal-explorer] [ lnk-iothub-explorer] az üzeneteket, amelyek az IoT-központ fogadja az átjáró figyelésére. Például az IOT hubbal-Explorerben figyelheti eszköz-a-felhőbe küldött üzeneteket a következő parancsot:

    ```sh
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Következő lépések

Azure IoT peremhálózati tájékozottabbak kapnak, és néhány kódpéldák kísérletezhet, látogasson el az alábbi fejlesztői oktatóanyagok és erőforrások:

* [Eszköz-felhő üzeneteket küldeni egy fizikai eszközről Azure IoT oldala][lnk-physical-device]
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
