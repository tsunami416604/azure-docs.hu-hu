---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz, így adatokat küldhet az Azure felhőalapú platformján.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 4017a3be5e03e1a9b85b4002b8069a1adc3a6b83
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551566"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Csatlakozás az Azure IoT Hub IoT DevKit AZ3166

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Használhatja a [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) fejlesztéséhez és prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja az Arduino-kompatibilis tábla gazdag perifériák és érzékelők, egy nyílt forráskódú üzenőfal-csomagot és gazdag [mintakatalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Ismertetett témák

* Hozzon létre egy IoT hubot és a egy eszközt regisztrálhat az MXChip IoT DevKit módja.
* Útmutató az IoT DevKit csatlakoztatása a Wi-Fi-re és az IoT Hub kapcsolati karakterlánc konfigurálása.
* A DevKit érzékelő telemetriai adatok küldését az IoT hub hogyan.
* Útmutató a fejlesztési környezet előkészítését, és az IoT DevKit az alkalmazás fejlesztése.

Még nem rendelkezik egy fejlesztői készlet? Próbálja ki a [DevKit szimulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) vagy [vásárolhat egy DevKit](https://aka.ms/iot-devkit-purchase).

Annak a forráskód minden DevKit kapcsolatos, a [IoTDevEnvExamples](https://github.com/IoTDevEnvExamples) tárház.

## <a name="what-you-need"></a>Mi szükséges

* Az MXChip IoT DevKit tábla Micro – USB-kábellel. [Letöltés most](https://aka.ms/iot-devkit-purchase).
* A Windows 10-es, macOS 10.10 + vagy Ubuntu 18.04 + futtató számítógép.
* Aktív Azure-előfizetés. [Ingyenes 30 napos próba Microsoft Azure-fiók aktiválása](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Készítse elő a hardvert

A számítógépen a következő hardver kapcsolni:

* Fejlesztői készlet tábla
* Micro – USB-kábelen keresztül

![Szükséges hardver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

A fejlesztői készlet a számítógéphez való kapcsolódáshoz kövesse az alábbi lépéseket:

1. USB vége kapcsolódni a számítógéphez.

2. Csatlakozzon a fejlesztői készlet Micro – USB vége.

3. A zöld LED-jét power megerősíti, hogy a kapcsolat.

   ![Hardver-kapcsolatok](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Gyors útmutató: A fejlesztői készlet telemetriát küldjön az IoT Hub

A rövid útmutatóban előre lefordított DevKit belső vezérlőprogram segítségével a telemetriát küldjön az IoT Hub. Előtt futtatja, akkor hozzon létre egy IoT hubot, és eszköz regisztrálása az agyhoz.

### <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben, hozza létre az eszközidentitást.

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **MyNodeDevice**: Ön regisztrálja az eszköz neve. Használat **MyNodeDevice** látható módon. Ha úgy dönt, hogy az eszköz egy másik nevet, meg kell során ez a cikk ezt a nevet használja, és az eszköz neve a mintaalkalmazások őket futtatása előtt.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > Ha a futó hibaüzenetet kap `device-identity`, telepítse a [Azure CLI-hez az Azure IOT-bővítmény](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) további részletekért.
  
1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

### <a name="send-devkit-telemetry"></a>Fejlesztői készlet telemetriai adatok küldése

A fejlesztői készlet az IoT hub eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat küld.

1. Töltse le a legújabb verzióját [GetStarted belső vezérlőprogram](https://aka.ms/devkit/prod/getstarted/latest) IoT DevKit számára.

1. Győződjön meg arról, hogy IoT DevKit csatlakoztatása a számítógéphez USB-Kapcsolaton keresztül. Nyissa meg a Fájlkezelőt van egy USB-háttértár nevű eszközt **AZ3166**.
    ![Nyissa meg a Windows Intézőben](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Fogd és vidd a háttértár-eszközt, és az imént letöltött a belső vezérlőprogram flash automatikusan lesz.
    ![Belső vezérlőprogram másolása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. A fejlesztői készlet a gombot lenyomva **B**, push és kiadása a **alaphelyzetbe** gomb, valamint a kiadásban gomb **B**. A fejlesztői készlet AP módra vált. Győződjön meg arról, hogy a képernyő szolgáltatáskészlet-azonosító (SSID Azonosítóját), a fejlesztői készlet és a konfiguráció portál IP-címét jeleníti meg.
    ![Alaphelyzetbe állítása gomb, a B gomb, valamint az SSID-t](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Hozzáférési mód beállítása](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Használata egy másik Wi-Fi egy webböngészőt engedélyezve (számítógép vagy mobiltelefon) eszköz csatlakozni az IoT DevKit SSID-t az előző lépést jelenik meg. Ha azt kéri a jelszót, hagyja üresen.
    ![Connect SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Nyissa meg **192.168.0.1** a böngészőben. Válassza ki a Wi-Fi azt szeretné, adja meg a Wi-Fi-jelszót, majd illessze be az eszköz kapcsolati karakterláncának, vegye figyelembe a korábban végrehajtott csatlakozni az IoT DevKit. Ezután kattintson a Save (Mentés) gombra.
    ![Konfigurációs felhasználói Felületet](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Az IoT fejlesztői készlet csak a 2,4 GHz-es hálózati támogatja. Ellenőrizze [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) további részletekért.

1. Ha az eredmény oldal jelenik meg a Wi-Fi információkat és az eszköz kapcsolati karakterlánc lesznek tárolva, az IoT DevKit.
    ![Konfiguráció eredménye](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi beállítások konfigurálása után a hitelesítő adatait az eszköz meg a kapcsolathoz tartozó megmaradnak, még akkor is, ha az eszköz nem csatlakozik.

1. Az IoT DevKit pár másodpercen belül újraindul. A fejlesztői készlet képernyőn láthatja a a fejlesztői készlet IP-címét a következő által a telemetriai adatokat, beleértve a hőmérsékletet és páratartalommal érték üzenetek száma és az Azure IoT hubra küldése.
    ![Wi-Fi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Adatok küldése](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Annak ellenőrzéséhez, az Azure-bA küldött telemetriai adatok, az Azure Cloud Shellben futtassa a következő parancsot:
    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Kövesse az alábbi lépéseket a fejlesztési környezet előkészítése a fejlesztői készlet:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Az Azure IoT-eszközök kiterjesztési csomagot a Visual Studio Code telepítése

1. Telepítés [Arduino IDE](https://www.arduino.cc/en/Main/Software). A szükséges eszközlánc biztosít a kódja lefordításának és Arduino kód feltöltése.
    * **Windows**: Windows Installer verzióját használja. Ne telepítse az App Store a.
    * **macOS**: Oszlopmezők áthúzása a kinyert **Arduino.app** be `/Applications` mappát.
    * **Ubuntu**: Bontsa ki azt a mappába például `$HOME/Downloads/arduino-1.8.8`

2. Telepítés [Visual Studio Code](https://code.visualstudio.com/), hatékony az intellisense, a többplatformos nyílt forráskódú Kódszerkesztő code befejezési és a hibakeresés támogatása, valamint a sokoldalú bővítmények telepíthető a piactérről.

3. Indítsa el a VS Code, keresse meg a **Arduino** a bővítmény Marketplace-en, és telepítse. Ez a bővítmény Arduino platform fejlesztéséhez fokozott élményt biztosít.
    ![Arduino telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Keressen [Azure IoT-eszközök](https://aka.ms/azure-iot-tools) a bővítmény Marketplace-en, és telepítse.
    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Vagy a közvetlen hivatkozás:
    > [!div class="nextstepaction"]
    > [Az Azure IoT-eszközök bővítményt csomag telepítése](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Az Azure IoT-eszközök bővítményt csomag tartalmazza a [Azure IoT-eszköz Workbench](https://aka.ms/iot-workbench) amely fejlesztésekor és hibakeresésekor különféle IoT devkit eszközökön szolgál. A [Azure IoT Hub-eszközkészlet](https://aka.ms/iot-toolkit), az Azure IoT-eszközök bővítményt csomaggal is tartalmazza, kezelhesse és használhassa az Azure IoT hub segítségével.

5. A VS Code konfigurálása Arduino beállításokkal.

    A Visual Studio Code-ban kattintson **fájl > Beállítások > Beállítások**. Kattintson a **...**  és **nyissa meg a settings.json**.
    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Adja hozzá az alábbi sorokat a platformtól függően Arduino konfigurálása: 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Cserélje le a **{username}** alább helyőrzőt a felhasználónevét.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Arduino: Üzenőfal Manager**. Keresse meg **AZ3166** és telepítse a legújabb verziót.
    ![Fejlesztői készlet SDK telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link-illesztőprogramok telepítése

[ST-hivatkozás/V2](https://www.st.com/en/development-tools/st-link-v2.html) az USB-felület, amely IoT DevKit segítségével kommunikál a fejlesztői gépen. Telepítse a lefordított eszköz kódot a DevKit Flash Windows kell. Operációsrendszer-specifikus kövesse az eszköz a gép hozzáférésének engedélyezéséhez.

* **Windows**: Töltse le és telepítse az USB-illesztőprogramot az [STMicroelectronics webhely](https://www.st.com/en/development-tools/stsw-link009.html) vagy [közvetlen hivatkozást](https://aka.ms/stlink-v2-windows).
* **macOS**: Nincs illesztőprogram nem szükséges a macOS-hez.
* **Ubuntu**: Futtassa a parancsokat a terminálon, és jelentkezzen ki, és jelentkezzen be a csoport a módosítás érvénybe:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Most már minden készen állunk előkészítése és a fejlesztési környezet konfigurálása. Ossza meg velünk hozhat létre az imént futtatott GetStarted minta.

## <a name="build-your-first-project"></a>Az első-projekt létrehozása

### <a name="open-sample-code-from-sample-gallery"></a>Nyissa meg a mintakód a mintakatalógus

Az IoT DevKit tartalmaz egy széles választéka a mintákat, amelyek segítségével megismerheti a DevKit csatlakozik a különböző Azure-szolgáltatások.

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Példák megnyitása...** . Válassza ki **IoT DevKit** , tábla.

1. Keresse meg az IoT Workbench példáit tartalmazó oldalon, **Ismerkedés** kattintson **nyílt minta**. Ezután kiválasztja a mintakód letöltése az alapértelmezett elérési utat.
    ![Nyissa meg a minta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Kiépítése Azure IoT Hub és az eszköz

Üzembe helyezési Azure IoT Hub és az eszköz az Azure Portalról, helyett ezt megteheti a VS Code a fejlesztési környezet elhagyása nélkül.

1. Kattintson az új ablakban megnyitott projekt `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Azure-szolgáltatások üzembe helyezése...** . Kövesse a részletes útmutató az Azure IoT Hub provisioning és az IoT Hub-eszköz létrehozása gombra.
    ![A parancs üzembe helyezése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Ha nem regisztrált az Azure-ban. A felugró értesítés bejelentkezés esetén kövesse.

1. Válassza ki a használni kívánt előfizetést.
    ![Select sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Válassza ki vagy hozzon létre egy új [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Válasszon erőforráscsoportot](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. A megadott erőforráscsoportban kövesse az útmutató válasszon vagy hozzon létre egy új Azure IoT Hub.
    ![Válassza ki az IoT Hub-lépések](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Select IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Selected IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. A kimeneti ablakban megjelenik az Azure IoT Hubban kiosztott ![IoT Hub kiépítve](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Válassza ki, vagy hozzon létre egy új eszközt az Ön által üzembe helyezett Azure IoT Hub.
    ![Válassza ki az IoT Device lépések](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Válassza ki az üzembe helyezett IoT-eszköz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Most már üzembe helyezett Azure IoT Hub és a benne létrehozott eszköz. Emellett az eszköz kapcsolati karakterláncának menti a rendszer a VS Code-ban az IoT DevKit későbbi konfigurálásához.
    ![Kész kiépítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurálja és a kód fordítása

1. A jobb alsó állapotsor, ellenőrizze a **MXCHIP AZ3166** jelenik meg, mint a kiválasztott táblához és a soros port **STMicroelectronics** szolgál.
    ![Válassza ki a tábla- és COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Beállítások konfigurálása...** , majd **konfigurációs eszköz kapcsolati karakterláncát > Válasszon IoT Hub eszköz kapcsolati karakterláncának**.

1. A fejlesztői készlet, tartsa lenyomva a **gombra A**, push és kiadása a **alaphelyzetbe** gombra, majd engedje **gombra A**. A fejlesztői készlet konfigurációs módra vált, és menti azokat a kapcsolati karakterláncot.
    ![Kapcsolati karakterlánc](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kattintson a `F1` ismét gépelje be, és válassza ki **Azure IoT-eszköz Workbench: Töltse fel az eszköz kód**. Azt a fordítási elindul, és töltse fel a kód DevKit.
    ![Arduino feltöltése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE]
> Ha kapcsolatos esetleges hibák vagy megszakítások, mindig helyreállíthatja futtassa újra a parancsot.

## <a name="test-the-project"></a>A projekt tesztelése

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Az Azure IoT hubra küldött telemetriai adatok megtekintése

Kattintson az állapotsorban nyissa meg a soros figyelő power beépülő ikonra: ![Soros figyelője](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A mintaalkalmazás sikeresen fut, amikor megjelenik a következő eredmények:

* A soros figyelő az IoT hubnak küldött üzeneteket jeleníti meg.
* Az MXChip IoT DevKit a LED villogó van.

![Soros figyelő kimenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Az Azure IoT Hub által fogadott telemetriai adatok megtekintése

Használhat [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) az eszközről a felhőbe (D2C) üzenetek az IoT Hub figyeléséhez.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/), keresse meg az IoT Hub létrehozott.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Az a **megosztott elérési házirendek** ablaktáblán kattintson a **iothubowner házirend**, és jegyezze fel az IoT hub kapcsolati karakterláncát.
    ![Az Azure IoT Hub kapcsolati karakterláncra](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. A VS Code-ban kattintson `F1`, írja be, és válassza ki **Azure IoT hubbal: Állítsa be az IoT Hub kapcsolati karakterláncra**. A kapcsolati karakterlánc másolja a fájlba.
    ![Azure IoT Hub kapcsolati karakterlánc beállítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Bontsa ki a **AZURE IOT HUB-eszközök** a jobb oldali ablaktáblán a jobb gombbal a létrehozott, és válassza ki az eszköz nevét **Start beépített esemény végpont**.
    ![D2C üzenet figyelése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. A **kimeneti** panelen láthatja a bejövő D2C-messages az IoT hubnak.
    ![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>A kód áttekintése

A `GetStarted.ino` a fő Arduino rajz fájl.

![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Hogyan eszköz telemetriai adatokat küld az Azure IoT Hub megtekintéséhez nyissa meg a `utility.cpp` fájl ugyanabban a mappában. Nézet [API-referencia](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) , megtudhatja, hogyan használható az érzékelők és perifériák IoT DevKit.

A `DevKitMQTTClient` használt van, egy burkoló a **iothub_client** a a [a Microsoft Azure IoT SDK-k és tárak a c nyelvhez készült](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) kommunikál az Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, ellenőrizheti a megoldás a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Akkor is is küldjön visszajelzést Megjegyzés Ha ezen az oldalon.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta az MXChip IoT DevKit az IoT hubhoz, és az IoT hubnak küldött rögzített érzékelőadatokat.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
