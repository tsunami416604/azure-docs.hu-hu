---
title: "Fizikai eszköz használata az Azure IoT peremhálózati |} Microsoft Docs"
description: "Hogyan lehet egy Texas eszközök SensorTag eszköz segítségével adatokat küldeni az IoT-központ egy málna Pi 3 eszközön futó IoT peremhálózati átjárón keresztül. Az átjáró-Azure IoT peremhálózati t."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: andbuc
ms.openlocfilehash: b24828ee1a09ba8e5f657954e11936f124270173
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="forward-device-to-cloud-messages-to-iot-hub-using-azure-iot-edge-on-a-raspberry-pi"></a>Az IoT-központ Azure IoT Edge használata a málna Pi üzenetek továbbítására eszközről a felhőbe

Ez a forgatókönyv a [Bluetooth alacsony energia minta] [ lnk-ble-samplecode] bemutatja, hogyan használható [Azure IoT peremhálózati] [ lnk-sdk] való:

* Eszköz-felhő telemetriai továbbítja az IoT-központ fizikai eszközről.
* Útvonal parancsokat az IoT-központ fizikai eszköz.

A bemutató tartalma:

* **Architektúra**: a Bluetooth alacsony energia minta fontos architekturális információkat.
* **Létrehozás és futtatás**: A minta elkészítéséhez és futtatásához szükséges lépések.

## <a name="architecture"></a>Architektúra

A bemutató ismerteti, hogyan lehet létrehozni, és futtassa az IoT-átjárónak egy málna Pi 3 Raspbian Linux futtató. Az átjáró-IoT peremhálózati t. A minta egy Texas eszközök SensorTag Bluetooth alacsony energia (int) eszközt használ hőmérséklet-adatok gyűjtéséért felelős ügyfélfeladatot.

Az IoT-átjárónak futtatásakor azt:

* A Bluetooth alacsony energia (int) protokollt használó SensorTag eszköz csatlakozik.
* Az IoT-központ HTTPS protokollon keresztül kapcsolódik.
* Telemetria továbbítja az SensorTag eszközről IoT-központot.
* Útvonalak parancsok az IoT-központ az SensorTag eszközre.

Az átjáró a következő IoT peremhálózati modulokat tartalmaz:

* A *BLA modul* , amely kapcsolódási pontok Generálja az eszköz hőmérséklet adatfogadás az eszközről, és az eszköz parancsainak elküldését.
* A *BLA felhő eszköz modulra* JSON üzenetek küldi az IoT-központ BLA utasításokat a következőkből fordítja le a *BLA modul*.
* A *naplózó modul* , amely az összes átjáró üzenet helyi fájlban naplózza.
* Egy *identitás hozzárendelési modul* , amely átalakítja az int eszköz MAC-címek és Azure IoT Hub eszköz identitásokat.
* Egy *IoT-központ modul* , telemetriai adatokat feltölt egy IoT-központot, és eszközparancsok kapott az IoT-központ.
* A *BLA nyomtató modul* , amely az int eszközről telemetriai értelmezi, és megrendelése formázott adatok hibaelhárítás és hibakeresés engedélyezése a konzolhoz.

### <a name="how-data-flows-through-the-gateway"></a>Hogyan adatáramlás az átjárón keresztül

A következő blokk ábra szemlélteti a telemetriai adatok feltöltési adatok folyamata folyamat:

