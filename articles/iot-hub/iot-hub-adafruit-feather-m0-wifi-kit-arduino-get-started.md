---
title: "A felhőalapú m0: lágyított M0 Wi-Fi csatlakozni az Azure IoT Hub |} Microsoft Docs"
description: "Megtudhatja, hogyan állítson be és Adafruit lágyított M0 Wi-Fi csatlakozni az Azure IoT Hub adatokat küldeni az Azure felhőalapú platform ebben az oktatóanyagban."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/16/2017
ms.author: xshi
ms.openlocfilehash: 9b278735ce3af9e6e61a85c5e95ea218622361c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Csatlakozás Adafruit lágyított M0 Wi-Fi Azure IoT Hub a felhőben
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Egy BME280 lágyított M0 Wi-Fi és az IoT-központ közötti kapcsolat](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Ebben az oktatóanyagban akkor először tanulás alapjainak a Arduino board használatifeltétel. Majd megtudhatja, hogyan kapcsolódhat zökkenőmentesen az eszközök a felhőbe [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Mit

Az IoT-központ az Ön által létrehozott Adafruit lágyított M0 Wi-Fi csatlakozni. Majd futtassa a mintaalkalmazást a hőmérséklet és a páratartalom adatokat gyűjteni a BME280 M0 Wi-Fi a. Végezetül az érzékelő adatokat küldött az IoT hub.


## <a name="what-you-learn"></a>Ismertetett témák

* Létrehoz egy IoT-központot, és lágyított M0 Wi-Fi az eszköz regisztrálása
* Wi-Fi M0 lágyított az érzékelő és a számítógép összekapcsolása
* Futtatja a mintaalkalmazás lágyított M0 Wi-Fi érzékelői adatok gyűjtéséről
* Útmutató az érzékelő adatokat küldeni az IoT hub

## <a name="what-you-need"></a>Mi szükséges

![Az oktatóanyaghoz szükség részei](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

A művelet végrehajtásához a következő részek a lágyított M0 Wi-Fi Starter Kit kell:

* A Wi-Fi M0 lágyított tábla
* A típus egy USB-kábel Micro USB

A fejlesztési környezetet is kell a következőket:

* Aktív Azure-előfizetés. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
* A Mac vagy Windows vagy az Ubuntu rendszert futtató számítógép.
* Vezeték nélküli hálózat lágyított M0 Wi-Fi való csatlakozáshoz.
* A kiszolgálókonfigurációs eszköz letöltéséhez internetkapcsolat.
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 verzió vagy újabb. Az Azure IoT Hub könyvtár korábbi verziói nem működik.

Ha az érzékelő nincs, a következő elemek nem kötelező. Akkor is szimulált érzékelőadatait használatát:

* BME280 hőmérséklet és a páratartalom érzékelő
* Egy breadboard
* M/M átkötés fenyegetéseknek

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Csatlakozás lágyított M0 Wi-Fi az érzékelő és a számítógép
Ebben a szakaszban csatlakozhat az érzékelők a tábla. Majd a eszközt csatlakoztat a számítógéphez további használatra.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Csatlakozás DHT22 hőmérséklet és a páratartalom érzékelő lágyított M0 WiFi

Használják a breadboard és átkötés a kapcsolathoz. Ha még nem rendelkezik érzékelő, ez a szakasz kihagyása, mert a szimulált érzékelő adatokat helyette használhatja.

![Kapcsolatok referencia](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


PIN-kód érzékelő használja a következő vezetékezést:


| Kezdő (érzékelő)           | Záró (tábla)            | Kábel szín   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kód 27A)            | 3V (PIN-kód 3A)            | Piros kábel     |
| GND (PIN-kód 29A)            | GND [PIN-kód 6]           | Fekete kábel   |
| SCK (PIN-kód 30A)            | SCK (PIN-kód 12A)          | Sárga kábel  |
| SDO (PIN-kód 31A)            | MI (PIN-kód 14A)           | A fehér kábel   |
| SDI (PIN-kód 32A)            | M0 (PIN-kód 13A)           | Kék kábel    |
| CS (PIN-kód 33A)             | GPIO 5 (PIN-kód 15J)       | Narancssárga kábel  |

További információkért lásd: [Adafruit BME280 páratartalom + légnyomás + hőmérséklet-érzékelő kitörése](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) és [Adafruit lágyított M0 Wi-Fi érintkezőkiosztása szerepel](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Most már a lágyított M0 Wi-Fi kell csatlakoztatni a működő érzékelő.

![Csatlakozás DHT22 lágyított Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Wi-Fi M0 lágyított kapcsolódni a számítógéphez

A típus egy USB-kábel Micro USB való csatlakozáskor használandó lágyított M0 Wi-Fi a számítógép látható módon:

![Lágyított Huzzah kapcsolódni a számítógéphez](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adja hozzá a soros port engedélyek (csak Ubuntu)

Ha Ubuntu használ, győződjön meg arról a engedélye ahhoz, hogy a lágyított M0 WiFi USB port működik. Soros port engedélyek hozzáadásához kövesse az alábbi lépéseket:


1. A terminálon futtassa a következő parancsokat:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek egyik beolvasása:

   * crw-rw---1 legfelső szintű uucp xxxxxxxx
   * crw-rw---1 legfelső szintű kitárcsázáshoz xxxxxxxx

   Figyelje meg, hogy a kimenet `uucp` vagy `dialout` a csoport tulajdonosának neve az USB-porttal.

2. A felhasználó hozzáadása a csoporthoz, a következő parancsot:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   Az előző lépésben beszerzett a csoport tulajdonosának neve `<group-owner-name>`. Ubuntu felhasználónév `<username>`.

3. A módosítás jelenik meg jelentkezzen ki Ubuntu, és jelentkezzen be újból.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelő adatokat gyűjteni, és küldje el az IoT hub

Ebben a szakaszban telepítése, és futtassa a mintaalkalmazást a lágyított M0 Wi-Fi. A mintaalkalmazás az LED villogási lágyított M0 Wi-Fi teszi. Ezután elküldi a hőmérséklet és a páratartalom gyűjtött adatokat a BME280 érzékelő az IoT hubhoz is.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>A mintaalkalmazás beszerzése a Githubról, és készítse elő a Arduino IDE

A mintaalkalmazás tárolja a Githubon. Klónozza a minta-tárház, amely tartalmazza a mintaalkalmazást a Githubról. A minta-tárház klónozása, kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy terminálablakot.

2. Nyissa meg a mappát, ahol a mintaalkalmazáshoz történő tárolását.
3. Futtassa az alábbi parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>A csomag telepítése lágyított M0 Wi-Fi a Arduino ide

1. Nyissa meg a mappát, ahol a mintaalkalmazás tárolja.

2. Nyissa meg a app.ino fájlt a Arduino ide app mappában.

   ![Nyissa meg a mintaalkalmazás Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Kattintson a **fájl** > **beállítások** (Windows/Linux) vagy **Arduino** > **beállítások** (Mac), másolja és illessze be az alábbi hivatkozásra a **további modulok Manager URL-címet** a Arduino IDE beállítások lehetőséget.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Kattintson a **eszközök** > **Board** > **modulok Manager**, majd telepítse a `Arduino SAMD Boards` verzió `1.6.2` vagy újabb. 

1. Az azonos ablakban telepítse `Adafruit SAMD Boards` csomag hozzáadása a tábla fájl definíciókat.

   ![A esp8266 csomag telepítve van](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Kattintson a **eszközök** > **Board** > **Adafruit M0 Wi-Fi**.

5. Illesztőprogramok telepítéséhez (csak Windows). Csatlakoztatásakor lágyított M0 Wi-Fi, szükség lehet illesztőprogramot telepíteni. Kattintson a [a letöltési hivatkozás a weblapon](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) az illesztőprogram-telepítő letöltéséhez. Kövesse a kívánt illesztőprogramok telepítéséhez.

### <a name="install-necessary-libraries"></a>Szükséges kódtárak telepítése

1. Kattintson a Arduino ide **vázlat** > **közé tartozik könyvtár** > **szalagtárak kezelése**.

2. Keresse meg a következő könyvtár nevek egyenként. Az egyes tárak talált, kattintson a **telepítése**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Telepítse manuálisan `Adafruit_WINC1500`. Nyissa meg a [ezen a webhelyen](https://github.com/adafruit/Adafruit_WINC1500) kattintson **Klónozás vagy letöltési** > **töltse le a ZIP-**. A Arduino ide, folytassa a **vázlat** > **közé tartozik könyvtár** > **.zip könyvtár hozzáadása** , és adja hozzá a zip-fájl.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>A mintaalkalmazás használja, ha nincs valós BME280 érzékelő

Ha egy valódi BME280 érzékelő nincs, a mintaalkalmazást szimulálhatja hőmérséklet és a páratartalom adatok. Állítsa be a mintaalkalmazást szimulált adatokat, kövesse az alábbi lépéseket:

1. Nyissa meg a `config.h` fájlt a `app` mappát.

2. Keresse meg a következő kódsort, és módosítsa az értéket `false` való `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurálja a mintaalkalmazás szimulált adatok használata](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Mentse a fájlt a `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>A mintaalkalmazás az lágyított M0 Wi-Fi telepítése

1. Kattintson a Arduino ide **eszköz** > **Port**, majd kattintson a soros port a lágyított M0 Wi-Fi.

2. Kattintson a **vázlat** > **feltöltése** felépítéséhez és az lágyított M0 Wi-Fi minta alkalmazást telepíti.

### <a name="enter-your-credentials"></a>Adja meg hitelesítő adatait

Ha sikeresen befejeződött a feltöltés, kövesse az alábbi lépéseket a hitelesítő adatok megadását:

1. Kattintson a Arduino ide **eszközök** > **soros figyelő**.

2. Válassza ki a soros figyelő ablak jobb alsó sarkában **nincs sor befejezési** a legördülő listában, a bal oldalon.
3. Válassza ki **115200 átviteli** jobb legördülő listáról.
4. A beviteli mezőbe, a bal felső, adja meg az az alábbi adatokat, ha megkérdezi, adja meg, és kattintson **küldése**:

   * Wi-Fi SSID
   * Wi-Fi jelszó
   * Eszköz kapcsolati karakterlánc

> [!Note]
> A hitelesítő adatokat a lágyított EEPROM M0 WiFi tárolódik. Ha a Wi-Fi M0 lágyított táblán a Visszaállítás gombra kattint, a mintaalkalmazást megkérdezi, hogy szeretné-e használni a. Adja meg `Y` törlésére az adatokat. Megkérdezi, hogy az adatok még egyszer.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Győződjön meg arról, hogy a mintaalkalmazás sikeresen fut.

Ha látható a következő kimeneti a soros figyelő ablakból és a villogó LED lágyított M0 Wi-Fi, a mintaalkalmazás sikeresen fut:

![Végső kimenetet a Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Következő lépések

Sikeresen csatlakoztatva az IoT hub lágyított M0 Wi-Fi és a rögzített érzékelő adatokat küldött az IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

