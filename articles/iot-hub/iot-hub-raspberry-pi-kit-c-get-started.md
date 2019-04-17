---
title: Raspberry Pi csatlakoztatása Azure IoT hubra C használatával |} A Microsoft Docs
description: Ismerje meg, hogyan beállítása és a Raspberry Pi csatlakoztatása Azure IoT hubhoz Raspberry pi adatokat küldeni az Azure felhőalapú platformján
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 6a895d7978f1af3914bbb9dee3594dbfffd9f317
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607901"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi csatlakoztatása az Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ez az oktatóanyag első lépésként, tanulás a Raspberry Pi Raspbian futtató való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe az eszközök [Azure IoT Hub](about-iot-hub.md). A Windows 10 IoT Core-minta, nyissa meg a [Windows Dev Center](https://www.windowsondevices.com/).

Még nem rendelkezik egy csomagot? Próbálja ki [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md). Vagy egy új csomag vásárlása [Itt](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>TEENDŐ

* Hozzon létre egy IoT hubot.

* Eszköz regisztrálása az IoT hub pi.

* A telepítő Raspberry Pi.

* A mintaalkalmazás futtatása az IoT hub érzékelői adatokat küldendő Pi-on.

Raspberry Pi csatlakozni az IoT hub által létrehozott. Ezután futtassa a mintaalkalmazás hőmérséklettel és páratartalommal kapcsolatos adatok gyűjtését BME280 érzékelő Pi-on. Végül az érzékelő adatokat küld az IoT hubnak.

## <a name="what-you-learn"></a>Ismertetett témák

* Útmutató az Azure IoT hub létrehozása és az új eszköz kapcsolati karakterláncának beszerzése.

* Hogyan BME280 érzékelő Pi kapcsolódni.

* Hogyan gyűjtheti az érzékelőktől kapott adatok egy mintaalkalmazás Pi-on való futtatásával.

* Hogyan küldhet az IoT hub érzékelői adatokat.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* A Raspberry Pi 2 vagy a Raspberry Pi 3 tábla.

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.

* Egy figyelő, egy USB billentyűzetből és egérből Pi csatlakozó.

* Mac vagy Windows vagy Linux rendszert futtató számítógép.

* Internetkapcsolat.

* 16 GB-os vagy újabb microSD-kártyán.

* USB-SD adapter vagy microSD kártya írása a microSD-kártyán operációsrendszer-képet.

* 5 voltos 2-és a egy power adja meg a 6-ragadós micro USB-kábellel.

A következő elemek nem kötelező:

* Az összeállított Adafruit BME280 páratartalom, hőmérséklet és nyomás érzékelő.

* Egy breadboard.

* 6-F/M átkötés fenyegetéseknek.

* Szórt 10 mm LED.

> [!NOTE]
> Ezek az elemek nem kötelező, mivel a kódminta szimulált érzékelői adatokat támogatja.
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi beállítása

Most állítsa be a Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi a Raspbian operációs rendszer telepítése

Készítse elő a Raspbian lemezkép telepítésének microSD-kártyán.

1. Töltse le a Raspbian.

   1. [Töltse le a Desktopban Raspbian Stretch](https://www.raspberrypi.org/downloads/raspbian/) (a .zip-fájlt).

   2. Bontsa ki a Raspbian lemezképet a számítógép egyik mappájába.

2. Telepítse a Raspbian a microSD-kártyán.

   1. [Töltse le és telepítse a Etcher SD-kártya író segédprogram](https://etcher.io/).

   2. Etcher futtassa, és jelölje ki a kibontott Raspbian lemezképét az 1. lépésben.

   3. Válassza ki a microSD-kártyán meghajtót. Vegye figyelembe, hogy Etcher előfordulhat, hogy már ki van választva a megfelelő meghajtót.

   4. Kattintson a Flash Raspbian telepíteni a microSD-kártyán.

   5. Eltávolítja a számítógépről a microSD-kártyán, amikor a telepítés befejeződött. Biztonságos-e a microSD-kártyán közvetlenül eltávolítani, mert Etcher automatikusan kiadása, vagy a befejezéskor microSD-kártyán leválasztja.

   6. A Pi szúrhat be a microSD-kártyán.

### <a name="enable-ssh-and-spi"></a>Az SSH és ÜTI engedélyezése

1. Pi csatlakozni a monitorból, billentyűzetből és egérből. Indítsa el a Pi, majd jelentkezzen be Raspbian használatával `pi` felhasználónevet és a `raspberry` jelszóként.
 
2. A Raspberry ikonra > **beállítások** > **Raspberry Pi konfigurációs**.

   ![A Raspbian beállítások menü](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Az a **felületek** lapra, és állítsa **ÜTI** és **SSH** való **engedélyezése**, és kattintson a **OK**. Ha nem rendelkezik fizikai érzékelők, és szeretné használni a szimulált érzékelői adatokat, ez a lépés nem kötelező.

   ![ÜTI és a Raspberry Pi-on SSH engedélyezése](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Az SSH és ÜTI engedélyezéséhez találhat további segédanyagok [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Csatlakozás az érzékelő Pi

Csatlakozhat a breadboard és átkötés fenyegetéseknek LED és a egy BME280 Pi módon. Ha nem rendelkezik az érzékelő [kihagyhatja ezt a szakaszt](#connect-pi-to-the-network).

![A Raspberry Pi és érzékelő kapcsolat](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

A BME280 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatokat gyűjthet. És a LED fog villogni, ha egy eszköz és a felhő közötti kommunikációt.

Érzékelő PIN-kód használja a következő kábelezés:

| Indítsa el a (érzékelő & LED)     | Teljes (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (5 g. PIN-kód)         | GPIO 4 (PIN-kód 7)         | Fehér kábel   |
| LED GND (PIN-kód 6G)         | GND (6 PIN-kód)            | Fekete kábellel   |
| VDD (18F PIN-kód)            | 3.3V PWR (17 PIN-kód)      | Fehér kábel   |
| GND (Pin 20F)            | GND (Pin 20)           | Fekete kábellel   |
| SCK (21F PIN-kód)            | SPI0 SCLK (23 PIN-kód)     | Narancssárga kábel  |
| SDO (Pin 22F)            | SPI0 MISO (21 PIN-kód)     | Sárga kábel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Zöld kábel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Kék kábel    |

Kattintson ide a megtekintéshez [Raspberry Pi-2 és 3 PIN-kód-leképezések](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referenciaként.

Miután sikeresen csatlakozott a Raspberry Pi BME280, kell tenni, például a kép alatt.

![Csatlakoztatott Pi és BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoztatása a hálózathoz

A Pi kapcsolja be a micro USB-kábelen keresztül és a tápegység. Az Ethernet-kábel használatával Pi csatlakoztatása a vezetékes hálózathoz, vagy kövesse a [utasításait a Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi a vezeték nélküli hálózathoz való kapcsolódáshoz. Miután a Pi sikeresen csatlakozott a hálózathoz, jegyezze fel az kell a [IP-címét a Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Vezetékes hálózathoz csatlakozik](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>A mintaalkalmazás futtatása Pi-on

### <a name="sign-into-your-raspberry-pi"></a>Jelentkezzen be a Raspberry Pi

1. A következő SSH-ügyfelet a gazdagép számítógépről szeretne csatlakozni a Raspberry Pi valamelyikét használja.
   
   **Windows-felhasználók**
   1. Töltse le és telepítse [PuTTY](https://www.putty.org/) Windows számára. 
   1. Másolja ki a gazdagép nevét (vagy IP-cím), a Pi szakasz IP-címét, és válassza ki az SSH a kapcsolat típusaként.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- és Ubuntu-felhasználók**

   A beépített SSH-ügyfél használata Ubuntu vagy macOS rendszeren. Előfordulhat, hogy kell futtatnia `ssh pi@<ip address of pi>` Pi SSH-n keresztül kapcsolódni.
   > [!NOTE]
   > Az alapértelmezett felhasználónév az `pi` , és a jelszó `raspberry`.


### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Klónozza a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Futtassa a beállítási szkriptet:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Ha, **nem rendelkezik egy fizikai BME280**, használhatja "--szimulált adatok" hőmérsékleti és páratartalom-adatok szimulálása parancssori paraméter. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>A minta-alkalmazás összeállítása és futtatása

1. A mintaalkalmazás létrehozása a következő parancs futtatásával:

   ```bash
   cmake . && make
   ```
   
   ![Kimeneti létrehozása](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. A mintaalkalmazás futtatása a következő parancs futtatásával:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Győződjön meg arról, másolás és beillesztés az eszköz kapcsolati karakterláncának be a szimpla idézőjelek között.
   >

Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

![Kimenet – Raspberry Pi az IoT hubnak küldött érzékelőktől kapott adatok](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Olvassa el a hub által fogadott üzeneteket

Egy figyelheti az eszközről az IoT hub által fogadott üzeneteket módja az Azure IoT-eszközök használata a Visual Studio Code. További tudnivalókért lásd: [használata az Azure IoT Tools for Visual Studio Code használatával az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

A további lehetőségek a eszköz által küldött adatok feldolgozásához folytassa a következő szakaszban.

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
