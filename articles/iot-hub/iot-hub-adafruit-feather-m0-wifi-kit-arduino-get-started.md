---
title: A felhőbe – m0 Feather M0 WiFi csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ismerje meg, hogyan Adafruit Feather M0 WiFi kapcsolatot az Azure IoT hubra az Azure felhőalapú platformján ebben az oktatóanyagban adatokat küldeni.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6899bbd294a16dee3a767e976a92deaa00f0e2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676692"
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather M0 WiFi csatlakoztatása Azure IoT hubhoz a felhőben
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Egy BME280 Feather M0 WiFi és az IoT Hub közötti kapcsolat](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Ez az oktatóanyag első lépésként, tanulás a az Arduino táblához való használatának alapjait. Ezután megismerheti, hogyan zökkenőmentes csatlakozás használatával a felhőbe az eszközök [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>TEENDŐ

Adafruit Feather M0 WiFi csatlakozni az IoT hub által létrehozott. Egy mintaalkalmazás ezt a M0 WiFi hőmérséklettel és páratartalommal kapcsolatos adatok gyűjtését egy BME280 futtatni. Végül az érzékelő adatokat küld az IoT hubnak.


## <a name="what-you-learn"></a>Ismertetett témák

* IoT hub létrehozása és eszköz regisztrálása az Feather M0 WiFi
* Az érzékelő és a számítógép csatlakoztatása a Feather M0 WiFi
* A mintaalkalmazás futtat Feather M0 WiFi érzékelőktől kapott adatok gyűjtéséről
* Az érzékelőktől kapott adatok küldése az IoT hubhoz

## <a name="what-you-need"></a>Mi szükséges

![Az oktatóanyaghoz szükség részei](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Ez a művelet végrehajtásához a következő részek a Feather M0 WiFi Starter Kit a kell:

* A Feather M0 WiFi-tábla
* A Micro USB-Kapcsolattal a típust egy USB-kábelen keresztül

A fejlesztési környezetet is kell az alábbiakat:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.
* Mac vagy Windows- vagy Ubuntu rendszert futtató számítógép.
* Feather M0 WiFi szeretne csatlakozni a vezeték nélküli hálózathoz.
* Töltse le a konfigurációs eszközt internetkapcsolaton keresztül.
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 verzió vagy újabb. Korábbi verziók az Azure IoT Hub szalagtár nem működik.

Ha nem rendelkezik olyan érzékelő, a következő elemek nem kötelező. A beállítással, szimulált érzékelői adatokat is van:

* Egy BME280 hőmérsékleti és páratartalom-érzékelőt
* Egy breadboard
* Millió/M átkötés fenyegetéseknek

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Az érzékelő és a számítógép csatlakoztatása a Feather M0 WiFi
Ebben a szakaszban az érzékelők a táblához való csatlakozáshoz. Majd, az eszköz csatlakoztatása a számítógépre, további használatra.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Csatlakozás egy DHT22 hőmérsékleti és páratartalom-érzékelőt Feather M0 WiFi

Használják a breadboard és átkötés hozhatja létre a kapcsolatot. Ha nem rendelkezik olyan érzékelő, kihagyhatja ezt a szakaszt, mert a szimulált érzékelői adatokat használhatja helyette.

![Kapcsolatok referencia](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Érzékelő PIN-kód használja a következő kábelezés:


| Kezdő (érzékelő.)           | Teljes (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| VDD (27A PIN-kód)            | 3V (PIN-kód 3A)            | Piros kábel     |
| GND (29A PIN-kód)            | GND [PIN-kód 6]           | Fekete kábellel   |
| SCK (30A PIN-kód)            | SCK (12A PIN-kód)          | Sárga kábel  |
| SDO (31A PIN-kód)            | MI (14A PIN-kód)           | Fehér kábel   |
| SDI (32A PIN-kód)            | M0 (13A PIN-kód)           | Kék kábel    |
| CS (33A PIN-kód)             | GPIO 5 (15J PIN-kód)       | Narancssárga kábel  |

További információkért lásd: [Adafruit BME280 páratartalom + légköri nyomás + hőmérséklet-érzékelő kiscsoportos](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) és [Adafruit Feather M0 WiFi érintkezőkiosztása szerepel](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Most már a Feather M0 WiFi kell csatlakoztatni a működő érzékelő.

![Feather Huzzah DHT22 összekapcsolása](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Csatlakozás a számítógép Feather M0 WiFi

Csatlakozhat a Micro USB-Kapcsolattal a típust egy USB-kábelen keresztül Feather M0 WiFi a számítógép látható módon:

![Feather Huzzah kapcsolódni a számítógéphez](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adja hozzá a soros port engedélyek (csak Ubuntu)

Ubuntu használatakor győződjön meg arról, hogy az engedélyeket a Feather M0 WiFi USB portot a művelethez használandó. Soros portos engedélyek hozzáadásához kövesse az alábbi lépéseket:


1. A terminálban futtassa a következő parancsokat:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek egyik kap:

   * crw-rw---1 a legfelső szintű uucp xxxxxxxx
   * crw-rw---1 a legfelső szintű kitárcsázáshoz xxxxxxxx

   A kimenetben láthatja, hogy `uucp` vagy `dialout` az USB-port a csoport tulajdonosának neve.

2. Adja hozzá a felhasználót a csoporthoz, futtassa a következő parancsot:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   Az előző lépésben beszerzett, a csoport tulajdonosának neve `<group-owner-name>`. Az Ubuntu felhasználónév `<username>`.

3. A módosítás jelenik meg jelentkezzen ki az Ubuntu, és jelentkezzen be újra.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelőktől kapott adatok gyűjtésére, és küldje el az IoT hubhoz

Ebben a szakaszban üzembe és futtathat egy mintaalkalmazást a Feather M0 WiFi. A mintaalkalmazás a LED kurzorvillogás Feather M0 WiFi teszi. Ezután elküldi a begyűjtött az IoT hubhoz a BME280 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatokat.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>A mintaalkalmazás beszerzése a Githubról, és készítse elő az Arduino IDE

A mintaalkalmazás GitHub-ban üzemel. Klónozza a mintatárházat, amely tartalmazza a mintaalkalmazást a Githubról. A minta tárház klónozásához, kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy terminálablakot.

2. Nyissa meg egy mappára, ahol a mintaalkalmazás kell tárolni.
3. Futtassa az alábbi parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Telepítse a csomagot a Feather M0 WiFi a Arduino ide

1. Nyissa meg a mappát, ahol a mintaalkalmazás tárolja.

2. Nyissa meg a app.ino fájlt az alkalmazás mappája az Arduino IDE-ben.

   ![Nyissa meg a mintaalkalmazást a Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Kattintson a **fájl** > **beállítások** (Windows/Linux) vagy **Arduino** > **beállítások** (Mac), és másolja és illessze be az alábbi hivatkozásra a **további táblákon való Manager URL-címek** lehetőség az Arduino IDE beállítások között.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Kattintson a **eszközök** > **tábla** > **célra szolgáló kártyákat kezelő**, majd telepítse a `Arduino SAMD Boards` verzió `1.6.2` vagy újabb. 

1. Majd a azonos ablakában telepítse `Adafruit SAMD Boards` csomagot adja hozzá a táblához fájl definíciókat.

   ![Az esp8266 csomag telepítve van](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Kattintson a **eszközök** > **tábla** > **Adafruit M0 WiFi**.

5. Illesztőprogramok telepítéséhez (csak Windows). Feather M0 WiFi csatlakoztatásakor szüksége lehet illesztőprogramot telepíteni. Kattintson a [a letöltési hivatkozás a weblapon](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) , töltse le az illesztőprogramot. Kövesse a lépéseket a kívánt illesztőprogramok telepítéséhez.

### <a name="install-necessary-libraries"></a>Szükséges kódtárak telepítése

1. Az Arduino IDE-ben kattintson **rajz** > **tartalmaznak könyvtár** > **szalagtárak kezelése**.

2. Keresse meg a következő könyvtár egyenként neveket. Minden könyvtár talált, kattintson a **telepítése**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Telepítse manuálisan `Adafruit_WINC1500`. Lépjen a [ezen a webhelyen](https://github.com/adafruit/Adafruit_WINC1500) kattintson **Klónozás vagy letöltés** > **ZIP letöltése**. Az Arduino ide használatával, folytassa a **rajz** > **tartalmaznak könyvtár** > **.zip könyvtár hozzáadása** , és adja hozzá a zip-fájlt.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>A mintaalkalmazás használja, ha nem rendelkezik BME280 valódi érzékelő

Ha nem rendelkezik BME280 valódi érzékelő, a mintaalkalmazás szimulálhatja hőmérséklettel és páratartalommal kapcsolatos adatokat. Szimulált adatok használata a mintaalkalmazás beállításához, kövesse az alábbi lépéseket:

1. Nyissa meg a `config.h` fájlt a `app` mappát.

2. Keresse meg a következő kódsort, és módosítsa az értéket `false` való `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![A mintaalkalmazás szimulált adatok konfigurálása](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Mentse a fájlt a `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>A minta Feather M0 WiFi-alkalmazás üzembe helyezése

1. Az Arduino IDE-ben kattintson **eszköz** > **Port**, majd kattintson a soros port Feather M0 WiFi számára.

2. Kattintson a **rajz** > **feltöltése** készíthet és helyezhet üzembe a Feather M0 WiFi-mintaalkalmazást.

### <a name="enter-your-credentials"></a>Adja meg hitelesítő adatait

A feltöltés sikeres befejeződése után adja meg hitelesítő adatait az alábbi lépésekkel:

1. Az Arduino IDE-ben kattintson **eszközök** > **soros figyelő**.

2. A soros figyelő ablakának jobb alsó sarokban, válassza ki **nincs sor vége** a legördülő listából válassza ki a bal oldalon található.
3. Válassza ki **115200 átviteli** a legördülő listában a jobb oldalon.
4. Az oldal tetején a beviteli mezőbe, adja meg az az alábbi adatokat, ha az kéri, hogy adja meg azt, majd kattintson **küldése**:

   * Wi-Fi SSID
   * Wi-Fi-jelszó
   * Eszköz kapcsolati karakterláncát

> [!Note]
> A hitelesítő adatokat EEPROM Feather M0 WiFi van tárolva. A Feather M0 WiFi táblán a Visszaállítás gombra kattint, a mintaalkalmazás megkérdezi, hogy szeretné-e törölni az adatokat. Adja meg `Y` törli az adatokat. Az adatok egy második alkalommal kéri.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Ellenőrizze, hogy a mintaalkalmazás sikeresen fut-e

Ha a soros figyelő ablakból és a villogó LED a következő kimenet látható Feather M0 WiFi, a mintaalkalmazás sikeresen fut:

![Végeredmény a Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>További lépések

Sikeresen Feather M0 WiFi csatlakozik az IoT hub és az IoT hubnak küldött rögzített érzékelőadatokat. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

