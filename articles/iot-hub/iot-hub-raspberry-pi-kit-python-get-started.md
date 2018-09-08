---
title: Raspberry Pi Raspberry Pi csatlakoztatása Azure IoT Hub (Python) - felhőhöz |} A Microsoft Docs
description: Ismerje meg, hogyan beállítása és a Raspberry Pi csatlakoztatása az Azure IoT hubba a Raspberry Pi adatokat küldeni az Azure felhőalapú platformján ebben az oktatóanyagban.
author: rangv
manager: ''
keywords: az Azure iot raspberry pi, a raspberry pi az iot hub, a raspberry pi send adatait a felhőbe, a raspberry pi a felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 6e668394bcb647df901dfac72c552475e56f20bf
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161101"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Raspberry Pi csatlakozhat az Azure IoT Hub (Python)

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
Ezek az elemek nem kötelező, mivel a kód minta támogatási szimulált érzékelői adatokat.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi beállítása

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

### <a name="enable-ssh-and-i2c"></a>Az SSH és I2C engedélyezése

1. Pi csatlakozni a monitorból, billentyűzetből és egérből. Indítsa el a Pi, majd jelentkezzen be Raspbian használatával `pi` felhasználónevet és a `raspberry` jelszóként.
1. A Raspberry ikonra > **beállítások** > **Raspberry Pi konfigurációs**.

   ![A Raspbian beállítások menü](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Az a **felületek** lapra, és állítsa **I2C** és **SSH** való **engedélyezése**, és kattintson a **OK**. Ha nem rendelkezik fizikai érzékelők, és szeretné használni a szimulált érzékelői adatokat, ez a lépés nem kötelező.

   ![I2C és a Raspberry Pi-on SSH engedélyezése](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Az SSH és I2C engedélyezéséhez találhat további segédanyagok [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) és [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Csatlakozás az érzékelő Pi

Csatlakozhat a breadboard és átkötés fenyegetéseknek LED és a egy BME280 Pi módon. Ha nem rendelkezik az érzékelő [kihagyhatja ezt a szakaszt](#connect-pi-to-the-network).

![A Raspberry Pi és érzékelő kapcsolat](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

A BME280 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatokat gyűjthet. És a LED fog villogni, ha egy eszköz és a felhő közötti kommunikációt. 

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

### <a name="install-the-prerequisite-packages"></a>Telepítse az előfeltételként szolgáló csomagok

A következő SSH-ügyfelet a gazdagép számítógépről szeretne csatlakozni a Raspberry Pi valamelyikét használja.
   
   **Windows-felhasználók**
   1. Töltse le és telepítse [PuTTY](http://www.putty.org/) Windows számára. 
   1. Másolja ki a gazdagép nevét (vagy IP-cím), a Pi szakasz IP-címét, és válassza ki az SSH a kapcsolat típusaként.
   
   
   **Mac- és Ubuntu-felhasználók**
   
   A beépített SSH-ügyfél használata Ubuntu vagy macOS rendszeren. Előfordulhat, hogy kell futtatnia `ssh pi@<ip address of pi>` Pi SSH-n keresztül kapcsolódni.
   > [!NOTE] 
   Az alapértelmezett felhasználónév az `pi` , és a jelszó `raspberry`.


### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Klónozza a mintaalkalmazást a következő parancs futtatásával:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Nyissa meg a konfigurációs fájlban a következő parancsok futtatásával:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   Nincsenek 5 makrók configurate használhatja ezt a fájlt. Az első egy `MESSAGE_TIMESPAN`, amely megadja az időtartam (ezredmásodpercben), két a felhőbe küldött üzenetek között. A második érték `SIMULATED_DATA`, azaz az e használni a szimulált érzékelői adatokat, vagy nem logikai érték. `I2C_ADDRESS` a I2C címével, amely a BME280 érzékelő van csatlakoztatva van. `GPIO_PIN_ADDRESS` az a GPIO cím a LED számára. A `BLINK_TIMESPAN`, amely definiálva az időtartam, ezredmásodpercben bekapcsolásakor a LED.

   Ha Ön **nem rendelkezik az érzékelő**állítsa be a `SIMULATED_DATA` értéket a következőre `True` , hogy a mintaalkalmazás létrehozása és használata a szimulált érzékelői adatokat.

1. Mentéséhez és bezárásához nyomja meg az ellenőrző-O > Enter > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>A minta-alkalmazás összeállítása és futtatása

1. A mintaalkalmazás létrehozása a következő parancs futtatásával. Mivel az Azure IoT SDK Pythonhoz készült felül az Azure IoT eszközoldali C SDK burkolókat, szüksége lesz a C-függvénytárak összeállításához, ha azt szeretné, vagy létre kell hozni a Python-kódtárakat a kódból származzanak.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Azt is megadhatja a kívánt futtatásával verziót `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Ha paraméter nélkül parancsfájlt futtat, a parancsfájl automatikusan észleli a python telepítve verzióját (keresési feladatütemezési 2.7 -> 3.4 -> 3.5). Ellenőrizze, hogy során készítése és a Python-verzió tartja konzisztens. 
   
   > [!NOTE] 
   Az alkalmazásfejlesztés a Python ügyféloldali kódtár (iothub_client.so) kisebb, mint 1GB RAM-MAL rendelkező Linux rendszerű eszközökön, látni hozhat létre az első iothub_client_python.cpp kiépítésekor, ahogy az alábbi 98 % megakad `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Ha a probléma, ellenőrizze a csökkenhet az eszköz használatának `free -m command` idő alatt egy másik terminálablakban. Ha nincs elég memória iothub_client_python.cpp fájl fordítása közben fut, előfordulhat, a lapozóterület beolvasni a sikeres kiépítéséhez a Python ügyféloldali eszközoldali SDK-könyvtár több elérhető memória ideiglenes növelésére.
   
1. A mintaalkalmazás futtatása a következő parancs futtatásával:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Győződjön meg arról, másolás és beillesztés az eszköz kapcsolati karakterláncának be a szimpla idézőjelek között. És a python 3 használja, akkor a paranccsal `python3 app.py '<your Azure IoT hub device connection string>'`.


   Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

   ![Kimenet – Raspberry Pi az IoT hubnak küldött érzékelőktől kapott adatok](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelőktől kapott adatok összegyűjtésére, és küldje el az IoT hubnak küldött futtatott. A Raspberry Pi a Raspberry Pi az IoT hub- vagy küldés üzeneteket küldött üzenetek, olvassa el a [Azure IoT-eszközkészlet használata az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása a Visual Studio Code-bővítmény](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
