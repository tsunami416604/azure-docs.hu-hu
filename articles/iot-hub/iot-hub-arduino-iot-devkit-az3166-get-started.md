---
title: IoT fejlesztői készlet a felhőbe – a IoT fejlesztői készlet-AZ3166 kapcsolódása az Azure IoT Hubhoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és csatlakozhat a IoT fejlesztői készlet AZ3166 az Azure IoT Hubhoz, így az képes az Azure Cloud platformra küldeni az adatküldést.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: e8a186fbcb04dc29fcf57a2353adcf89ce46b119
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677935"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>A IoT fejlesztői készlet AZ3166 összekapcsolásával az Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

A [MXChip IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/) olyan eszközök internetes hálózata-(IoT-) megoldások fejlesztéséhez és prototípusához használható, amelyek kihasználják Microsoft Azure-szolgáltatásokat. Tartalmaz egy Arduino-kompatibilis táblát, amely gazdag perifériákkal és érzékelőkkel, egy nyílt forráskódú táblás csomaggal és egy gazdag [minta](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)-katalógussal rendelkezik.

## <a name="what-you-learn"></a>Ismertetett témák

* IoT hub létrehozása és az eszköz regisztrálása a MXChip IoT fejlesztői készlet.
* A IoT-fejlesztői készlet csatlakoztatása Wi-Fi-hez és a IoT Hub kapcsolati karakterlánc konfigurálása.
* A fejlesztői készlet-érzékelő telemetria-adatainak elküldése az IoT hubhoz.
* A fejlesztési környezet előkészítése és alkalmazás fejlesztése a IoT-fejlesztői készlet.