![Telemetriai adatainak feltöltése átjáró folyamat](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

A lépéseket, amelyek telemetriai elem időt vesz igénybe, IoT-központot egy BLA eszközről utazik a következők:

1. Az int eszköz hőmérséklet minta hoz létre, és elküldi Bluetooth-on keresztül az átjáró BLA moduljának.
1. A táblázat modul megkapja a minta, és közzéteszi azokat az átvitelszervező mellett az eszköz MAC-címét.
1. Az identitás hozzárendelési modul szerzi be ezt az üzenetet, és egy belső tábla segítségével az eszköz MAC-címet jelenti azt, hogy az IoT-központ eszközidentitás. Az IoT-központ eszközidentitás Eszközazonosító és eszközkulcs áll.
1. Az identitás hozzárendelési modul tesz közzé egy új üzenet, amely tartalmazza a következő információkat:
   - Hőmérséklet-mintaadatok
   - Az eszköz MAC-címe
   - Az eszköz azonosítója
   - Az eszköz kulcsa  
1. Az IoT-központ modulja (identity hozzárendelési modul által előállított) az új üzenetet kap, és közzéteszi azokat az IoT-központ.
1. A naplózási modul összes üzenetet a broker egy helyi fájlba naplózza.

A következő blokk ábra szemlélteti a eszköz parancs adatok áramlási folyamat:

![Eszköz parancs átjáró folyamat](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. Az IoT-központ modul rendszeres időközönként lekérdezi az IoT hub új parancs üzenetek.
1. Az IoT-központ modul új parancs üzenetet kap, ha azt közzé teszi azt az átvitelszervező.
1. Az identitás hozzárendelési modul szerzi be a parancs üzenetet, és egy belső tábla lefordítani az IoT-központ Eszközazonosítót az eszköz MAC-címet használ. Majd egy új üzenet, amely tartalmazza a célként megadott eszköz MAC-cím az üzenet tulajdonságai térképen tesz közzé.
1. A lehetséges felhő eszköz modul szerzi be ezt az üzenetet, és fordítja le azt a megfelelő BLA utasítás BLA modul. Majd egy új üzenet tesz közzé.
1. A táblázat modul szerzi be ezt az üzenetet, és végrehajtja az i/o-utasítás által a BLA eszköz kommunikál.
1. A naplózási modul naplózza az összes üzenet az átvitelszervező egy lemezen levő fájlra.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

SSH-ügyfél van szüksége az asztali gépen ahhoz, hogy a parancssor a málna Pi a érheti el távolról.

- A Windows tartalmaz egy SSH-ügyfél. Azt javasoljuk, [PuTTY](http://www.putty.org/).
- A legtöbb Linux terjesztéseket, a Mac OS közé tartoznak az SSH parancssori segédprogramot. További információkért lásd: [SSH használatával Linux és Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

## <a name="prepare-your-hardware"></a>Készítse elő a hardvert

Ez az oktatóanyag feltételezi, hogy a egy [Texas eszközök SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) egy málna Pi 3 Raspbian futtató eszköz csatlakoztatva.

### <a name="install-raspbian"></a>Raspbian telepítése

Segítségével az alábbi lehetőségek valamelyikét Raspbian az málna Pi 3 eszközökre telepíthető.

* Raspbian legújabb verziójának telepítéséhez használja a [NOOBS] [ lnk-noobs] grafikus felhasználói felületen.
* Manuálisan [letöltése] [ lnk-raspbian] és a legújabb a Raspbian operációs rendszer lemezképének írni SD-kártya.

### <a name="sign-in-and-access-the-terminal"></a>Bejelentkezhet és elérheti a Terminálszolgáltatások

A Terminálszolgáltatások tesztkörnyezetben, a málna Pi eléréséhez két lehetőség közül választhat:

* Ha a figyelő a málna Pi csatlakoztatott és a billentyűzeten, használhatja a Raspbian grafikus felhasználói felület egy terminálablakot eléréséhez.

* A parancssorban meg az SSH használata a asztali gépen málna Pi eléréséhez.

#### <a name="use-a-terminal-window-in-the-gui"></a>A grafikus felhasználói felületen terminálablakot használni

Az alapértelmezett Raspbian a hitelesítő adatok felhasználónév **pi** és a jelszó **málna**. A tálcán a grafikus felhasználói felületén, elindíthatja a **Terminálszolgáltatások** segédprogram használatával egy figyelőt a ikon.

#### <a name="sign-in-with-ssh"></a>SSH bejelentkezés

SSH használható parancssori hozzáférést a málna Pi. A cikk [SSH (Secure Shell)] [ lnk-pi-ssh] útmutatás a málna Pi SSH konfigurálása, valamint csatlakozhat a [Windows] [ lnk-ssh-windows] vagy [ Linux és Mac OS][lnk-ssh-linux].

Jelentkezzen be a felhasználónevet **pi** és a jelszó **málna**.

### <a name="install-bluez-537"></a>BlueZ 5.37 telepítése

A táblázat modulok konzultáljon a Bluetooth hardver a BlueZ vermen keresztül. A megfelelő működéséhez a modulok BlueZ 5.37 verziójára van szükség. Ezek az utasítások ellenőrizze, hogy BlueZ megfelelő verziója telepítve van.

1. Állítsa le a jelenlegi bluetooth démon:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Telepítse a BlueZ függőségek:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf libtool glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Töltse le a BlueZ forráskód bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Bontsa ki a forráskód:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Módosítsa a könyvtárat az újonnan létrehozott mappa:

    ```sh
    cd bluez-5.37
    ```

1. Adja meg a kialakítani BlueZ kódot:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Build BlueZ:

    ```sh
    make
    ```

1. Telepítse a BlueZ, ha ezzel végzett építési:

    ```sh
    sudo make install
    ```

1. Systemd-szolgáltatás konfigurációjának módosítása a Bluetooth-on, így az új bluetooth démon a fájlban mutat `/lib/systemd/system/bluetooth.service`. A "ExecStart" sorban cserélje le a következő szöveget:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>A SensorTag eszközre kapcsolódásának engedélyezése a málna Pi 3 eszközről

A minta futtatásához vissza kell igazolnia, hogy a málna Pi 3 a SensorTag eszköz csatlakozhat.

1. Győződjön meg arról a `rfkill` segédprogram:

    ```sh
    sudo apt-get install rfkill
    ```

1. A málna Pi 3 bluetooth feloldása, és ellenőrizze, hogy a verziószáma **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Adja meg az interaktív bluetooth rendszerhéj, a bluetooth-szolgáltatás elindítása és hajtható végre a **bluetoothctl** parancs:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Adja meg a parancsot **bekapcsolási** energiagazdálkodással fel a Bluetooth-vezérlő. A parancs kimenetét adja vissza a következőhöz hasonló:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. Az interaktív bluetooth felületet, adja meg a parancs **vizsgálhat** szolgáltatást a Bluetooth-eszközök. A parancs kimenetét adja vissza a következőhöz hasonló:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. A SensorTag eszköz felderíthető ügyeljen a kis gombra kattintva (a zöld LED flash kell). A málna Pi 3 kell felderíteni a SensorTag eszköz:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    Ebben a példában látható, hogy a MAC-cím a SensorTag eszköz **A0:E6:F8:B5:F6:00**.

1. Kapcsolja ki a megadásával ellenőrzését a **ki vizsgálata** parancs:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Csatlakozás egy SensorTag-eszközt a MAC-cím megadásával **csatlakozás \<MAC-cím\>**. A következő minta kimenet az átláthatóság rövidítése:

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Az eszköz újra GATT jellemzői listázhatja a **lista-attribútumok** parancsot.

1. Most már leválaszthatja a eszköz a a **leválasztása** parancsot, és zárja be a rendszerhéj a bluetooth a **lépjen ki a** parancs:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Most már készen áll a lehetséges IoT peremhálózati minta futtatásához a málna Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Futtathatja a IoT peremhálózati BLA

Az IoT peremhálózati BLA minta futtatásához kell három feladatok elvégzéséhez:

* Adja meg az IoT Hub két minta eszköz.
* Build IoT peremhálózati málna Pi 3 eszközén.
* Konfigurálja, és futtathatja a BLA málna Pi 3 eszközén.

Írásának időpontjában IoT peremhálózati csak támogatja BLA modulok Linux rendszert futtató átjárókat.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Az IoT Hub két minta eszközök konfigurálása

* [Létrehoz egy IoT-központot] [ lnk-create-hub] az Azure-előfizetéséhez, a nevét a hub forgatókönyv végrehajtásához szükség van. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* Hozzáad egy eszközt nevű **SensorTag_01** a IoT-központot, és jegyezze fel az azonosítót és az eszköz kulcsának. Használhatja a [eszköz explorer vagy az IOT hubbal-explorer] [ lnk-explorer-tools] eszközöket, az eszköz hozzáadása az IoT-központ az előző lépésben létrehozott lekérni a kulcsot. Ez az eszköz hozzárendelését az SensorTag eszközre az átjáró konfigurálásakor.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Az Azure IoT peremhálózati létrehozása a Raspberry Pi 3-kiszolgálón

Függőségek telepítése Azure IoT szegély:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev libtool
```

Az alábbi parancsokkal IoT széle és az kezdőkönyvtárához összes submodules klónozása:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Ha rendelkezik a málna Pi 3 IoT peremhálózati összetevőtárházat teljes másolata, hozhat létre a következő parancs használatával, amely tartalmazza az SDK mappából:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Konfigurálja és futtathatja a Generálja a málna Pi 3

Bootstrap, és futtathatja, adja meg minden egyes IoT peremhálózati modul, amely részt vesz az átjárót. Ebben a konfigurációban megadott JSON-fájlt, és konfigurálnia kell az öt részt vevő IoT peremhálózati modulok. A tárház nevű minta JSON-fájl van **átjáró\_sample.json** használható kiindulási pontként a saját konfigurációs fájl készítéséhez. A fájl a **minták/ble_gateway/src** helyi másolat készítése az IoT-Edge tárház mappájában.

Az alábbi szakaszok azt ismertetik, hogyan ezt a konfigurációs fájlt a lehetséges minta szerkesztése. Feltételezzük, hogy az IoT-Edge tárház van a **/home/pi/iot-edge /** a málna Pi 3 mappájába. Ha a tárház máshol, ennek megfelelően állítsa be az elérési utakat.

#### <a name="logger-configuration"></a>Naplózási konfiguráció

Feltéve, hogy az átjáró tárházban található a **/home/pi/iot-edge /** mappa, a naplózási modul konfigurálása az alábbiak szerint:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>BLA modul konfigurációja

A minta konfigurációs BLA eszköz azt feltételezi, hogy egy Texas eszközök SensorTag eszközt. Bármely szabványos BLA eszköz, amely működhet, a perifériák GATT kell működnie, de frissítésére lehet szükség a GATT jellemző azonosítók és adatokat. Adja hozzá a SensorTag eszköz MAC-címe:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Ha nem használ egy SensorTag eszközt, tekintse át a meghatározásához, hogy szükséges-e frissíteni a GATT jellemző azonosítókat és az adatértékek lehetséges eszköz dokumentációját.

#### <a name="iot-hub-module"></a>Az IoT-központ modulja

Adja hozzá az IoT Hub nevét. A utótag érték általában **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Identitás-hozzárendelési modul konfigurációja

Adja hozzá a MAC-címet az SensorTag eszköz eszköz azonosítója és kulcsa a **SensorTag_01** felvette az IoT Hub eszköz:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>BLA nyomtató modul konfigurációja

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>BLEC2D modul konfigurációja

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Útválasztási konfigurációja

A következő konfigurációs biztosítja a következő útválasztási IoT peremhálózati modulok között:

* A **naplózó** modul kap, és minden üzenetet naplózza.
* A **SensorTag** modul üzeneteket küld mind a **leképezési** és **BLA nyomtató** modulok.
* A **leképezési** modul üzeneteket küld a **IOT hubbal** küldendő az IoT Hub modul.
* A **IOT hubbal** modul küld vissza üzenetek a **leképezési** modul.
* A **leképezési** modul üzeneteket küld a **BLEC2D** modul.
* A **BLEC2D** modul küld vissza üzenetek a **Sensor Tag** modul.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

A minta futtatásához adja át az elérési út a JSON-konfigurációs fájl paramétereként a **gedélyezése\_átjáró** bináris. Az alábbi parancs feltételezi, hogy használja a **gateway_sample.json** konfigurációs fájlt. A parancs végrehajtásához a **iot-edge** a málna Pi mappájában:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Szükség lehet a kis gombra a SensorTag eszköz könnyebben felderíthetők a minta futtatása előtt.

A minta futtatásához használhatja a [eszköz explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) vagy a [IOT hubbal-explorer](https://github.com/Azure/iothub-explorer) az IoT-átjárónak a SensorTag eszközről továbbítja az üzeneteket figyelésére. Például az IOT hubbal-Explorerben figyelheti eszköz-a-felhőbe küldött üzeneteket a következő parancsot:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

A táblázat modul is támogatja az eszközt az IoT-központ küldő parancsokat. Használhatja a [eszköz explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) vagy a [IOT hubbal-explorer](https://github.com/Azure/iothub-explorer) eszköz, amely a BLA átjáró modul továbbítja az int eszköz be küldési JSON üzenetekre.

Ha a Texas eszközök SensorTag eszközt használ, bekapcsolhatja a piros LED-jét, zöld LED-jét, vagy berregő parancsokat küld az IoT-központ. Az IoT-központ küldeni a parancsok, először a következő két JSON-üzenetek küldése sorrendben. Ezután a parancsok a fény vagy berregő bekapcsolása küldhet.

1. Minden LED és a berregő alaphelyzetbe állítása (kikapcsolni őket):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. "Távoli" i/o konfigurálni:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Küldheti el a következő parancsok fény vagy berregő az SensorTag eszköz bekapcsolása:

* A piros LED bekapcsolása:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* A zöld LED bekapcsolása:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Kapcsolja be a berregő:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, IoT peremhálózati tájékozottabbak kapnak, és néhány kódpéldák kísérletezhet, látogasson el az alábbi fejlesztői oktatóanyagok és erőforrások:

* [Az Azure IoT él][lnk-sdk]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
