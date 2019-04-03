---
title: A felhőbe – ESP8266 Feather HUZZAH ESP8266 csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ismerje meg, hogyan és beállítása és kapcsolódás Adafruit Feather HUZZAH ESP8266 Azure IoT Hub, az Azure felhőalapú platformján ebben az oktatóanyagban adatokat küldeni.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 82e31c498c6148454a5790a6eb4b82e3a2300a92
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847066"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather HUZZAH ESP8266 csatlakoztatása Azure IoT hubhoz a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22 Feather HUZZAH ESP8266 és az IoT Hub közötti kapcsolat](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>TEENDŐ

Adafruit Feather HUZZAH ESP8266 csatlakozni az IoT hub által létrehozott. Ezután egy mintaalkalmazás futtatunk ESP8266 DHT22 érzékelő hőmérséklettel és páratartalommal kapcsolatos adatok gyűjtését. Végül az érzékelő adatokat küld az IoT hubnak.

> [!NOTE]
> Ha használ egyéb ESP8266 célra szolgáló kártyákat, ezeket a lépéseket csatlakozni az IoT hub továbbra is követheti. Az ESP8266 táblához használ, attól függően előfordulhat, hogy szeretne a `LED_PIN`. Például ha ESP8266 az AI-Thinker használ, érdemes lehet módosítani a `0` való `2`. Még nem rendelkezik egy csomagot? A letöltés a [Azure-webhelyen](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Ismertetett témák

* IoT hub létrehozása és eszköz regisztrálása az Feather HUZZAH ESP8266
* Az érzékelő és a számítógép Feather HUZZAH ESP8266 csatlakoztatása
* A mintaalkalmazás futtat Feather HUZZAH ESP8266 érzékelőktől kapott adatok gyűjtéséről
* Az érzékelőktől kapott adatok küldése az IoT hubhoz

## <a name="what-you-need"></a>Mi szükséges

![Az oktatóanyaghoz szükség részei](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Ez a művelet végrehajtásához a következő részek a Feather HUZZAH ESP8266 Starter Kit a kell:

* A Feather HUZZAH ESP8266 tábla
* A Micro USB-Kapcsolattal a típust egy USB-kábelen keresztül

A fejlesztési környezetet is kell az alábbiakat:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiók [hozzon létre egy ingyenes Azure próbafiókot](https://azure.microsoft.com/free/) mindössze néhány perc múlva.
* Mac vagy Windows- vagy Ubuntu rendszert futtató számítógép.
* Feather HUZZAH ESP8266 szeretne csatlakozni a vezeték nélküli hálózathoz.
* Töltse le a konfigurációs eszközt internetkapcsolaton keresztül.
* [A Visual Studio Code-bővítmény Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> A Visual Studio Code-bővítménnyel használja Arduino azt kell verzió 1.6.8 Arduino IDE verzió vagy újabb. Korábbi verziók nem működnek a AzureIoT könyvtárhoz.

A következő elemek nem kötelezők, abban az esetben nem kell érzékelő. Akkor is szimulált érzékelői adatokat használja.

* Az Adafruit DHT22 hőmérséklettel és páratartalommal kapcsolatos érzékelő.
* Egy breadboard
* Millió/M átkötés fenyegetéseknek

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Az érzékelő és a számítógép Feather HUZZAH ESP8266 csatlakoztatása

Ebben a szakaszban az érzékelők a táblához való csatlakozáshoz. Majd, az eszköz csatlakoztatása a számítógépre, további használatra.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Csatlakozás egy DHT22 hőmérsékleti és páratartalom-érzékelőt Feather HUZZAH ESP8266

Használják a breadboard és átkötés módon hozhatja létre a kapcsolatot. Ha nem rendelkezik olyan érzékelő, kihagyhatja ezt a szakaszt, mert a szimulált érzékelői adatokat használhatja helyette.

![Kapcsolatok referencia](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Érzékelő PIN-kód használja a következő kábelezés:

| Indítsa el az (érzékelő.)           | Teljes (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------  |
| VDD (31F PIN-kód)            | 3V (rögzítése 58H)           | Piros kábel     |
| ADATOK (32F PIN-kód)           | GPIO 2 (46A PIN-kód)       | Kék kábel    |
| GND (34F PIN-kód)            | GND (56I PIN-kód)          | Fekete kábellel   |

További információkért lásd: [Adafruit DHT22 érzékelő telepítése](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) és [Adafruit Feather HUZZAH Esp8266 érintkezőkiosztása szerepel](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Most már a Feather Huzzah ESP8266 kell csatlakoztatni a működő érzékelő.

![Feather Huzzah DHT22 összekapcsolása](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Feather HUZZAH ESP8266 kapcsolódni a számítógéphez

Ahogyan tovább, a Micro USB-Kapcsolattal a típust egy USB-kábel használatával Feather HUZZAH ESP8266 kapcsolódni a számítógéphez.

![Feather Huzzah kapcsolódni a számítógéphez](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adja hozzá a soros port engedélyek (csak Ubuntu)

Ubuntu használatakor győződjön meg arról, hogy az engedélyeket a Feather HUZZAH ESP8266 USB portot a művelethez használandó. Soros portos engedélyek hozzáadásához kövesse az alábbi lépéseket:

1. Parancsot egy terminálban futtassa a következő parancsokat:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek egyik kap:

   * crw-rw---1 a legfelső szintű uucp xxxxxxxx
   * crw-rw---1 a legfelső szintű kitárcsázáshoz xxxxxxxx

   A kimenetben láthatja, hogy `uucp` vagy `dialout` az USB-port a csoport tulajdonosának neve.

2. Adja hozzá a felhasználót a csoporthoz a következő parancs futtatásával:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` az a csoport tulajdonosa név az előző lépésben beszerzett. `<username>` az Ön Ubuntu felhasználó neve.

3. Jelentkezzen ki az Ubuntu, és jelentkezzen be újra a módosítás megjelenik.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelőktől kapott adatok gyűjtésére, és küldje el az IoT hubhoz

Ebben a szakaszban üzembe és futtathat egy mintaalkalmazást a Feather HUZZAH ESP8266. A mintaalkalmazás a LED a Feather HUZZAH ESP8266 v milisekundách, és az IoT hubhoz a DHT22 érzékelő összegyűjtött hőmérséklettel és páratartalommal kapcsolatos adatokat küld.

### <a name="get-the-sample-application-from-github"></a>A mintaalkalmazás beszerzése a Githubról

A mintaalkalmazás GitHub-ban üzemel. Klónozza a mintatárházat, amely tartalmazza a mintaalkalmazást a Githubról. A minta tárház klónozásához, kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy terminálablakot.

2. Nyissa meg egy mappára, ahol a mintaalkalmazás kell tárolni.

3. Futtassa az alábbi parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Ezután telepítse a csomagot a Feather HUZZAH ESP8266 Visual Studio Code-ban.

4. Nyissa meg a mappát, ahol a mintaalkalmazás tárolja.

5. Nyissa meg a app.ino fájlt a Visual Studio Code az alkalmazás mappájában.

   ![A mintaalkalmazás megnyitásához a Visual Studio Code-ban](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Adja meg a Visual Studio Code-ban `F1`.

7. Típus **Arduino** válassza **Arduino: Üzenőfal Manager**.

8. Az a **Arduino tábla Manager** lapra, majd **további URL-címek**.

   ![A VS Code Arduino tábla Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. Az a **felhasználói beállítások** ablakban másolja és illessze be a következő parancsot a fájl vége

   ```json
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Arduino csomag URL-cím konfigurálása a VS Code-ban](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Mentse a fájlt, és zárja be a **felhasználói beállítások** fülre.

11. Kattintson a **csomag indexek frissítése**. A frissítés befejezése után keressen **esp8266**.

12. Kattintson a **telepítése** esp8266 gombra.

    Tanácsok Manager azt jelzi, hogy telepítve van-e a ESP8266 és a egy 2.2.0 vagy újabb verziója.

    ![Az esp8266 csomag telepítve van](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Adja meg `F1`, majd írja be a **Arduino** válassza **Arduino: Board Config**.

14. Jelölje be a **kijelölt táblához:** , és írja be **esp8266**, majd **Adafruit HUZZAH ESP8266 (esp8266)**.

    ![Esp8266 tábla kiválasztása](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Szükséges kódtárak telepítése

1. Adja meg a Visual Studio Code-ban `F1`, majd írja be a **Arduino** válassza **Arduino: Szalagtár Manager**.

2. Keresse meg a következő könyvtár egyenként neveket. Minden könyvtár talált, kattintson a **telepítése**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nincs valós DHT22 érzékelő?

A mintaalkalmazás abban az esetben nem kell valódi érzékelő DHT22 szimulálhat hőmérséklettel és páratartalommal kapcsolatos adatokat. Szimulált adatok használata a mintaalkalmazás beállításához, kövesse az alábbi lépéseket:

1. Nyissa meg a `config.h` fájlt a `app` mappát.

2. Keresse meg a következő kódsort, és módosítsa az értéket `false` való `true`:

   ```c
   define SIMULATED_DATA true
   ```

   ![A mintaalkalmazás szimulált adatok konfigurálása](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Mentse a fájlt.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>A mintaalkalmazás Feather HUZZAH ESP8266 üzembe helyezése

1. A Visual Studio Code-ban kattintson **<Select Serial Port>** állapotát, és majd kattintson a soros port Feather HUZZAH ESP8266 a.

2. Adja meg `F1`, majd írja be a **Arduino** válassza **Arduino: Töltse fel** készíthet és helyezhet üzembe a mintaalkalmazás Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Adja meg hitelesítő adatait

A feltöltés sikeres befejeződése után adja meg hitelesítő adatait az alábbi lépésekkel:

1. Nyissa meg az Arduino IDE, kattintson **eszközök** > **soros figyelő**.

2. A soros figyelő ablakban figyelje meg, hogy a két legördülő lista a jobb alsó sarokban.

3. Válassza ki **nincs sor vége** a bal oldali legördülő listát.

4. Válassza ki **115200 átviteli** a megfelelő legördülő listát.

5. A soros figyelő ablak tetején található beviteli mezőbe, írja be az a következő információkat, ha a rendszer megkéri, hogy adja meg őket, és kattintson a **küldése**.

   * Wi-Fi SSID
   * Wi-Fi-jelszó
   * Eszköz kapcsolati karakterláncát

> [!Note]
> A hitelesítő adatokat EEPROM Feather HUZZAH ESP8266 van tárolva. A Feather HUZZAH ESP8266 táblán a Visszaállítás gombra kattint, a mintaalkalmazás megkérdezi, hogy szeretné-e törölni az adatokat. Adja meg `Y` az adatok törölve lesz. Adja meg az adatokat egy második alkalommal kéri.

### <a name="verify-the-sample-application-is-running-successfully"></a>A mintaalkalmazás sikeresen fut-e

Ha a soros figyelő ablakból és a villogó LED a következő kimenet látható Feather HUZZAH ESP8266, a mintaalkalmazás sikeresen fut.

![Végeredmény a Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Olvassa el a hub által fogadott üzeneteket

Egy figyelheti az eszközről az IoT hub által fogadott üzeneteket módja az Azure IoT-eszközök használata a Visual Studio Code. További tudnivalókért lásd: [használata az Azure IoT Tools for Visual Studio Code használatával az eszközön, és az IoT Hub közötti üzenetek küldése és fogadása](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

A további lehetőségek a eszköz által küldött adatok feldolgozásához folytassa a következő szakaszban.

## <a name="next-steps"></a>További lépések

Sikeresen egy Feather HUZZAH ESP8266 csatlakozik az IoT hubhoz, és az IoT hubnak küldött rögzített érzékelőadatokat.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]