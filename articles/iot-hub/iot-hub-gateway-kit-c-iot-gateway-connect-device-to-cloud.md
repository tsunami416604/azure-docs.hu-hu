---
title: "Az IoT-átjáró segítségével egy eszköz csatlakoztatása az Azure IoT Hub |} Microsoft Docs"
description: "Megtudhatja, hogyan IoT átjáróként Intel NUC segítségével csatlakozzon a TI SensorTag és érzékelő adatokat küldeni a felhőben Azure IoT-központot."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az IOT-átjáró felhőbe eszköz csatlakoztatása"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: 4fb77ed0241d15338c2574fd22828507c3e40cb3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Az IoT-átjáró használata dolgot csatlakozni a felhő - SensorTag Azure IoT hubhoz

> [!NOTE]
> Ez az oktatóanyag megkezdése előtt győződjön meg arról, hogy befejezte [Intel NUC IoT átjáróként beállítva](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). A [Intel NUC IoT átjáróként beállítva](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), úgy konfigurálja az Intel NUC eszközt az IoT átjáróként.

## <a name="what-you-will-learn"></a>Amiről tanulni fog

Megismerheti, hogyan egy IoT-átjáró használatához Azure IoT-központot egy Texas eszközök SensorTag (CC2650STK) kapcsolódni. Az IoT-átjáró adatokat küld a hőmérséklet és a páratartalom összegyűjtött a SensorTag az Azure IoT-központot.

## <a name="what-you-will-do"></a>Mit fog

- Létrehoz egy IoT-központot.
- A SensorTag az IoT hub eszköz regisztrálása
- Engedélyezze a kapcsolatot az IoT-átjáró és a SensorTag.
- SensorTag adatokat küldeni az IoT hub BLA mintaalkalmazás futtatása.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [Intel NUC IoT átjáróként beállítva](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) befejeződött a amely Intel NUC IoT átjáróként beállítása.
- * Aktív Azure-előfizetés. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
- Egy SSH-ügyfél rendszert futtató számítógépen. A puTTY Windows ajánlott. Linux- és macOS egy SSH-ügyfél már rendelkeznek.
- Az IP-cím és a felhasználónév és jelszó megadásával érheti el az átjárót az SSH-ügyfél.
- Az internethez.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Itt az új eszköz regisztrálásához a SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Az IoT-átjáró és a SensorTag közötti kapcsolat engedélyezése

Ebben a szakaszban hajtsa végre a következő feladatokat:

- Bluetooth-kapcsolat a MAC-címét a SensorTag beolvasása.
- Az IoT-átjáró a SensorTag a Bluetooth-kapcsolat kezdeményezéséhez.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>A MAC-címét a SensorTag lekérése Bluetooth-kapcsolat

1. Az állomáson az SSH-ügyfél, és az IoT-átjárón.
1. Bluetooth feloldása a következő parancs futtatásával:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. A Bluetooth-szolgáltatás elindítása az IoT-átjárón, és írja be a Bluetooth rendszerhéjat Bluetooth konfigurálása a következő parancsok futtatásával:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. A Bluetooth-vezérlő a következő parancs futtatásával a Bluetooth rendszerhéj bekapcsolása:

   ```bash
   power on
   ```

   ![a Bluetooth-vezérlő az IoT-átjárón a bluetoothctl bekapcsolása](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Indítsa el a következő parancs futtatásával közeli Bluetooth-eszközök keresése:

   ```bash
   scan on
   ```

   ![Bluetooth-eszközök a bluetoothctl közelben vizsgálata](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Nyomja meg a SensorTag párosítási gombját. A zöld a SensorTag villanás a vezetett.
1. A Bluetooth rendszerhéj, meg kell jelennie a SensorTag megtalálható-e. Jegyezze fel a SensorTag MAC-címét. Ebben a példában a SensorTag a MAC-címe: `24:71:89:C0:7F:82`.
1. Kapcsolja ki a vizsgálat a következő parancs futtatásával:

   ```bash
   scan off
   ```

   ![Állítsa le a közeli bluetoothctl a Bluetooth-eszközök keresése](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Az IoT-átjáró a SensorTag a Bluetooth-kapcsolat kezdeményezéséhez

1. A SensorTag csatlakoztatása a következő parancs futtatásával:

   ```bash
   connect <MAC address>
   ```

   ![Csatlakozás a bluetoothctl SensorTag](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Válassza le a SensorTag, és zárja be a Bluetooth rendszerhéj a következő parancsok futtatásával:

   ```bash
   disconnect
   exit
   ```

   ![Válassza le a bluetoothctl SensorTag](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Sikeresen engedélyezte a SensorTag és az IoT-átjáró közötti kapcsolat.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>SensorTag adatokat küldeni az IoT hub BLA mintaalkalmazás futtatása

A Bluetooth alacsony energia (int) mintaalkalmazás Azure IoT peremhálózati biztosítja. A mintaalkalmazás BLA kapcsolat gyűjti az adatokat, és elküldheti az adatokat az IoT hub. Futtassa a mintaalkalmazást, kell:

1. A mintaalkalmazás konfigurálása.
1. Futtassa a mintaalkalmazást az IoT-átjárón.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Ugrás a mintaalkalmazásnak a mappa a következő parancs futtatásával:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. Nyissa meg a konfigurációs fájlt a következő parancs futtatásával:

   ```bash
   vi ble_gateway.json
   ```

1. A konfigurációs fájlban töltse ki a következő értékeket:

   **IoTHubName**: az IoT hub nevét.

   **IoTHubSuffix**: IoTHubSuffix beolvasása az eszköz kapcsolati karakterlánc az elsődleges kulcs le feljegyzett. Biztosítják, hogy az eszköz kapcsolati karakterláncot, az IoT hub kapcsolati karakterlánc nem az elsődleges kulcs az elsődleges kulcs. Az eszköz kapcsolati karakterlánc az elsődleges kulcs van formátumban `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Átviteli**: az alapértelmezett érték `amqp`. Ez az érték a protokoll során transpotation tartalmazza. Annak oka az lehet `http`, `amqp`, vagy `mqtt`.

   **macAddress**: a SensorTag feljegyzett le a MAC-címét.

   **deviceID**: az eszköz az IoT hub létrehozott azonosítója.

   **deviceKey**: az eszköz kapcsolati karakterlánc az elsődleges kulcs.

   ![Fejezze be a konfigurációs fájl a Gedélyezése mintaalkalmazásnak](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Nyomja le az `ESC` és típus `:wq` fájl mentéséhez.

### <a name="run-the-sample-application"></a>Futtassa a mintaalkalmazást

1. Győződjön meg arról, hogy a SensorTag be van kapcsolva.
1. Futtassa az alábbi parancsot:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Következő lépések

[Az IoT-átjáró használata az érzékelő adatokat átalakításhoz Azure IoT oldala](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)
