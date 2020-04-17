---
title: Az IoT DevKit AZ3166 csatlakoztatása egy Azure IoT Hubhoz
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be és csatlakozhatja az IoT DevKit AZ3166-ot az Azure IoT Hubhoz, hogy adatokat küldhessen az Azure felhőplatformjára.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 0c7566df870b3c41b1f1dd7d4a4129ff9d9d9407
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481736"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Az IoT DevKit AZ3166 csatlakoztatása az Azure IoT Hubhoz

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Az [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) segítségével olyan Megoldásokat fejleszthet és prototípusálhat ki, amelyek kihasználják a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja az Arduino-kompatibilis fórumon gazdag perifériák és érzékelők, egy nyílt forráskódú tábla csomag, és egy gazdag [minta galéria](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Ismertetett témák

* IoT-központ létrehozása és az MXChip IoT DevKit eszközregisztrálása.
* Hogyan lehet csatlakoztatni az IoT DevKit Wi-Fi-hez, és konfigurálni az IoT Hub kapcsolati karakterlánc.
* A DevKit-érzékelő telemetriai adatainak küldése az IoT hubra.
* Hogyan készítse elő a fejlesztői környezetet, és dolgozzon ki alkalmazást az IoT DevKit.

Még nincs DevKitje? Próbálja ki a [DevKit-szimulátort,](https://azure-samples.github.io/iot-devkit-web-simulator/) vagy [vásároljon egy DevKit-et.](https://aka.ms/iot-devkit-purchase)

Megtalálható a forráskód az összes DevKit oktatóanyagok [kódminták galériában](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Mi szükséges

* MXChip IoT DevKit alaplap Micro-USB kábellel. [Szerezd meg most](https://aka.ms/iot-devkit-purchase).
* Windows 10, macOS 10.10+ vagy Ubuntu 18.04+ rendszert futtató számítógép.
* Aktív Azure-előfizetés. [Aktiválja az ingyenes 30 napos próbaverziós Microsoft Azure-fiókot.](https://azureinfo.microsoft.com/us-freetrial.html)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>A hardver előkészítése

Csatlakoztassa a következő hardvert a számítógéphez:

* DevKit fórumon
* Micro-USB kábel

![Szükséges hardver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

A DevKit számítógéphez való csatlakoztatásához kövesse az alábbi lépéseket:

1. Csatlakoztassa az USB-végpontot a számítógéphez.

2. Csatlakoztassa a Micro-USB-végét a DevKithez.

3. A zöld LED a tápellátáshoz megerősíti a kapcsolatot.

   ![Hardverkapcsolatok](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Rövid útmutató: Telemetriai adatok küldése a DevKit-től egy IoT Hub

A rövid útmutató előre lefordított DevKit belső vezérlőprogram ot használ a telemetriai adatok ioT Hubba küldéséhez. A futtatás előtt hozzon létre egy IoT hubot, és regisztráljon egy eszközt a hub.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névvel.

   **MyNodeDevice**: A regisztráló eszköz neve. Használja **a MyNodeDevice-t** az ábrán látható módon. Ha más nevet választ az eszközhöz, ezt a nevet kell használnia ebben a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Ha egy hiba `device-identity`futtatása kor, telepítse az [Azure IoT extension for Azure CLI.](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)
   > Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) az Azure CLI-hez tartozó parancsokat.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

### <a name="send-devkit-telemetry"></a>DevKit telemetria küldése

A DevKit csatlakozik egy eszköz-specifikus végpont az IoT hubon, és elküldi a hőmérséklet és a páratartalom telemetriai adatokat.

1. Töltse le a [GetStarted belső vezérlőprogram](https://aka.ms/devkit/prod/getstarted/latest) legújabb verzióját az IoT DevKit hez.

1. Győződjön meg arról, hogy az IoT DevKit USB-n keresztül csatlakozik a számítógéphez. Nyissa meg a Fájlkezelőt, van egy USB háttértároló eszköz, az **AZ3166**.

    ![A Windows Intéző megnyitása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Drag and drop a firmware csak letöltött a háttértároló eszköz, és automatikusan villog.

    ![Belső vezérlőprogram másolása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. A DevKiten tartsa lenyomva a **B**gombot, nyomja meg és engedje fel a **Visszaállítás** gombot, majd engedje fel a **B**gombot. A DevKit AP módba lép. A megerősítéshez a képernyőn megjelenik a DevKit szolgáltatáskészlet-azonosítója (SSID) és a konfigurációs portál IP-címe.

    ![Visszaállítás gomb, B gomb és SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP mód beállítása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Használjon webböngészőt egy másik Wi-Fi-kompatibilis eszközön (számítógépen vagy mobiltelefonon) az előző lépésben megjelenített IoT DevKit SSID-hez való csatlakozáshoz. Ha jelszót kér, hagyja üresen.

    ![SSID csatlakoztatása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Nyissa meg a **192.168.0.1-et** a böngészőben. Válassza ki azt a Wi-Fi-t, amelyhez csatlakozni szeretne az IoT DevKit-hez, írja be a Wi-Fi jelszót, majd illessze be a korábban feljegyzett eszközkapcsolati karakterláncot. Ezután kattintson a Save (Mentés) gombra.

    ![Konfigurációs felhasználói felület](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Az IoT DevKit csak 2,4 GHz-es hálózatot támogat. További részletekért tekintse meg a [GYIK-et.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)

1. A Wi-Fi-adatok és az eszköz kapcsolati karakterlánc a rendszer tárolja az IoT DevKit, amikor megjelenik az eredményoldalon.

    ![Konfiguráció eredménye](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > A Wi-Fi konfigurálása után a hitelesítő adatok megmaradnak az adott kapcsolathoz szükséges eszközön, még akkor is, ha az eszköz nincs csatlakoztatva.

1. Az IoT DevKit néhány másodperc en belül újraindul. A DevKit képernyőn láthatja a DevKit IP-címét, amelyet a telemetriai adatok, beleértve a hőmérséklet és a páratartalom értékét az Üzenetek száma küldése az Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Adatok küldése](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Az Azure-ba küldött telemetriai adatok ellenőrzéséhez futtassa a következő parancsot az Azure Cloud Shellben:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Kövesse az alábbi lépéseket a fejlesztői környezet devkit előkészítéséhez:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>A Visual Studio-kód telepítése az Azure IoT Tools bővítménycsomaggal

1. Telepítse [Arduino IDE](https://www.arduino.cc/en/Main/Software). Ez biztosítja a szükséges eszköztár összeállításához és feltöltéséhez Arduino kódot.
    * **Windows**: A Windows Installer verziójának használata. Ne telepítse az App Store-ból.
    * **macOS**: Húzza a kivont **Arduino.app-ot** a mappába. `/Applications`
    * **Ubuntu**: Csomagolja ki a mappába, mint például`$HOME/Downloads/arduino-1.8.8`

2. Telepítse [a Visual Studio Code,](https://code.visualstudio.com/)a platformfüggetlen forráskód szerkesztő erős értelem, kód befejezése és hibakeresés támogatása, valamint a gazdag kiterjesztések lehet telepíteni a piacon.

3. Indítsa el a VS Code-ot, keresse meg **az Arduino-t** a bővítmény piacon, és telepítse. Ez a kiterjesztés továbbfejlesztett élményt nyújt az Arduino platformon való fejlesztéshez.

    ![Arduino telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Keresse meg az [Azure IoT Tools](https://aka.ms/azure-iot-tools) a bővítmény piactéren, és telepítse azt.

    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Vagy használja ezt a közvetlen URL-t:`vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Az Azure IoT Tools bővítménycsomag tartalmazza az [Azure IoT Device Workbench,](https://aka.ms/iot-workbench) amely fejlesztésére és hibakeresésére használják a különböző IoT devkit-eszközök. Az [Azure IoT Hub-bővítmény](https://aka.ms/iot-toolkit), amely az Azure IoT Tools bővítménycsomag részét is tartalmazza, az Azure IoT Hubs kezelésére és kezelésére szolgál.

5. Konfigurálja a VS Code-ot az Arduino beállításokkal.

    A Visual Studio-kódban kattintson **a Fájl > beállítások > beállítások** (macOS rendszerben, a Kód > beállítások > **Beállítások**) elemre. Ezután kattintson a Beállítások *lap* jobb felső sarkában lévő **JSON-ikonra.**

    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adja hozzá a következő sorokat az Arduino konfigurálásához a platformtól függően: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS :**

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Cserélje le az alábbi **{felhasználónév}** helyőrzőt a felhasználónevére.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kattintson a `F1` parancspaletta megnyitásához, írja be és válassza **az Arduino: Board Manager lehetőséget.** Keresse meg az **AZ3166-ot,** és telepítse a legújabb verziót.

    ![Fejlesztői Fejlesztői Kit SDK telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link illesztőprogramok telepítése

[Az ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) az az USB-interfész, amelyet az IoT DevKit a fejlesztőgéppel való kommunikációhoz használ. Telepítenie kell a Windows-ra, hogy a lefordított eszköz kódját a DevKit villog. Kövesse az operációs rendszer-specifikus lépéseket, hogy a gép hozzáférhessen az eszközhöz.

* **Windows**: USB-illesztőprogram letöltése és telepítése az [STMicroelectronics webhelyéről.](https://www.st.com/en/development-tools/stsw-link009.html)
* **macOS**: A macOS rendszerhez nincs szükség illesztőprogramra.
* **Ubuntu**: Futtassa a parancsokat a terminálon, és jelentkezzen ki, és jelentkezzen be a csoportmódosítás érvénybe léptetéséhez:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Most már minden készen áll a fejlesztési környezet előkészítésével és konfigurálásával. Hadd készítsük el a GetStarted mintát, amit most futtatott.

## <a name="build-your-first-project"></a>Az első projekt megépítése

### <a name="open-sample-code-from-sample-gallery"></a>Mintakód megnyitása a mintagyűjteményből

Az IoT DevKit egy gazdag gyűjteményminták, amelyek segítségével megtudhatja, hogy csatlakoztassa a DevKit különböző Azure-szolgáltatások.

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógéphez. Először indítsa el a VS-kódot, majd csatlakoztassa a DevKit-et a számítógéphez.

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT Device Workbench: Open Examples...** lehetőséget. Ezután válassza **az IoT DevKit** fórumon.

1. Az IoT Workbench-példák lapon keresse meg az **Első lépések lapot,** és kattintson a **Minta megnyitása gombra.** Ezután kiválasztja a mintakód letöltéséhez vezető alapértelmezett elérési utat.

    ![Nyitott minta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub és eszköz kiépítése

Az Azure IoT Hub és az Azure Portalról való kiépítés helyett a VS-kódban is megteheti a fejlesztői környezet elhagyása nélkül.

1. Az új megnyitott projektablakban kattintson `F1` a parancspaletta megnyitásához, írja be és válassza az Azure **IoT Device Workbench: Provision Azure Services...** lehetőséget. Kövesse a lépésről lépésre útmutatót az Azure IoT Hub kiépítésének befejezéséhez és az IoT Hub-eszköz létrehozásához.

    ![Kiépítés parancs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Ha még nem írta alá az Azure-ban. Kövesse az előugró értesítés a bejelentkezéshez.

1. Válassza ki a használni kívánt előfizetést.

    ![Alkijelölés](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Ezután jelöljön ki vagy hozzon létre egy új [erőforráscsoportot.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)

    ![Erőforráscsoport kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. A megadott erőforráscsoportban kövesse az útmutatót egy új Azure IoT Hub kiválasztásához vagy létrehozásához.

    ![Válassza az IoT Hub lépéseit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT-központ kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Kijelölt IoT-központ](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. A kimeneti ablakban az Azure IoT Hub kiépített jelenik meg.

    ![IoT hub kiépítve](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Válasszon ki vagy hozzon létre egy új eszközt az Azure IoT Hub ban, amelyet kiépített.

    ![Válassza az IoT-eszköz lépéseit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![IoT-eszköz kiépített kiválasztása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Most már rendelkezik az Azure IoT Hub kiépített és az eszköz jön létre benne. Az eszközkapcsolati karakterlánc is mentésre kerül a VS-kódban az IoT DevKit későbbi konfigurálásához.

    ![Céltartalék](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Eszközkód konfigurálása és fordítása

1. A jobb alsó állapotsorban ellenőrizze, hogy az **MXCHIP AZ3166** a kiválasztott táblaként jelenik-e meg, és az **STMicroelectronics** soros portját használja.

    ![Válasszon fórumot és COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT-eszköz munkapadja: Eszközbeállítások konfigurálása...** lehetőséget, majd válassza a **Config Device Connection String > Select IoT Hub Device Connection String**lehetőséget.

1. A DevKit en tartsa lenyomva **az A gombot,** nyomja meg és engedje fel a **reset** gombot, majd engedje fel az **A gombot.** A DevKit konfigurációs módba lép, és menti a kapcsolati karakterláncot.

    ![Kapcsolati sztring](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kattintson ismét, írja be, és válassza **az Azure IoT-eszköz munkapad: Eszközkód feltöltése**. `F1` Elindítja a fordítást, és feltölti a kódot a DevKit-be.

    ![Arduino feltöltés](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

A DevKit újraindul, és elindítja a kód futtatását.

> [!NOTE]
> Ha bármilyen hiba vagy megszakítás van, a parancs ismételt futtatásával bármikor helyreállíthatja a hibát.

## <a name="test-the-project"></a>A projekt tesztelése

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Az Azure IoT Hubnak küldött telemetriai adatok megtekintése

A soros monitor megnyitásához kattintson az állapotsor hálózati csatlakozójának ikonjára:

![Soros monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A mintaalkalmazás sikeresen fut, ha a következő eredmények jelennek meg:

* A Soros-figyelő megjeleníti az IoT Hubnak küldött üzenetet.
* Az MXChip IoT DevKit LED-je villog.

![Soros monitor kimenete](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Előfordulhat, hogy a tesztelés során olyan hiba lép fel, amelyben a LED nem villog, az Azure Portal nem jeleníti meg az eszközről érkező adatokat, de az eszköz OLED-képernyője **futó...**. A probléma megoldásához az Azure Portalon nyissa meg az eszközt az IoT hubban, és küldjön egy üzenetet az eszközre. Ha a vs-kód soros figyelőjében a következő választ látja, lehetséges, hogy az eszközről érkező közvetlen kommunikáció blokkolva van az útválasztó szintjén. Ellenőrizze a csatlakozó eszközökhöz konfigurált tűzfal- és útválasztó-szabályokat. Győződjön meg arról is, hogy az 1833-as kimenő port nyitva van.
> 
> HIBA: mqtt_client.c (ln 454): Hiba: hiba a végponthoz való csatlakozás megnyitásakor  
> INFORMÁCIÓ: >>>Kapcsolat állapota: leválasztva  
> HIBA: tlsio_mbedtls.c (ln 604): Az alapul szolgáló IO-nyílt nem sikerült  
> HIBA: mqtt_client.c (ln 1042): Hiba: io_open nem sikerült  
> HIBA: iothubtransport_mqtt_common.c (ln 2283): hiba a atcsliothub.azure-devices.net címhez való kapcsolódás.  
> INFO: >>>Újracsatlakozás.  
> INFORMÁCIÓ: IoThub verzió: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Az Azure IoT Hub által fogadott telemetriai adatok megtekintése

[Az Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) segítségével figyelheti az eszközök közötti (D2C) üzeneteket az IoT Hubban.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/)keresse meg a létrehozott IoT Hubot.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. A **Megosztott hozzáférési házirendek** ablaktáblán kattintson az **iothubowner házirendre**, és írja le az IoT hub kapcsolati karakterláncát.

    ![Azure IoT Hub kapcsolati karakterlánc](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. A VS-kód `F1`ban kattintson a gombra, és válassza az **Azure IoT Hub: Set IoT Hub connection String**lehetőséget. Másolja a kapcsolati karakterláncot.

    ![Az Azure IoT Hub kapcsolati karakterláncának beállítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Bontsa ki a jobb oldalon található **AZURE IOT HUB DEVICES** ablaktáblát, kattintson a jobb gombbal a létrehozott eszköznevére, és válassza a Beépített **eseményvégpont indítása parancsot.**

    ![D2C-üzenet figyelése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. A **OUTPUT** ablaktáblán láthatja a bejövő D2C-üzeneteket az IoT Hub.

    ![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>A kód áttekintése

A `GetStarted.ino` a fő Arduino vázlat fájlt.

![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Ha meg szeretné tekinteni, hogyan eszköz telemetriai `utility.cpp` adatokat küldött az Azure IoT Hub, nyissa meg a fájlt ugyanabban a mappában. Tekintse meg [az API-referencia segítségével](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) megtudhatja, hogyan használhatja az érzékelőket és perifériákat az IoT DevKit-en.

A `DevKitMQTTClient` használt a microsoft [azure IoT SDK-k és a C-kódtárak](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) **iothub_client** burkolója az Azure IoT Hubbal való interakcióhoz.

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákba ütközik, az [IoT DevKit GYIK-ben](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) megkeresheti a megoldást, vagy kapcsolatba léphet velünk a [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)ből. Visszajelzést is adhat nekünk, ha megjegyzést fűz hozzánk ezen az oldalon.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta az MXChip IoT DevKit-et az IoT hubhoz, és elküldte a rögzített érzékelőadatokat az IoT hubra.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
