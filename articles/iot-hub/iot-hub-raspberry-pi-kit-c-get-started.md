---
title: Raspberry Pi csatlakoztatása az Azure IoT Hubhoz c használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként állítható be és kapcsolhatja össze a Raspberry Pi alkalmazást az Azure IoT Hubhoz a Raspberry Pi számára, hogy adatokat küldjön az Azure felhőplatformjára
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67838780"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi csatlakoztatása az Azure IoT Hubhoz (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban először a Raspbiant futtató Raspberry Pi-vel való munka alapjait tanulhatja meg. Ezután megtudhatja, hogyan csatlakoztathatja zökkenőmentesen eszközeit a felhőhöz az [Azure IoT Hub](about-iot-hub.md)használatával. Windows 10 IoT Core minták esetén keresse fel a [Windows fejlesztői központot.](https://www.windowsondevices.com/)

Még nincs készleted? Próbálja [ki a Raspberry Pi online szimulátort.](iot-hub-raspberry-pi-web-simulator-get-started.md) Vagy vesz egy új készlet [itt](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Mit csinálsz

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a Pi számára az IoT hubban.

* Állítsa be a Raspberry Pi-t.

* Futtasson egy mintaalkalmazást a Pi-n az IoT hubra küldött érzékelőadatok küldéséhez.

Csatlakoztassa a Raspberry Pi-t egy létrehozott IoT-hubhoz. Ezután futtategy mintaalkalmazást a Pi-n, hogy hőmérsékleti és páratartalom-adatokat gyűjtsön egy BME280 érzékelőből. Végül elküldi az érzékelő adatait az IoT hubra.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT-központ létrehozása és az új eszközkapcsolati karakterlánc beszerezni.

* A Pi csatlakoztatása BME280 érzékelővel.

* Az érzékelőadatok gyűjtése a Pi-n futó mintaalkalmazás sal.

* Az érzékelőadatok küldése az IoT hubra.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* A Raspberry Pi 2 vagy A Raspberry Pi 3 tábla.

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiókkal, néhány perc alatt [hozzon létre egy ingyenes Azure-próbafiókot.](https://azure.microsoft.com/free/)

* A Pi-hez csatlakozó monitor, USB-billentyűzet és egér.

* Mac vagy Windows vagy Linux rendszert futtató PC.

* Internetkapcsolat.

* 16 GB-os vagy microSD-kártya.

* USB-SD adapter vagy microSD-kártya az operációs rendszer lemezképének a microSD-kártyára való írásához.

* 5 voltos, 2 amperes tápegység a 180 centis micro USB-kábellel.

A következő elemek nem kötelezőek:

* Egy összeszerelt Adafruit BME280 hőmérséklet-, nyomás- és páratartalom-érzékelő.

* Egy kenyérvágódeszka.

* 6 F/M áthidaló vezeték.

* Egy szórt 10 mm-es LED.

> [!NOTE]
> Ezek az elemek nem kötelezőek, mert a kódminta támogatja a szimulált érzékelőadatokat.
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi beállítása

Most állítsa be a Raspberry Pi-t.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Telepítse a Raspbian operációs rendszert a Pi-hez

Készítse elő a microSD-kártyát a Raspbian kép telepítéséhez.

1. Letöltés Raspbian.

   1. [Letöltés Raspbian Stretch asztali](https://www.raspberrypi.org/downloads/raspbian/) (a .zip fájl).

   2. Bontsa ki a Raspbian lemezképet a számítógép egy mappájába.

2. Telepítse a Raspbian-t a microSD kártyára.

   1. [Töltse le és telepítse az Etcher SD kártya író segédprogram](https://etcher.io/).

   2. Futtassa az Etcher t, és válassza ki az 1.

   3. Válassza ki a microSD-kártya meghajtóját. Ne feledje, hogy az Etcher lehet, hogy már kiválasztotta a megfelelő meghajtót.

   4. Kattintson a Flash gombra a Raspbian microSD-kártyára való telepítéséhez.

   5. A telepítés befejeztével vegye ki a microSD-kártyát a számítógépről. A microSD-kártyát biztonságosan közvetlenül eltávolítani, mert az Etcher a befejezéskor automatikusan kiadja vagy leveszi a microSD-kártyát.

   6. Helyezze be a microSD-kártyát a Pi-be.

### <a name="enable-ssh-and-spi"></a>SSH és SPI engedélyezése

1. Csatlakoztassa a Pi-t a monitorhoz, a billentyűzethez és az `pi` egérhez, indítsa `raspberry` el a Pi-t, majd jelentkezzen be a Raspbian ba felhasználónévként és jelszóként.
 
2. Kattintson a Raspberry ikonra > > **Raspberry Pi konfigurációja beállításra**. **Preferences**

   ![A Raspbian Preferences menü](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. A **Kapcsolatok** lapon állítsa az **SPI** és **az SSH** **beállítását az Engedélyezés gombra,** majd kattintson **az OK**gombra. Ha nem rendelkezik fizikai érzékelőkkel, és szimulált érzékelőadatokat szeretne használni, ez a lépés nem kötelező.

   ![SPI és SSH engedélyezése raspberry Pi-n](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Az SSH és az SPI engedélyezéséhez további referenciadokumentumokat találhat [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [raspi-CONFIG dokumentumairól.](https://www.raspberrypi.org/documentation/configuration/raspi-config.md)
>

### <a name="connect-the-sensor-to-pi"></a>Csatlakoztassa az érzékelőt a Pi-hez

A kenyérvágódeszkával és az áthidaló vezetékekkel csatlakoztasson egy LED-et és egy BME280-at a Pi-hez az alábbiak szerint. Ha nem rendelkezik az érzékelővel, [hagyja ki ezt a szakaszt.](#connect-pi-to-the-network)

![A Raspberry Pi és az érzékelő csatlakozás](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

A BME280 érzékelő képes a hőmérséklet- és páratartalom-adatok gyűjtésére. És a LED villogni fog, ha kommunikáció van az eszköz és a felhő között.

Érzékelőcsapok esetén a következő vezetékeket használja:

| Indítás (érzékelő & LED)     | Vége (board)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (5G tű)         | GPIO 4 (7. pin)         | Fehér kábel   |
| LED GND (6G tű)         | GND (6. pin)            | Fekete kábel   |
| VDD (18F-es pin)            | 3.3V PWR (17. pin)      | Fehér kábel   |
| GND (20F tű)            | GND (20-as tű)           | Fekete kábel   |
| SCK (21F tű)            | SPI0 SCLK (23-as tű)     | Narancssárga kábel  |
| SDO (22F tű)            | SPI0 MISO (21-es tű)     | Sárga kábel  |
| SDI (23F tű)            | SPI0 MOSI (19-es tű)     | Zöld kábel   |
| CS (24F pin)             | SPI0 CS (24-es pin)       | Kék kábel    |

Kattintson ide a [Raspberry Pi 2 & 3 tűs leképezések](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) megtekintéséhez.

Miután sikeresen csatlakoztatta a BME280-at a Raspberry Pi-hez, az alábbi képhez hasonlóan kell lennie.

![Csatlakoztatott Pi és BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoztatása a hálózathoz

Kapcsolja be a Pi-t a mikro USB-kábel és a tápegység segítségével. Az Ethernet-kábellel csatlakoztassa a Pi-t a vezetékes hálózathoz, vagy kövesse [a Raspberry Pi Foundation utasításait a](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi vezeték nélküli hálózathoz való csatlakoztatásához. Miután a Pi sikeresen csatlakozott a hálózathoz, meg kell jegyezned a [Pi IP-címét.](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)

![Csatlakozás vezetékes hálózathoz](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Mintaalkalmazás futtatása pi-n

### <a name="sign-into-your-raspberry-pi"></a>Jelentkezzen be a Raspberry Pi-be

1. A Raspberry Pi-hez való csatlakozáshoz a gazdagépről az alábbi SSH-ügyfelek egyikével csatlakozhat.
   
   **Windows-felhasználók**
   1. Töltse le és telepítse [a PuTTY](https://www.putty.org/) for Windows rendszert. 
   1. Másolja a Pi IP-címét az állomásnév (vagy IP-cím) szakaszba, és válassza az SSH-t kapcsolattípusként.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac és Ubuntu felhasználók**

   Használja a beépített SSH kliensubuntu vagy macOS. Előfordulhat, hogy `ssh pi@<ip address of pi>` futnia kell a Pi SSH-n keresztüli csatlakoztatásához.
   > [!NOTE]
   > Az alapértelmezett felhasználónév a `pi` , `raspberry`a jelszó pedig a.


### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Klónozza a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. A telepítőparancsfájl futtatása:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Ha **nem rendelkezik fizikai BME280-as**sal, a hőmérséklet-&páratartalom adatainak szimulálásához használhatja a "-szimulált adatok" parancsot parancssori paraméterként. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>A mintaalkalmazás létrehozása és futtatása

1. A mintaalkalmazás létrehozása a következő parancs futtatásával:

   ```bash
   cmake . && make
   ```
   
   ![Kimenet összeállítása](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Futtassa a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Győződjön meg arról, hogy másolja be az eszköz kapcsolati karakterláncát az egyidézőjelek közé.
   >

A következő kimenetnek kell megjelennie, amely tartalmazza az érzékelők adatait és az IoT Hubnak üzeneteket.

![Kimenet – A Raspberry Pi-ról az IoT Hubba küldött érzékelői adatok](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>A központ által fogadott üzenetek elolvasása

Az IoT hub által az eszközről fogadott üzenetek figyelésének egyik módja az Azure IoT-eszközök a Visual Studio-kódhoz használata. További információ: [Az Azure IoT-eszközök használata a Visual Studio-kódhoz az eszköz és az IoT Hub közötti üzenetek küldéséhez és fogadásához.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Az eszköz által küldött adatok feldolgozásának további módjaiért folytassa a következő szakaszsal.

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazást futtatott az érzékelőadatok gyűjtésére és az IoT-központba való elküldésre.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
