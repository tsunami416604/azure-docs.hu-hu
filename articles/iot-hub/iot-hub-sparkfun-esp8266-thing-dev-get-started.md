---
title: Felhőbe - csatlakozás Sparkfun ESP8266 dolog fejlesztés az Azure IoT Hub ESP8266 |} Microsoft Docs
description: Megtudhatja, hogyan kell beállítania, és Azure IoT-központ az adatokat küldeni az Azure felhőalapú platform ebben az oktatóanyagban Sparkfun ESP8266 dolog fejlesztői csatlakozni.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: e6837d0312217d8e27e3639b8220f5016a2505a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Csatlakozás Sparkfun ESP8266 dolog fejlesztői Azure IoT Hub a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22 dolog fejlesztői és az IoT-központ közötti kapcsolat](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Mit fog

Csatlakozás Sparkfun ESP8266 dolog fejlesztői során létrehoz egy IoT-központot. Ezután futtassa a mintaalkalmazást ESP8266 DHT22 érzékelő hőmérséklet és a páratartalom adatokat gyűjteni. Végezetül az érzékelő adatokat továbbít az IoT hub.

> [!NOTE]
> Ha használ egyéb ESP8266 modulok, ezeket a lépéseket az IoT hub csatlakozni továbbra is követheti. Attól függően, hogy a ESP8266 üzenőfalon használ, szükség lehet, hogy engedélyezzék a `LED_PIN`. Például ha az Eszközintelligencia-Thinker ESP8266 használ, módosíthatja az `0` való `2`. Még nem rendelkezik a csomag?: kattintson a [Itt](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Amiről tanulni fog

* Létrehoz egy IoT-központot, és az eszköz regisztrálása dolog helyőrzőkivétel fejlesztői útmutató
* Hogyan lehet az érzékelő és a számítógép dolog fejlesztői csatlakoztatni.
* Futtatja a mintaalkalmazás dolog helyőrzőkivétel fejlesztői érzékelői adatok gyűjtéséről
* Az érzékelő adatokat küldeni az IoT hub módjáról.

## <a name="what-you-will-need"></a>Mit kell

![Az oktatóanyaghoz szükség részei](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

A művelet végrehajtásához a következő részek a dolog fejlesztői Starter Kit kell:

* A Sparkfun ESP8266 dolog fejlesztői kártya.
* Egy Micro USB-típus egy USB-kábellel.

A fejlesztési környezetet is kell a következőket:

* Aktív Azure-előfizetés. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
* Mac vagy Windows vagy az Ubuntu rendszert futtató számítógép.
* Vezeték nélküli hálózat Sparkfun ESP8266 dolog fejlesztői való csatlakozáshoz.
* A kiszolgálókonfigurációs eszköz letöltéséhez internetkapcsolat.
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 verziót (vagy újabb), korábbi verziói nem fog működni a AzureIoT könyvtárban.

A következő elemek nem kötelező, abban az esetben, ha nincs érzékelő. Akkor is a szimulált érzékelőadatait használatának lehetőségét.

* Egy Adafruit DHT22 hőmérséklet és a páratartalom érzékelő.
* Egy breadboard.
* M/M átkötés fenyegetéseknek.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Csatlakozás az érzékelő és a számítógép ESP8266 dolog fejlesztői

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Csatlakozás ESP8266 dolog fejlesztői DHT22 hőmérséklet és a páratartalom érzékelő

Használják a breadboard és átkötés az alábbiak szerint a kapcsolathoz. Ha még nem rendelkezik érzékelő, ez a szakasz kihagyása, mert a szimulált érzékelő adatokat helyette használhatja.

![Kapcsolatok referencia](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Az érzékelő PIN-kód a következő vezetékezést használjuk:

| Indítsa el a (érzékelő)           | Záró (tábla)           | Kábel szín   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kód 27F)            | 3V (PIN-kód 8A)           | Piros kábel     |
| ADATOK (PIN-kód 28F)           | GPIO 2 (PIN-kód 9A)       | A fehér kábel    |
| GND (PIN-kód 30F)            | GND (PIN-kód 7J)          | Fekete kábel   |


- További információkért lásd: [DHT22 érzékelő telepítő](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) és [Sparkfun ESP8266 dolog fejlesztői specifikációja](https://www.sparkfun.com/products/13711)

Most már a Sparkfun ESP8266 dolog fejlesztői kell csatlakoztatni a működő érzékelő.

![Csatlakozás dht22 ESP8266 dolog fejlesztői](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Sparkfun ESP8266 dolog fejlesztői kapcsolódni a számítógéphez

A Micro USB,-típus egy USB-kábel segítségével Sparkfun ESP8266 dolog fejlesztői csatlakozni a számítógép az alábbiak szerint.

![lágyított huzzah kapcsolódni a számítógéphez](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Soros port engedélyek – csak Ubuntu hozzáadása

Ubuntu használatakor győződjön meg arról a normál felhasználói a jogosult működéséhez a a USB port a Sparkfun ESP8266 dolog helyőrzőkivétel fejlesztői A szokványos felhasználói engedélyeinek soros port hozzáadásához kövesse az alábbi lépéseket:

1. A következő parancsokat a terminálon:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek egyik beolvasása:

   * crw-rw---1 legfelső szintű uucp xxxxxxxx
   * crw-rw---1 legfelső szintű kitárcsázáshoz xxxxxxxx

   Figyelje meg, a kimenetben `uucp` vagy `dialout` , amely a csoport tulajdonosának neve az USB-porttal.

1. A felhasználó felvétele a csoportba a következő parancs futtatásával:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` az az előző lépésben beolvasott tulajdonos neve van. `<username>` Ubuntu felhasználónevének van.

1. Ubuntu kijelentkezés, és jelentkezzen be újra az a változtatás érvénybe léptetéséhez.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelő adatokat gyűjteni, és küldje el az IoT hub

Ebben a részben telepíti, és futtassa a mintaalkalmazást a Sparkfun ESP8266 dolog helyőrzőkivétel fejlesztői A mintaalkalmazás a Sparkfun ESP8266 dolog fejlesztői LED villogjon, és elküldi a hőmérséklet és a páratartalom gyűjtött adatokat a DHT22 érzékelő az IoT hubhoz.

### <a name="get-the-sample-application-from-github"></a>A mintaalkalmazás beszerzése a Githubról

A mintaalkalmazás tárolja a Githubon. Klónozza a minta-tárház, amely tartalmazza a mintaalkalmazást a Githubról. A minta-tárház klónozása, kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy terminálablakot.
1. Nyissa meg a mappát, ahol a mintaalkalmazáshoz történő tárolását.
1. Futtassa az alábbi parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

A csomag telepítése Sparkfun ESP8266 dolog fejlesztői Arduino ide:

1. Nyissa meg a mappát, ahol a mintaalkalmazás tárolja.
1. Nyissa meg a app.ino fájlt Arduino IDE az alkalmazás mappájában.

   ![Nyissa meg a mintaalkalmazás arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Kattintson a Arduino ide **fájl** > **beállítások**.
1. Az a **beállítások** párbeszédpanel mellett kattintson a ikonra a **további modulok Manager URL-címet** szövegmezőben.
1. Az előugró ablakban írja be a következő URL-címet, és kattintson **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Mutasson a arduino ide egy alkalmazáscsomag URL-címe](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. Az a **preferencia** párbeszédpanel, kattintson a **OK**.
1. Kattintson a **eszközök** > **Board** > **modulok Manager**, majd keresse meg a esp8266.
   ESP8266 2.2.0 vagy újabb verziójával kell telepíteni.

   ![A esp8266 csomag telepítve van](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Kattintson a **eszközök** > **Board** > **Sparkfun ESP8266 dolog fejlesztői**.

### <a name="install-necessary-libraries"></a>Szükséges kódtárak telepítése

1. Kattintson a Arduino ide **vázlat** > **közé tartozik könyvtár** > **szalagtárak kezelése**.
1. Keresse meg a következő könyvtár nevek egyenként. Az egyes megtalálta a könyvtárban kattintson **telepítése**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nincs valós DHT22 érzékelő?

A mintaalkalmazás szimulálhatja a hőmérséklet és a páratartalom adatok, abban az esetben, ha nincs valós DHT22 érzékelő. Ahhoz, hogy a mintaalkalmazás szimulált adatokat, kövesse az alábbi lépéseket:

1. Nyissa meg a `config.h` fájlt a `app` mappát.
1. Keresse meg a következő kódsort, és módosítsa az értéket `false` való `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurálja a mintaalkalmazás szimulált adatok használata](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Mentse `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>A minta alkalmazást Sparkfun ESP8266 dolog fejlesztői telepíti

1. Kattintson a Arduino ide **eszköz** > **Port**, majd kattintson a soros port Sparkfun ESP8266 dolog helyőrzőkivétel fejlesztői
1. Kattintson a **vázlat** > **feltöltése** létrehozásához és telepítéséhez a mintaalkalmazáshoz történő Sparkfun ESP8266 dolog helyőrzőkivétel fejlesztői

> [!Note]
> MacOS használata valószínűleg bemutatásához az alábbi üzenetek feltöltése során. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Nyissa meg a ternimal ablakot, és a probléma megoldásához műveletek alatt Befejezés.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Adja meg hitelesítő adatait

Ha sikeresen befejeződött a feltöltés, kövesse a hitelesítő adatok megadását:

1. Kattintson a Arduino ide **eszközök** > **soros figyelő**.
1. A soros figyelő ablakban figyelje meg, a két legördülő lista a jobb alsó sarokban.
1. Válassza ki **nincs sor befejezési** a bal oldali legördülő listát.
1. Válassza ki **115200 átviteli** jobb legördülő listája.
1. A beviteli mezőbe, a soros figyelő ablak tetején található, adja meg az az alábbi adatokat, ha a rendszer kéri azokat, és kattintson a **küldése**.
   * Wi-Fi SSID
   * Wi-Fi jelszó
   * Eszköz kapcsolati karakterlánc

> [!Note]
> A hitelesítő adatokat a EEPROM a Sparkfun ESP8266 dolog helyőrzőkivétel fejlesztői van tárolva. Ha a Sparkfun ESP8266 dolog fejlesztői táblán a Visszaállítás gombra kattint, a mintaalkalmazást megkérdezi, hogy szeretné-e használni a. Adja meg `Y` kell rendelkeznie az adatokat, törlése és a rendszer kéri újra az adatok.

### <a name="verify-the-sample-application-is-running-successfully"></a>Ellenőrizze a mintaalkalmazás sikeresen fut-e

A következő kimeneti a soros figyelő ablakból és a villogó LED Sparkfun ESP8266 dolog fejlesztői látható, ha sikeresen fut a mintaalkalmazáshoz.

![végső kimenetet a arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>További lépések

Sikeresen egy Sparkfun ESP8266 dolog fejlesztői csatlakozik az IoT hub és a rögzített érzékelő adatokat küldött az IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
