---
title: Raspberry Pi felhőbe (C) – a Raspberry Pi csatlakoztatása Azure IoT hubra |} A Microsoft Docs
description: Ismerje meg, hogyan beállítása és a Raspberry Pi csatlakoztatása az Azure IoT hubba a Raspberry Pi adatokat küldeni az Azure felhőalapú platformján ebben az oktatóanyagban.
author: rangv
manager: ''
keywords: az Azure iot raspberry pi, a raspberry pi az iot hub, a raspberry pi send adatait a felhőbe, a raspberry pi a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: c8e2dcfef1db8bfe3d76ac917f8c14a4060e3968
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318758"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi csatlakoztatása az Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ez az oktatóanyag első lépésként, tanulás a Raspberry Pi Raspbian futtató való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe az eszközök [Azure IoT Hub](about-iot-hub.md). A Windows 10 IoT Core-minta, nyissa meg a [Windows Dev Center](http://www.windowsondevices.com/).

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

![Mi szükséges](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

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
> Ezek az elemek nem kötelező, mivel a kód minta támogatási szimulált érzékelői adatokat.
>

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Raspberry Pi beállítása

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi a Raspbian operációs rendszer telepítése

Készítse elő a Raspbian lemezkép telepítésének microSD-kártyán.

1. Töltse le a Raspbian.
   1. [Töltse le a Desktopban Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/) (a .zip-fájlt).
   1. Bontsa ki a Raspbian lemezképet a számítógép egyik mappájába.
1. Telepítse a Raspbian a microSD-kártyán.
   1. [Töltse le és telepítse a Etcher SD-kártya író segédprogram](https://etcher.io/).
   1. Etcher futtassa, és jelölje ki a kibontott Raspbian lemezképét az 1. lépésben.
   1. Válassza ki a microSD-kártyán meghajtót. Vegye figyelembe, hogy Etcher előfordulhat, hogy már ki van választva a megfelelő meghajtót.
   1. Kattintson a Flash Raspbian telepíteni a microSD-kártyán.
   1. Eltávolítja a számítógépről a microSD-kártyán, amikor a telepítés befejeződött. Biztonságos-e a microSD-kártyán közvetlenül eltávolítani, mert Etcher automatikusan kiadása, vagy a befejezéskor microSD-kártyán leválasztja.
   1. A Pi szúrhat be a microSD-kártyán.

### <a name="enable-ssh-and-spi"></a>Az SSH és ÜTI engedélyezése

1. Pi csatlakozni a monitorból, billentyűzetből és egérből. Indítsa el a Pi, majd jelentkezzen be Raspbian használatával `pi` felhasználónevet és a `raspberry` jelszóként.
1. A Raspberry ikonra > **beállítások** > **Raspberry Pi konfigurációs**.

   ![A Raspbian beállítások menü](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Az a **felületek** lapra, és állítsa **ÜTI** és **SSH** való **engedélyezése**, és kattintson a **OK**. Ha nem rendelkezik fizikai érzékelők, és szeretné használni a szimulált érzékelői adatokat, ez a lépés nem kötelező.

   ![ÜTI és a Raspberry Pi-on SSH engedélyezése](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Az SSH és ÜTI engedélyezéséhez találhat további segédanyagok [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Csatlakozás az érzékelő Pi

Csatlakozhat a breadboard és átkötés fenyegetéseknek LED és a egy BME280 Pi módon. Ha nem rendelkezik az érzékelő [kihagyhatja ezt a szakaszt](#connect-pi-to-the-network).

![A Raspberry Pi és érzékelő kapcsolat](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

A BME280 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatokat gyűjthet. És a LED fog villogni, ha egy eszköz és a felhő közötti kommunikációt. 

Érzékelő PIN-kód használja a következő kábelezés:

| Indítsa el a (érzékelő & LED)     | Teljes (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (5 g. PIN-kód)         | GPIO 4 (PIN-kód 7)         | Fehér kábel   |
| LED GND (PIN-kód 6G)         | GND (6 PIN-kód)            | Fekete kábellel   |
| VDD (18F PIN-kód)            | 3.3V PWR (17 PIN-kód)      | Fehér kábel   |
| GND (20F PIN-kód)            | GND (20 PIN-kód)           | Fekete kábellel   |
| SCK (21F PIN-kód)            | SPI0 SCLK (23 PIN-kód)     | Narancssárga kábel  |
| SDO (22F PIN-kód)            | SPI0 MISO (21 PIN-kód)     | Sárga kábel  |
| SDI (23F PIN-kód)            | SPI0 MOSI (19 PIN-kód)     | Zöld kábel   |
| CS (24F PIN-kód)             | SPI0 CS (PIN-kód 24)       | Kék kábel    |

Kattintson ide a megtekintéshez [Raspberry Pi-2 és 3 PIN-kód-leképezések](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referenciaként.

Miután sikeresen csatlakozott a Raspberry Pi BME280, kell tenni, például a kép alatt.

![Csatlakoztatott Pi és BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoztatása a hálózathoz

A Pi kapcsolja be a micro USB-kábelen keresztül és a tápegység. Az Ethernet-kábel használatával Pi csatlakoztatása a vezetékes hálózathoz, vagy kövesse a [utasításait a Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi a vezeték nélküli hálózathoz való kapcsolódáshoz. Miután a Pi sikeresen csatlakozott a hálózathoz, jegyezze fel az kell a [IP-címét a Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Vezetékes hálózathoz csatlakozik](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>A mintaalkalmazás futtatása Pi-on

### <a name="login-to-your-raspberry-pi"></a>Jelentkezzen be a Raspberry Pi

1. A következő SSH-ügyfelet a gazdagép számítógépről szeretne csatlakozni a Raspberry Pi valamelyikét használja.
   
   **Windows-felhasználók**
   1. Töltse le és telepítse [PuTTY](http://www.putty.org/) Windows számára. 
   1. Másolja ki a gazdagép nevét (vagy IP-cím), a Pi szakasz IP-címét, és válassza ki az SSH a kapcsolat típusaként.
   
   ![Putty-kapcsolaton keresztül](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- és Ubuntu-felhasználók**
   
   A beépített SSH-ügyfél használata Ubuntu vagy macOS rendszeren. Előfordulhat, hogy kell futtatnia `ssh pi@<ip address of pi>` Pi SSH-n keresztül kapcsolódni.
   > [!NOTE] 
   Az alapértelmezett felhasználónév az `pi` , és a jelszó `raspberry`.


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
   
   ![Kimeneti létrehozása](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. A mintaalkalmazás futtatása a következő parancs futtatásával:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Győződjön meg arról, másolás és beillesztés az eszköz kapcsolati karakterláncának be a szimpla idézőjelek között.
   >

Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

![Kimenet – Raspberry Pi az IoT hubnak küldött érzékelőktől kapott adatok](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott. A Raspberry Pi a Raspberry Pi az IoT hub- vagy küldés üzeneteket küldött üzenetek, olvassa el a [Azure IoT-eszközkészlet használata az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása a Visual Studio Code-bővítmény](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