Még nincs fejlesztői készlet? Próbálja ki a [fejlesztői készlet szimulátort](https://azure-samples.github.io/iot-devkit-web-simulator/) , vagy [vásároljon egy fejlesztői készlet](https://aka.ms/iot-devkit-purchase).

A [Code Samples](https://docs.microsoft.com/samples/browse/?term=mxchip)katalógusból megtalálhatja az összes fejlesztői készlet-oktatóanyag forráskódját.

## <a name="what-you-need"></a>Mi szükséges

* Egy MXChip IoT fejlesztői készlet-tábla Micro-USB-kábellel. [Töltse le most](https://aka.ms/iot-devkit-purchase).
* Windows 10, macOS 10.10 + vagy Ubuntu 18.04 + rendszert futtató számítógép.
* Aktív Azure-előfizetés. [Aktiváljon egy 30 napos ingyenes próbaverziós Microsoft Azure fiókot](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>A hardver előkészítése

Csatlakoztassa a következő hardvert a számítógéphez:

* Fejlesztői készlet-tábla
* Micro-USB-kábel

![Szükséges hardver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

A fejlesztői készlet a számítógéphez való kapcsolódáshoz kövesse az alábbi lépéseket:

1. Csatlakoztassa az USB-végpontot a számítógéphez.

2. Kapcsolja össze a Micro-USB-végpontot a fejlesztői készlet.

3. Az energia zöld LED-je megerősíti a kapcsolódást.

   ![Hardveres kapcsolatok](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Gyors útmutató: Telemetria küldése a fejlesztői készlet-ből egy IoT Hub

A rövid útmutató az előre lefordított fejlesztői készlet belső vezérlőprogram használatával küldi el a telemetria a IoT Hub. A futtatása előtt létre kell hoznia egy IoT hubot, és regisztrálnia kell egy eszközt a hubhoz.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyNodeDevice**: Annak az eszköznek a neve, amelyhez regisztrálva van. Használja a **MyNodeDevice** az ábrán látható módon. Ha más nevet választ az eszközének, ezt a nevet kell használnia ebben a cikkben, és frissítenie kell az eszköz nevét a minta alkalmazásokban a futtatása előtt.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Ha a rendszer `device-identity`hibaüzenetet kap, a további részletekért telepítse az Azure [IOT bővítményt az Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) -hez.
  
1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

### <a name="send-devkit-telemetry"></a>Fejlesztői készlet telemetria küldése

A fejlesztői készlet az IoT hub eszköz-specifikus végpontján csatlakozik, és hőmérséklet-és páratartalom-telemetria küld.

1. Töltse le a [GetStarted belső vezérlőprogram](https://aka.ms/devkit/prod/getstarted/latest) legújabb verzióját a IoT fejlesztői készlet.

1. Győződjön meg arról, hogy a IoT fejlesztői készlet csatlakozik a számítógéphez USB-kapcsolaton keresztül. Nyissa meg a Fájlkezelőben a **AZ3166**nevű USB háttértár eszközt.

    ![A Windows Intéző megnyitása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Húzza a belső vezérlőprogram letöltését a háttértár-eszközre, és automatikusan villogni fog.

    ![Belső vezérlőprogram másolása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. A fejlesztői készlet tartsa lenyomva a **b**gombot, nyomja le és szabadítsa fel az **Alaphelyzetbe állítás** gombot, majd a **b**gombot. A fejlesztői készlet AP-módba lép. A megerősítéshez a képernyő a fejlesztői készlet és a konfigurációs portál IP-címének szolgáltatáskészlet-azonosítóját (SSID-t) jeleníti meg.

    ![Alaphelyzetbe állítás gomb, B gomb és SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP mód beállítása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Az előző lépésben megjelenő IoT fejlesztői készlet SSID-hez való csatlakozáshoz használjon webböngészőt egy másik Wi-Fi-kompatibilis eszközön (számítógép vagy mobiltelefon). Ha jelszót kér, hagyja üresen.

    ![Összekötő SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Nyissa meg a **192.168.0.1** a böngészőben. Válassza ki azt a Wi-Fi-t, amelyhez csatlakozni kíván a IoT fejlesztői készlet, írja be a Wi-Fi jelszót, majd illessze be a korábban létrehozott eszköz kapcsolati karakterláncát. Ezután kattintson a Save (Mentés) gombra.

    ![Konfigurációs felhasználói felület](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > A IoT fejlesztői készlet csak 2,4 GHz-es hálózatot támogat. További részletekért lásd a [gyakori kérdéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) .

1. A Wi-Fi-és az eszköz-kapcsolódási karakterláncot a rendszer a IoT fejlesztői készlet tárolja, amikor megjelenik az eredmény oldal.

    ![Konfiguráció eredménye](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > A Wi-Fi konfigurálása után a hitelesítő adatai megmaradnak az eszközön a kapcsolathoz, még akkor is, ha az eszköz le van húzva.

1. A IoT fejlesztői készlet néhány másodperc múlva újraindul. A fejlesztői készlet képernyőn láthatja, hogy a fejlesztői készlet IP-címe a telemetria-információ, beleértve a hőmérséklet és a páratartalom értékét, valamint az üzenetek száma az Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Adatok küldése](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Az Azure-ba irányuló telemetria-adatgyűjtési ellenőrzéshez futtassa a következő parancsot Azure Cloud Shellban:

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A következő lépésekkel készítheti elő a fejlesztési környezetet a fejlesztői készlet:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>A Visual Studio Code telepítése az Azure IoT Tools bővítmény-csomaggal

1. Telepítse az [ARDUINO ide](https://www.arduino.cc/en/Main/Software)-t. Ez biztosítja a szükséges toolchain az Arduino-kódok fordításához és feltöltéséhez.
    * **Windows**: Windows Installer verzió használata. Ne telepítse az App Store-ból.
    * **MacOS**: Húzza a kibontott **Arduino. app** `/Applications` mappát a mappába.
    * **Ubuntu**: Bontsa ki a mappát, például`$HOME/Downloads/arduino-1.8.8`

2. Telepítse a [Visual Studio Code](https://code.visualstudio.com/)-ot, amely egy platformfüggetlen forráskód-szerkesztő, amely hatékony IntelliSense-, kód-befejezési és hibakeresési támogatást, valamint sokoldalú bővítményeket is telepíthet a Piactérről.

3. Indítsa el a VS Code-ot, keresse meg az **Arduino** -t a bővítmény piactéren, és telepítse. Ez a bővítmény továbbfejlesztett funkciókat biztosít az Arduino platformon való fejlesztéshez.

    ![Az Arduino telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Keresse meg az [Azure IoT-eszközöket](https://aka.ms/azure-iot-tools) a bővítmény piactéren, és telepítse azt.

    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Vagy használja ezt a közvetlen hivatkozást:
    > [!div class="nextstepaction"]
    > [Az Azure IoT Tools Extension Pack telepítése](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Az Azure IoT Tools Extension Pack tartalmazza az [Azure IoT Device workbencht](https://aka.ms/iot-workbench) , amely különböző IoT-fejlesztői készlet eszközök fejlesztésére és hibakeresésére szolgál. Az Azure [IoT hub Toolkit](https://aka.ms/iot-toolkit), amelyet az Azure IoT Tools Extension Pack is tartalmaz, az Azure IoT hubok felügyeletére és kezelésére szolgál.

5. Állítsa be a VS Code-ot az Arduino-beállításokkal.

    A Visual Studio Code-ban kattintson a **fájl > beállítások > beállítások** (MacOS, **kód > beállítások > Beállítások**) elemre. Ezután kattintson az **Open settings (JSON)** ikonra a *Beállítások* lap jobb felső sarkában.

    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adja hozzá a következő sorokat az Arduino konfigurálásához a platformtól függően: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **MacOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Cserélje le az alábbi **{username}** helyőrzőt a felhasználónevére.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kattintson `F1` a parancs paletta megnyitásához, írja be a **parancsot, és válassza az Arduino: Üzenőfal-** kezelő. Keresse meg a **AZ3166** , és telepítse a legújabb verziót.

    ![A fejlesztői készlet SDK telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>A ST-link illesztőprogramjainak telepítése

A [St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) az USB-interfész, amelyet a IoT fejlesztői készlet használ a fejlesztői géppel folytatott kommunikációhoz. Telepítenie kell a Windows rendszerre, hogy a lefordított eszköz kódját a fejlesztői készlet. Kövesse az operációs rendszerre vonatkozó lépéseket, amelyekkel engedélyezheti a számítógép hozzáférését az eszközhöz.

* **Windows**: Töltse le és telepítse az USB-illesztőprogramot a [STMicroelectronics webhelyről](https://www.st.com/en/development-tools/stsw-link009.html).
* **MacOS**: Nem szükséges illesztőprogram a macOS-hez.
* **Ubuntu**: Futtassa a parancsokat a terminálon, és jelentkezzen ki, majd jelentkezzen be a csoport módosításának érvénybe léptetéséhez:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Most már minden készen áll a fejlesztési környezet előkészítésére és konfigurálására. Hozzuk létre az imént futtatott GetStarted-mintát.

## <a name="build-your-first-project"></a>Az első projekt létrehozása

### <a name="open-sample-code-from-sample-gallery"></a>Mintakód megnyitása a minta-gyűjteményből

A IoT fejlesztői készlet olyan gyűjteményeket tartalmaz, amelyek segítségével a fejlesztői készlet különböző Azure-szolgáltatásokhoz csatlakoztathatók.

1. Győződjön meg arról, hogy a IoT fejlesztői készlet nincs **csatlakoztatva** a számítógéphez. Először indítsa el a VS Code-ot, majd kapcsolja össze a fejlesztői készlet a számítógéppel.

1. Kattintson `F1` a parancs paletta megnyitásához, írja be a **parancsot, majd válassza az Azure IoT Device Workbench: Példák megnyitása...** . Ezután válassza a **IoT fejlesztői készlet** lehetőséget.

1. A IoT Workbench-példák oldalon keresse meg az első **lépéseket** , és kattintson a **minta megnyitása**lehetőségre. Ezután kiválasztja a mintakód letöltésének alapértelmezett elérési útját.

    ![Minta megnyitása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure-IoT Hub és-eszköz kiépítése

Az Azure IoT Hub és az eszköznek a Azure Portalból való kiépítése helyett a VS Code-ban is elvégezheti a fejlesztési környezet elhagyása nélkül.

1. Az új megnyitott projekt ablakban kattintson `F1` a parancs palettájának megnyitásához, írja be a parancsot, majd válassza **az Azure IoT Device Workbench: Azure-szolgáltatások kiépítése...** . Az Azure-IoT Hub üzembe helyezésének befejezéséhez és a IoT Hub eszköz létrehozásához kövesse az útmutató lépésről lépésre útmutatót.

    ![Parancs kiépítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Ha még nem jelentkezett be az Azure-ba. A bejelentkezéshez kövesse az előugró értesítéseket.

1. Válassza ki a használni kívánt előfizetést.

    ![Altípus kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Ezután válasszon ki vagy hozzon létre egy új [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Erőforráscsoport kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. A megadott erőforráscsoport esetében kövesse az útmutatót az új Azure-IoT Hub kiválasztásához vagy létrehozásához.

    ![IoT Hub lépések kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Selected IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. A kimeneti ablakban megjelenik az Azure IoT Hub kiépítve.

    ![IoT Hub kiépítve](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Válasszon ki vagy hozzon létre egy új eszközt az Azure-IoT Hub kiépítve.

    ![IoT eszköz lépéseinek kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![IoT-eszköz kiépített kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Most már rendelkezik az Azure IoT Hub üzembe helyezésével és az eszköz létrehozásával. Az IoT-fejlesztői készlet később is konfigurálhatja a VS Code-ban az eszköz-csatlakoztatási karakterláncot.

    ![Kiépítés kész](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Az eszköz kódjának konfigurálása és fordítása

1. A jobb alsó állapotsorban tekintse meg a **MXCHIP AZ3166** , amely a **STMicroelectronics** használatával kiválasztott táblaként és soros porton jelenik meg.

    ![Válassza ki a táblát és a COM-t](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kattintson `F1` a parancs paletta megnyitásához, írja be a **parancsot, majd válassza az Azure IoT Device Workbench: Eszközbeállítások konfigurálása... , majd válassza a **konfigurációs eszköz kapcsolatainak karakterlánca lehetőséget > Válassza ki IoT hub eszköz csatlakoztatási karakterláncát.****

1. A fejlesztői készlet-on tartsa lenyomva az **a gombot**, leküldheti és felszabadíthatja az **Alaphelyzetbe állítás** gombot, majd **a "a" gombot**. A fejlesztői készlet konfigurációs módba lép, és menti a kapcsolatok karakterláncát.

    ![Kapcsolati sztring](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kattintson `F1` ismét az Azure IoT Device **Workbench elemre, és válassza ki a következőt: Töltse fel az**eszköz kódját. Elindul a kód fordítása és feltöltése a fejlesztői készlet.

    ![Arduino-feltöltés](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatását.

> [!NOTE]
> Ha hibák vagy megszakítások történnek, akkor a parancs ismételt futtatásával bármikor helyreállítható.

## <a name="test-the-project"></a>A projekt tesztelése

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Az Azure-ba eljuttatott telemetria megtekintése IoT Hub

A soros figyelő megnyitásához kattintson az állapotsorban található Power dugó ikonra:

![Soros figyelő](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A minta alkalmazás sikeresen fut, amikor a következő eredmények jelennek meg:

* A soros figyelő megjeleníti a IoT Hub küldött üzenetet.
* A MXChip IoT fejlesztői készlet jelzőfénye villog.

![Soros figyelő kimenete](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Az Azure IoT Hub által fogadott telemetria megtekintése

Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) segítségével figyelheti az eszközről a felhőbe irányuló (D2C) üzeneteket a IoT hub.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/), keresse meg a létrehozott IoT hub.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. A **megosztott elérési házirendek** ablaktáblán kattintson a **iothubowner házirendre**, és jegyezze fel az IoT hub kapcsolati karakterláncát.

    ![Azure IoT Hub-beli kapcsolatok karakterlánca](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. A vs Code-ban `F1`kattintson a elemre **, írja be és válassza ki az Azure IoT hub: IoT Hub a kapcsolatok karakterláncának**beállítása. Másolja a kapcsolatok karakterláncát.

    ![Azure IoT Hub-beli kapcsolatok karakterláncának beállítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Bontsa ki az **Azure IOT hub-eszközök** panelt a jobb oldalon, kattintson a jobb gombbal a létrehozott eszköz nevére, és válassza a **figyelés a beépített esemény végpontja**lehetőséget.

    ![D2C-üzenet figyelése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. A **kimeneti** ablaktáblán láthatja a bejövő D2C üzeneteket a IoT hub.

    ![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>A kód áttekintése

A `GetStarted.ino` a fő Arduino-vázlatos fájl.

![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Ha szeretné megtudni, hogyan küldik el az eszköz telemetria az Azure IoT hubba `utility.cpp` , nyissa meg a fájlt ugyanabban a mappában. Tekintse meg az [API-referenciát](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) , amelyből megtudhatja, hogyan használhatók érzékelők és perifériák a IoT fejlesztői készlet.

Az `DevKitMQTTClient` a **iothub_client** egy burkolója, amely az [Microsoft Azure IoT SDK-k és a C könyvtárai számára](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) készült, hogy az Azure IoT hub használatával működjön együtt.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, megkeresheti a megoldást a [IoT fejlesztői készlet GYIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) -ban, vagy elérheti a [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Visszajelzését az oldalon található Megjegyzés elküldésével is megadhatja.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta a MXChip IoT fejlesztői készlet a IoT hubhoz, és elküldte a rögzített érzékelő adatait a IoT hubhoz.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
