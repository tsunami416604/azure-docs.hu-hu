---
title: A felhőbe – csatlakozás Sparkfun ESP8266 Thing Dev Azure IoT hubra ESP8266 |} A Microsoft Docs
description: Ismerje meg, hogyan és beállítása és kapcsolódás Sparkfun ESP8266 Thing Dev Azure IoT Hub, az Azure felhőalapú platformján ebben az oktatóanyagban adatokat küldeni.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452515"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Sparkfun ESP8266 Thing Dev csatlakoztatása Azure IoT hubhoz a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22 Thing Dev és az IoT Hub közötti kapcsolat](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Mit fog

Sparkfun ESP8266 Thing Dev csatlakozni az IoT hub fogja létrehozni. Hőmérséklettel és páratartalommal kapcsolatos adatok gyűjtését DHT22 érzékelő ESP8266 futtassa a mintaalkalmazást. Végül az érzékelő adatokat küld az IoT hub.

> [!NOTE]
> Ha más ESP8266 célra szolgáló kártyákat használ, ezeket a lépéseket csatlakozni az IoT hub továbbra is követheti. Az ESP8266 táblához használ, attól függően szükség lehet konfigurálja újra a `LED_PIN`. Például az AI-Thinker ESP8266 használatakor előfordulhat, hogy módosítja a `0` való `2`. Még nem rendelkezik egy kit?: kattintson a [Itt](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Amiről tanulni fog

* IoT hub létrehozása és eszköz regisztrálása dolog helyőrzőkivétel fejlesztői útmutató
* Hogyan Thing Dev az érzékelő és a számítógép csatlakozni.
* A mintaalkalmazás futtat dolog helyőrzőkivétel fejlesztői érzékelőktől kapott adatok gyűjtéséről
* Az érzékelő adatokat küld az IoT hub hogyan.

## <a name="what-you-will-need"></a>Mit kell

![Az oktatóanyaghoz szükség részei](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Ez a művelet végrehajtásához a következő részek a Thing Dev alapszintű csomag a kell:

* A Sparkfun ESP8266 Thing Dev kártya.
* A Micro USB-típus egy USB-kábellel.

A fejlesztési környezetet is kell a következőket:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.
* Mac vagy Windows- vagy Ubuntu rendszert futtató számítógép.
* Sparkfun ESP8266 Thing Dev szeretne csatlakozni a vezeték nélküli hálózathoz.
* A konfigurációs eszköz letöltéséhez internetkapcsolat.
* [Arduino IDE](https://www.arduino.cc/en/main/software) verzió 1.6.8 (vagy újabb) a korábbi verziók nem fog működni a AzureIoT könyvtárhoz.

A következő elemek nem kötelezők, abban az esetben nem kell érzékelő. Akkor is szimulált érzékelői adatokat használja.

* Az Adafruit DHT22 hőmérséklettel és páratartalommal kapcsolatos érzékelő.
* Egy breadboard.
* Millió/M átkötés fenyegetéseknek.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Az érzékelő és a számítógép összekapcsolása ESP8266 Thing Dev

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Csatlakozás egy DHT22 hőmérsékleti és páratartalom-érzékelőt ESP8266 Thing Dev

Használják a breadboard és átkötés módon hozhatja létre a kapcsolatot. Ha nem rendelkezik olyan érzékelő, kihagyhatja ezt a szakaszt, mert a szimulált érzékelői adatokat használhatja helyette.

![Kapcsolatok referencia](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

A PIN-kódjaihoz érzékelő a következő kábelezés használjuk:

| Indítsa el az (érzékelő.)           | Teljes (tábla)           | Kábel színe   |
| -----------------------  | ---------------------- | ------------: |
| VDD (27F PIN-kód)            | 3V (PIN-kód 8A)           | Piros kábel     |
| ADATOK (28F PIN-kód)           | GPIO 2 (pedig a 9A PIN-kód)       | Fehér kábel    |
| GND (30F PIN-kód)            | GND (PIN-kód 7J)          | Fekete kábellel   |


- További információkért lásd: [DHT22 érzékelő telepítése](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) és [Sparkfun ESP8266 Thing Dev specifikáció](https://www.sparkfun.com/products/13711)

Most már a Sparkfun ESP8266 Thing Dev kell csatlakoztatni a működő érzékelő.

![az ESP8266 Thing Dev dht22 csatlakoztatása](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Sparkfun ESP8266 Thing Dev kapcsolódni a számítógéphez

A Micro USB-Kapcsolattal a típust egy USB-kábel használatával Sparkfun ESP8266 Thing Dev módon csatlakozhat a számítógép.

![feather huzzah kapcsolódni a számítógéphez](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Adja hozzá a soros port engedélyekkel – csak Ubuntu

Ubuntu használatakor ellenőrizze, hogy egy normál felhasználók számára a működnek a az USB port az Sparkfun ESP8266 Thing Dev. Soros port engedélyek egy normál felhasználó hozzáadásához kövesse az alábbi lépéseket:

1. Parancsot egy terminálban futtassa a következő parancsokat:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek egyik kap:

   * crw-rw---1 a legfelső szintű uucp xxxxxxxx
   * crw-rw---1 a legfelső szintű kitárcsázáshoz xxxxxxxx

   A kimenetben láthatja `uucp` vagy `dialout` , amely a csoport tulajdonosa nevére az USB-port.

1. Adja hozzá a felhasználót a csoporthoz a következő parancs futtatásával:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` az a csoport tulajdonosa név az előző lépésben beszerzett. `<username>` az Ön Ubuntu felhasználó neve.

1. Jelentkezzen ki az Ubuntu, és jelentkezzen be a módosítás érvénybe lépéséhez újra.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelőktől kapott adatok gyűjtésére, és küldje el az IoT hubhoz

Ebben a szakaszban üzembe és futtathat egy mintaalkalmazást a Sparkfun ESP8266 Thing Dev. A mintaalkalmazás v milisekundách a LED a Sparkfun ESP8266 Thing Dev és az IoT hubhoz a DHT22 érzékelő összegyűjtött hőmérséklettel és páratartalommal kapcsolatos adatokat küld.

### <a name="get-the-sample-application-from-github"></a>A mintaalkalmazás beszerzése a Githubról

A mintaalkalmazás GitHub-ban üzemel. Klónozza a mintatárházat, amely tartalmazza a mintaalkalmazást a Githubról. A minta tárház klónozásához, kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy terminálablakot.
1. Nyissa meg egy mappára, ahol a mintaalkalmazás kell tárolni.
1. Futtassa az alábbi parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Telepítse a csomagot a Sparkfun ESP8266 Thing Dev Arduino IDE-ben:

1. Nyissa meg a mappát, ahol a mintaalkalmazás tárolja.
1. Nyissa meg a app.ino fájlt az alkalmazás mappája Arduino IDE-ben.

   ![Nyissa meg a mintaalkalmazást a arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Az Arduino IDE-ben kattintson **fájl** > **beállítások**.
1. Az a **beállítások** párbeszédpanel mellett kattintson az ikonra a a **további táblákon való Manager URL-címek** szövegmezőben.
1. Az előugró ablakban adja meg a következő URL-címet, és kattintson **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![arduino ide-ben a csomag URL-címre mutasson](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. Az a **szabályozó** párbeszédpanelen kattintson a **OK**.
1. Kattintson a **eszközök** > **tábla** > **célra szolgáló kártyákat kezelő**, és keressen esp8266.
   ESP8266 2.2.0 vagy újabb verzióját kell telepíteni.

   ![Az esp8266 csomag telepítve van](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Kattintson a **eszközök** > **tábla** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Szükséges kódtárak telepítése

1. Az Arduino IDE-ben kattintson **rajz** > **tartalmaznak könyvtár** > **szalagtárak kezelése**.
1. Keresse meg a következő könyvtár egyenként neveket. A könyvtárban találja mindegyik esetében kattintson **telepítése**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nincs valós DHT22 érzékelő?

A mintaalkalmazás abban az esetben nem kell valódi érzékelő DHT22 szimulálhat hőmérséklettel és páratartalommal kapcsolatos adatokat. Ahhoz, hogy a mintaalkalmazás szimulált adatokat használja, kövesse az alábbi lépéseket:

1. Nyissa meg a `config.h` fájlt a `app` mappát.
1. Keresse meg a következő kódsort, és módosítsa az értéket `false` való `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![A mintaalkalmazás szimulált adatok konfigurálása](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Takarékoskodjon `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Sparkfun ESP8266 Thing Dev a mintaalkalmazás üzembe helyezése

1. Az Arduino IDE-ben kattintson **eszköz** > **Port**, majd kattintson a soros port Sparkfun ESP8266 Thing helyőrzőkivétel fejlesztői számára
1. Kattintson a **rajz** > **feltöltése** készíthet és helyezhet üzembe a mintaalkalmazás Sparkfun ESP8266 Thing Dev.

> [!Note]
> Ha sikerült valószínűleg látni feltöltése során a következő üzenetek macOS használ. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Nyissa meg a ternimal ablakot, és fejezze be a probléma megoldásához műveletek alá.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Adja meg hitelesítő adatait

Befejeződött a feltöltés sikeres, kövesse a lépéseket adja meg hitelesítő adatait:

1. Az Arduino IDE-ben kattintson **eszközök** > **soros figyelő**.
1. A soros figyelő ablakban figyelje meg, hogy a két legördülő lista a jobb alsó sarokban.
1. Válassza ki **nincs sor vége** a bal oldali legördülő listát.
1. Válassza ki **115200 átviteli** a megfelelő legördülő listát.
1. A soros figyelő ablak tetején található beviteli mezőbe, írja be az a következő információkat, ha a rendszer megkéri, hogy adja meg őket, és kattintson a **küldése**.
   * Wi-Fi SSID
   * Wi-Fi-jelszó
   * Eszköz kapcsolati karakterláncát

> [!Note]
> A hitelesítő adatokat tárolja a EEPROM, Sparkfun ESP8266 Thing Dev. Ha a Sparkfun ESP8266 Thing Dev táblán a Visszaállítás gombra kattint, a mintaalkalmazás rákérdez, hogy szeretné-e törölni az adatokat. Adja meg `Y` szeretné, hogy a törölt adatokat, és a rendszer felkéri újra megadnia az adatokat.

### <a name="verify-the-sample-application-is-running-successfully"></a>A mintaalkalmazás sikeresen fut-e

Ha a soros figyelő ablakból és a villogó LED a következő kimenet látható Sparkfun ESP8266 Thing Dev, a mintaalkalmazás sikeresen fut.

![végeredmény a arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>További lépések

Sikeresen egy Sparkfun ESP8266 Thing Dev csatlakozik az IoT hub és az IoT hubnak küldött rögzített érzékelőadatokat. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
