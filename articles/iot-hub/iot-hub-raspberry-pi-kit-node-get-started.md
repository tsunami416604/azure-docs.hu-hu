---
title: Raspberry Pi felhőbe (Node.js) – a Raspberry Pi csatlakoztatása Azure IoT hubra |} A Microsoft Docs
description: Ismerje meg, hogyan beállítása és a Raspberry Pi csatlakoztatása az Azure IoT hubba a Raspberry Pi adatokat küldeni az Azure felhőalapú platformján ebben az oktatóanyagban.
author: rangv
manager: ''
keywords: az Azure iot raspberry pi, a raspberry pi az iot hub, a raspberry pi send adatait a felhőbe, a raspberry pi a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 348186bcb29b272b7e6512ce42221d54d6b388d9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161815"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi csatlakoztatása Azure IoT hubhoz (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ez az oktatóanyag első lépésként, tanulás a Raspberry Pi Raspbian futtató való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe az eszközök [Azure IoT Hub](about-iot-hub.md). A Windows 10 IoT Core-minta, nyissa meg a [Windows Dev Center](http://www.windowsondevices.com/).

Még nem rendelkezik egy csomagot? Próbálja ki [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md). Vagy egy új csomag vásárlása [Itt](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>TEENDŐ

* Hozzon létre egy IoT hubot.
* Eszköz regisztrálása az IoT hub pi.
* Raspberry Pi beállítása.
* A mintaalkalmazás futtatása az IoT hub érzékelői adatokat küldendő Pi-on.

## <a name="what-you-learn"></a>Ismertetett témák

* Útmutató az Azure IoT hub létrehozása és az új eszköz kapcsolati karakterláncának beszerzése.
* Hogyan BME280 érzékelő Pi kapcsolódni.
* Hogyan gyűjtheti az érzékelőktől kapott adatok egy mintaalkalmazás Pi-on való futtatásával.
* Hogyan küldhet az IoT hub érzékelői adatokat.

## <a name="what-you-need"></a>Mi szükséges

![Mi szükséges](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 vagy a Raspberry Pi 3 kártya.
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Egy figyelő, egy USB billentyűzetből és egérből, amely Pi csatlakozik.
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
> Ha a választható elemek nincs, szimulált érzékelői adatokat is használhatja.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi beállítása

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi a Raspbian operációs rendszer telepítése

Készítse elő a Raspbian lemezkép telepítésének microSD-kártyán.

1. Töltse le a Raspbian.

   a. [Töltse le a Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (a .zip-fájlt).

   > [!WARNING]
   > Használja a fenti hivatkozást letöltéséhez `raspbian-2017-07-5` zip-lemezképet. Raspbian lemezképek legújabb verziója van bizonyos ismert problémák kábelezés-Pi csomóponttal, amely a következő lépésben hibát okozhat.
 
   b. Bontsa ki a Raspbian lemezképet a számítógép egyik mappájába.

2. Telepítse a Raspbian a microSD-kártyán.

   a. [Töltse le és telepítse a Etcher SD-kártya író segédprogram](https://etcher.io/).

   b. Etcher futtassa, és jelölje ki a kibontott Raspbian lemezképét az 1. lépésben.

   c. Válassza ki a microSD-kártyán meghajtót. Etcher előfordulhat, hogy már ki van választva a megfelelő meghajtót.

   d. Kattintson a Flash Raspbian telepíteni a microSD-kártyán.

   e. Eltávolítja a számítógépről a microSD-kártyán, amikor a telepítés befejeződött. Biztonságos-e a microSD-kártyán közvetlenül eltávolítani, mert Etcher automatikusan kiadása, vagy a befejezéskor microSD-kártyán leválasztja.

   f. A Pi szúrhat be a microSD-kártyán.

### <a name="enable-ssh-and-i2c"></a>Az SSH és I2C engedélyezése

1. A Pi csatlakozhat a monitor, billentyűzet és egér. 

2. Indítsa el a Pi, és ezután jelentkezzen be Raspbian használatával `pi` felhasználónevet és a `raspberry` jelszóként.

3. A Raspberry ikonra > **beállítások** > **Raspberry Pi konfigurációs**.

   ![A Raspbian beállítások menü](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

4. Az a **felületek** lapra, és állítsa **I2C** és **SSH** való **engedélyezése**, és kattintson a **OK**. Ha nem rendelkezik fizikai érzékelők, és szeretné használni a szimulált érzékelői adatokat, ez a lépés nem kötelező.

   ![I2C és a Raspberry Pi-on SSH engedélyezése](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Az SSH és I2C engedélyezéséhez találhat további segédanyagok [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Csatlakozás az érzékelő Pi

Csatlakozhat a breadboard és átkötés fenyegetéseknek LED és a egy BME280 Pi módon. Ha nem rendelkezik az érzékelő [kihagyhatja ezt a szakaszt](#connect-pi-to-the-network).

![A Raspberry Pi és érzékelő kapcsolat](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

A BME280 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatokat gyűjthet. A LED v milisekundách, amikor az eszköz egy üzenetet küld a felhőbe. 

Érzékelő PIN-kód használja a következő kábelezés:

| Indítsa el a (érzékelő & LED)     | Teljes (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| VDD (5 g. PIN-kód)             | 3.3V PWR (1 PIN-kód)       | Fehér kábel   |
| GND (PIN-kód 7G)             | GND (6 PIN-kód)            | Barna kábel   |
| SDI (PIN-kód 10G)            | I2C1 SDA (3 PIN-kód)       | Piros kábel     |
| SCK (8G PIN-kód)             | I2C1 SCL (5 PIN-kód)       | Narancssárga kábel  |
| LED VDD (18F PIN-kód)        | GPIO 24 (18 PIN-kód)       | Fehér kábel   |
| LED GND (17F PIN-kód)        | GND (20 PIN-kód)           | Fekete kábellel   |

Kattintson ide a megtekintéshez [Raspberry Pi-2 és 3 PIN-kód-leképezések](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referenciaként.

Miután sikeresen csatlakozott a Raspberry Pi BME280, kell tenni, például a kép alatt.

![Csatlakoztatott Pi és BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>A Pi csatlakoztatása a hálózathoz

A Pi kapcsolja be a micro USB-kábelen keresztül és a tápegység. Az Ethernet-kábel használatával Pi csatlakoztatása a vezetékes hálózathoz, vagy kövesse a [utasításait a Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) Pi a vezeték nélküli hálózathoz való kapcsolódáshoz. Miután a Pi sikeresen csatlakozott a hálózathoz, jegyezze fel az kell a [IP-címét a Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Vezetékes hálózathoz csatlakozik](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Győződjön meg arról, hogy a Pi csatlakozik-e a számítógép ugyanazon a hálózaton. Például ha a számítógép vezeték nélküli hálózathoz csatlakoztatva van, a Pi vezetékes hálózathoz van csatlakoztatva, Ön nem láthatja a devdisco kimenetben szereplő IP-cím.

## <a name="run-a-sample-application-on-pi"></a>A mintaalkalmazás futtatása Pi-on

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klónozza a mintaalkalmazást, és telepítse az előfeltételként szolgáló csomagok

1. Csatlakoztassa a gazdaszámítógépet az valamelyik a következő SSH-ügyfelet a Raspberry Pi:
   
   **Windows-felhasználók**
  
   a. Töltse le és telepítse [PuTTY](http://www.putty.org/) Windows számára. 

   b. Másolja ki a gazdagép nevét (vagy IP-cím), a Pi szakasz IP-címét, és válassza ki az SSH a kapcsolat típusaként.
   
   ![Putty-kapcsolaton keresztül](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- és Ubuntu-felhasználók**
   
   A beépített SSH-ügyfél használata Ubuntu vagy macOS rendszeren. Előfordulhat, hogy kell futtatnia `ssh pi@<ip address of pi>` Pi SSH-n keresztül kapcsolódni.

   > [!NOTE] 
   > Az alapértelmezett felhasználónév az `pi` és a jelszó `raspberry`.

2. Telepítse a Node.js és NPM, a Pi.
   
   Először ellenőrizze a Node.js-verzió. 
   
   ```bash
   node -v
   ```

   Kisebb, mint a 4.x-es verzió esetén, vagy ha nincs Node.js a Pi-on, telepítse a legújabb verziót.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klónozza a mintaalkalmazást.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. A minta összes csomagok telepítéséhez. A telepítés Azure IoT eszközoldali SDK-t, a BME280 érzékelő könyvtár és a fennmaradó összefűzése Pi könyvtár tartalmaz.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   >Eltarthat néhány percig, attól függően, a hálózati kapcsolatot a telepítési folyamat befejezéséhez.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Nyissa meg a konfigurációs fájlban a következő parancsok futtatásával:

   ```bash
   nano config.json
   ```

   ![Konfigurációs fájl](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Nincsenek a két elem konfigurálhatja ezt a fájlt. Az első egy `interval`, amely megadja, hogy az időtartam alatt a felhőbe küldött üzenetek között (ezredmásodpercben). A második az egyik `simulatedData`, azaz az e használni a szimulált érzékelői adatokat, vagy nem logikai érték.

   Ha Ön **nem rendelkezik az érzékelő**állítsa be a `simulatedData` értéket a következőre `true` , hogy a mintaalkalmazás létrehozása és használata a szimulált érzékelői adatokat.

2. Mentéséhez és bezárásához írja be a vezérlő-O > Enter > CTRL-X.

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A mintaalkalmazás futtatása a következő parancs futtatásával:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Győződjön meg arról, másolás és beillesztés az eszköz kapcsolati karakterláncának be a szimpla idézőjelek között.


Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

![Kimenet – Raspberry Pi az IoT hubnak küldött érzékelőktől kapott adatok](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott. A Raspberry Pi a Raspberry Pi az IoT hub- vagy küldés üzeneteket küldött üzenetek, olvassa el a [Azure IoT-eszközkészlet használata az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása a Visual Studio Code-bővítmény](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
