---
title: ESP8266 a Feather-HUZZAH ESP8266 az Azure IoT Hubba | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan telepítheti és csatlakozhat a adafruit Feather HUZZAH ESP8266 az Azure IoT Hubhoz, hogy az Azure Cloud platformba küldje az információkat.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 8e97a979c37af8ade51b4ff6ca4b2c5b4eec126e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232726"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>A adafruit Feather HUZZAH ESP8266 összekapcsolhatók az Azure IoT Hub a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Kapcsolat a DHT22, a Feather HUZZAH ESP8266 és a IoT Hub között](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Teendők

Csatlakoztathatja a adafruit Feather HUZZAH ESP8266 egy olyan IoT-hubhoz, amelyet Ön hozott létre. Ezután egy minta alkalmazást futtat a ESP8266-on a hőmérséklet és a páratartalom adatainak a DHT22-érzékelőből való összegyűjtéséhez. Végül elküldi az érzékelő adatait az IoT hubhoz.

> [!NOTE]
> Ha más ESP8266-kártyákat használ, továbbra is követheti ezeket a lépéseket az IoT hubhoz való kapcsolódáshoz. Az Ön által használt ESP8266 függően előfordulhat, hogy újra kell konfigurálnia a `LED_PIN`-t. Ha például a ESP8266-t használja az AI-Think szolgáltatásból, akkor előfordulhat, `0` hogy a verzióról a értékre `2`módosítja. Még nem rendelkezik csomaggal? Töltse le az [Azure](https://azure.com/iotstarterkits)-webhelyről.

## <a name="what-you-learn"></a>Ismertetett témák

* IoT hub létrehozása és az eszköz regisztrálása a Feather HUZZAH ESP8266
* A Feather HUZZAH ESP8266 összekötése az érzékelővel és a számítógéppel
* Az érzékelők adatainak összegyűjtése a Feather HUZZAH-ESP8266 futó minta alkalmazás futtatásával
* Az érzékelő adatainak elküldése az IoT hubhoz

## <a name="what-you-need"></a>Mi szükséges

![Az oktatóanyaghoz szükséges részek](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

A művelet végrehajtásához a Feather HUZZAH ESP8266 Starter Kit következő részeire lesz szüksége:

* A Feather HUZZAH ESP8266-táblája
* USB-kábel beírása a Micro USB-vel

A fejlesztési környezethez az alábbiakra is szükség van:

* Aktív Azure-előfizetés. Ha nem rendelkezik Azure-fiókkal, mindössze néhány perc alatt [hozzon létre egy ingyenes Azure próbaverziós fiókot](https://azure.microsoft.com/free/) .
* Windows vagy Ubuntu rendszert futtató Mac vagy PC.
* [GIT](https://git-scm.com/download)
* [Arduino](https://www.arduino.cc/en/main/software#download)
* [Adafruit Huzzah ESP8266-illesztőprogram USB-hez](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
* Egy vezeték nélküli hálózat, amely a Feather HUZZAH ESP8266 csatlakozik.
* Internetkapcsolat a konfigurációs eszköz letöltéséhez.
* [Visual Studio Code-bővítmény az Arduino-hez](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Az Arduino-hez készült Visual Studio Code-bővítmény által használt Arduino IDE-verziónak 1.6.8 vagy újabb verziónak kell lennie. A korábbi verziók nem működnek a AzureIoT könyvtárral.

Ha nem rendelkezik érzékelővel, a következő elemek választhatók. Lehetősége van szimulált szenzoros adatmennyiséget is használni.

* Adafruit DHT22 hőmérséklet-és páratartalom-érzékelő
* Egy kenyérvágódeszka
* M/M jumper huzalok

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>A Feather HUZZAH ESP8266 összekötése az érzékelővel és a számítógéppel

Ebben a szakaszban az érzékelőket a táblához kapcsolja. Ezután további használatra csatlakoztathatja az eszközt a számítógéphez.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>DHT22 hőmérséklet-és páratartalom-érzékelő összekötése a Feather HUZZAH ESP8266

A kenyérvágódeszka és a jumper huzalok segítségével a következő módon teheti meg a kapcsolódást. Ha még nem rendelkezik érzékelővel, ugorja át ezt a szakaszt, mert a szimulált szenzoros adattípusokat is használhatja.

![Kapcsolatok referenciája](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Az érzékelő PIN-kódokhoz használja a következő huzalozást:

| Indítás (érzékelő)           | Befejezés (tábla)            | Kábel színe   |
| -----------------------  | ---------------------- | ------------  |
| VDD (PIN-kód 31F)            | 3V (PIN-kód 58H)           | Piros kábel     |
| Adatkezelési (32F PIN-kód)           | 2\. GPIO (PIN-kód 46A)       | Kék kábel    |
| GND (PIN-kód 34F)            | GND (PIn-kód 56I)          | Fekete kábel   |

További információ: [ADAFRUIT DHT22-érzékelő beállítása](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) és [adafruit Feather HUZZAH Esp8266 pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Most a Feather Huzzah-ESP8266 egy működő érzékelővel kell összekapcsolni.

![A DHT22 és a Feather Huzzah összekötése](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>A Feather HUZZAH ESP8266 összekötése a számítógéphez

A következő ábrán látható módon a Micro USB-vel írja be az USB-kábelt a Feather HUZZAH ESP8266 csatlakoztatásához a számítógéphez.

![A Feather Huzzah összekötése a számítógéphez](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Soros port engedélyeinek hozzáadása (csak Ubuntu esetén)

Ha Ubuntut használ, győződjön meg arról, hogy rendelkezik a tollas HUZZAH ESP8266 USB-portján való működéséhez szükséges engedélyekkel. A soros port engedélyeinek hozzáadásához kövesse az alábbi lépéseket:

1. Futtassa a következő parancsokat egy terminálon:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   A következő kimenetek valamelyikét kapja:

   * CRW-RW----1 root UUCP XXXXXXXX
   * CRW-RW----1 root kitárcsázáshoz XXXXXXXX

   A kimenetben figyelje meg, `uucp` hogy `dialout` a vagy az USB-port csoportjának tulajdonosi neve.

2. Adja hozzá a felhasználót a csoporthoz a következő parancs futtatásával:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`az előző lépésben beszerzett csoport tulajdonosának neve. `<username>`az Ubuntu-felhasználóneve.

3. Jelentkezzen ki az Ubuntu szolgáltatásból, majd jelentkezzen be újra a módosítás megjelenéséhez.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Érzékelő adatainak összegyűjtése és az IoT hub-ba való küldése

Ebben a szakaszban egy minta alkalmazást helyez üzembe és futtat a Feather HUZZAH ESP8266. A minta alkalmazás a tollas HUZZAH ESP8266, és a DHT22 érzékelőtől az IoT hubhoz gyűjtött hőmérséklet-és páratartalom-adatokat küldi el.

### <a name="get-the-sample-application-from-github"></a>Minta alkalmazás beszerzése a GitHubról

A minta alkalmazás a GitHubon fut. A minta alkalmazást tartalmazó minta tárház klónozása a GitHubról. A minta tárház klónozásához kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort vagy egy Terminálablak-ablakot.

2. Nyissa meg azt a mappát, ahol a minta alkalmazást tárolni szeretné.

3. Futtassa a következő parancsot:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Ezután telepítse a Feather HUZZAH ESP8266 csomagot a Visual Studio Code-ban.

4. Nyissa meg azt a mappát, ahol a minta alkalmazást tárolja.

5. Nyissa meg az app. Ino fájlt az App mappában a Visual Studio Code-ban.

   ![A minta alkalmazás megnyitása a Visual Studio Code-ban](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. A Visual Studio Code-ban adja `F1`meg a értéket.

7. Írja  be az Arduino **elemet, és válassza az Arduino: Üzenőfal-** kezelő.

8. Az **Arduino Board Manager** lapon kattintson a **további URL-címek**elemre.

   ![VS Code Arduino Board Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. A **felhasználói beállítások** ablakban másolja és illessze be a következőt a fájl végére.

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Arduino-csomag URL-címének konfigurálása a VS Code-ban](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Mentse a fájlt, és zárjuk be a **felhasználói beállítások** lapot.

11. Kattintson a **csomag indexek frissítése**elemre. A frissítés befejeződése után keresse meg a **esp8266**.

12. Kattintson a **telepítés** gombra a esp8266.

    A Boards Manager azt jelzi, hogy a ESP8266 a 2.2.0 vagy újabb verzióval van telepítve.

    ![A esp8266 csomag telepítve van](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Írja `F1`be, majd  írja be az **Arduino elemet, és válassza az Arduino: Tábla konfigurációja**

14. Kattintson a **kijelölt táblához** tartozó mezőre, és írja be a következőt: **esp8266**, majd válassza a **adafruit HUZZAH esp8266 (esp8266)** lehetőséget.

    ![Esp8266-tábla kiválasztása](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>A szükséges kódtárak telepítése

1. A Visual Studio Code-ban írja `F1`be a (z), **majd írja be az **Arduino** elemet, és válassza az Arduino: Library Manager**.

2. Keresse meg a következő könyvtárak nevét eggyel. Minden megtalált könyvtárhoz kattintson a **telepítés**gombra.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nem rendelkezik valós DHT22-érzékelővel?

Ha nem rendelkezik valós DHT22-érzékelővel, a minta alkalmazás képes szimulálni a hőmérséklet-és páratartalom-adattípust. A következő lépésekkel állíthatja be a minta alkalmazást a szimulált adatértékek használatára:

1. Nyissa meg a `app` fájltamappában.`config.h`

2. Keresse meg a következő kódrészletet, és módosítsa az értéket `false` `true`a következőre:

   ```c
   define SIMULATED_DATA true
   ```

   ![A minta alkalmazás konfigurálása szimulált adatértékek használatára](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Mentse a fájlt.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>A minta alkalmazás üzembe helyezése a Feather HUZZAH ESP8266

1. A Visual Studio Code-ban kattintson a  **\<soros port kiválasztása >** elemre az állapotsoron, majd kattintson a soros portra a Feather HUZZAH ESP8266.

2. Írja `F1`be, majd  írja be az **Arduino elemet, és válassza az Arduino: Töltse** fel a minta alkalmazást a Feather HUZZAH ESP8266 felépítésére és üzembe helyezésére.

### <a name="enter-your-credentials"></a>Adja meg a hitelesítő adatait

A feltöltés sikeres befejezése után kövesse az alábbi lépéseket a hitelesítő adatok megadásához:

1. Nyissa meg az Arduino IDE-t, kattintson az **eszközök** > **soros figyelő**elemre.

2. A soros figyelő ablakban figyelje meg a két legördülő listát a jobb alsó sarokban.

3. A bal oldali legördülő listához válassza a **Nincs vonal véget** .

4. A jobb oldali legördülő listához válassza a **115200 Baud** lehetőséget.

5. A soros figyelő ablak felső részén található beviteli mezőben adja meg a következő információkat, ha a rendszer kéri, hogy adja meg azokat, majd kattintson a **Küldés**gombra.

   * Wi-Fi SSID
   * Wi-Fi jelszó
   * Eszköz-csatlakoztatási karakterlánc

> [!Note]
> A hitelesítő adatokat a rendszer az EEPROM-ban tárolja a Feather HUZZAH ESP8266. Ha a Feather HUZZAH ESP8266-tábla alaphelyzetbe állítás gombjára kattint, a minta alkalmazás megkérdezi, hogy szeretné-e törölni az adatokat. Adja `Y` meg, hogy az adatok törlődnek-e. A rendszer Másodszor kéri az információ megadását.

### <a name="verify-the-sample-application-is-running-successfully"></a>A minta alkalmazás sikeres futtatásának ellenőrzése

Ha a soros figyelő ablakának következő kimenetét látja, és a villogó LED a Feather HUZZAH ESP8266, a minta alkalmazás sikeresen fut.

![Végső kimenet az Arduino IDE-ben](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>A hub által fogadott üzenetek olvasása

Az IoT hub által az eszközről fogadott üzenetek figyelésének egyik módja a Visual Studio Code-hoz készült Azure IoT Tools használata. További információ: az [Azure IoT Tools for Visual Studio Code használata üzenetek küldéséhez és fogadásához az eszköz és a IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Az eszköz által elküldhető adatfeldolgozás további módjairól folytassa a következő szakasszal.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta a tollas HUZZAH ESP8266 az IoT hubhoz, és elküldte a rögzített érzékelő adatait a IoT hubhoz.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]