---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz, így adatokat küldhet az Azure felhőalapú platformján.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: e6412ce36110f2e9a800262c968039112f2e4b1c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955683"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Csatlakozás az Azure IoT Hub IoT DevKit AZ3166

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Használhatja a [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) fejlesztéséhez és prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja az Arduino-kompatibilis tábla az gazdag perifériák és érzékelők, egy nyílt forráskódú üzenőfal-csomagot, és egyre növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>TEENDŐ

Csatlakozás a [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) az Ön által létrehozott Azure IoT hub, az adatgyűjtési hőmérséklettel és páratartalommal kapcsolatos érzékelőktől és az IoT hubnak küldi az adatokat.

Még nem rendelkezik egy fejlesztői készlet? Próbálja ki a [DevKit szimulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) vagy [vásárolhat egy DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Ismertetett témák

* Az IoT DevKit csatlakoztatása a vezeték nélküli hozzáférési pont, és a fejlesztési környezet előkészítését.
* Hozzon létre egy IoT hubot és a egy eszközt regisztrálhat az MXChip IoT DevKit módja.
* Hogyan gyűjtheti az érzékelőktől kapott adatok egy mintaalkalmazást az MXChip IoT DevKit történő futtatásával.
* Az érzékelő adatokat küld az IoT hub hogyan.

## <a name="what-you-need"></a>Mi szükséges

* Az MXChip IoT DevKit kártya Micro – USB-kábelen keresztül. [Letöltés most](https://aka.ms/iot-devkit-purchase).
* Windows 10-es vagy macOS 10.10 + rendszert futtató számítógép.
* Aktív Azure-előfizetés. [Ingyenes 30 napos próba Microsoft Azure-fiók aktiválása](https://azureinfo.microsoft.com/us-freetrial.html).
  
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

## <a name="configure-wi-fi"></a>Wi-Fi konfigurálása

Az IoT-projektek internetkapcsolat támaszkodnak. A DevKit kapcsolódni a Wi-Fi konfigurálásához kövesse az alábbi utasításokat.

### <a name="enter-ap-mode"></a>Adja meg a hozzáférési mód

Tartsa lenyomva a gomb a B leküldéses és kiadási a Visszaállítás gombra, és engedje a b gomb A fejlesztői készlet a konfigurálásához a Wi-Fi hozzáférési pont módra vált. A képernyő szolgáltatáskészlet-azonosító (SSID Azonosítóját), a fejlesztői készlet és a konfiguráció portál IP-címét jeleníti meg.

![Alaphelyzetbe állítása gomb, a B gomb, valamint az SSID-t](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Csatlakozás DevKit hozzáférési pont

Most már egy másik Wi-Fi engedélyezése (számítógép vagy mobiltelefon) eszköz használatával kapcsolódhat az DevKit SSID-t (az előző ábrán vannak kiemelve). Hagyja üresen a jelszót.

![Hálózati adatok és a csatlakozás gomb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>A fejlesztői készlet Wi-Fi konfigurálása

Nyissa meg a számítógépen vagy a mobiltelefon böngésző DevKit képernyőn megjelenő IP-címét, válassza ki a Wi-Fi hálózathoz való csatlakozáshoz a DevKit kívánt, és írja be a jelszót. Kattintson a **Csatlakozás** gombra.

![Jelszó mezőbe, és a csatlakozás gomb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Ha a kapcsolódás sikeres, a fejlesztői készlet újraindul, néhány másodperc alatt. Ekkor megjelenik a Wi-Fi neve és IP-címet a képernyőn:

![Wi-Fi neve és IP-cím](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> A fénykép jelenik meg az IP-cím nem egyeznek a tényleges IP-cím hozzárendelve, és a fejlesztői készlet képernyőn jelenik meg. Ez a normál, mert a Wi-Fi IP-címek dinamikus hozzárendelése DHCP használatával.

Wi-Fi beállítások konfigurálása után a hitelesítő adatait az eszköz meg a kapcsolathoz tartozó megmaradnak, még akkor is, ha az eszköz nem csatlakozik. Például ha a DevKit konfigurálhatja az otthoni Wi-Fi, és az Office fejlesztői a készlet megfelelő, szüksége lesz a fejlesztői készlet csatlakoztatása az Office Wi-Fi hozzáférési pont módban (a "Adja meg hozzáférési módban" című szakaszhoz-gyel kezdődik) újrakonfigurálás. 

## <a name="start-using-the-devkit"></a>A fejlesztői készlet használatának megkezdése

Az alapértelmezett alkalmazást a fejlesztői készlet futó ellenőrzi a belső vezérlőprogramjának legújabb verzióját, és néhány diagnosztikai érzékelőadatok megjeleníti az Ön számára.

### <a name="upgrade-to-the-latest-firmware"></a>A legújabb belső vezérlőprogramjának frissítése

> [!NOTE]
> Óta v1.1 DevKit ST környezetben is biztonságos a rendszerbetöltőt lehetővé teszi. A belső vezérlőprogram frissítése, ha v1.1 előtti verziót futtatja kell.

Ha a belső vezérlőprogram frissítése, a képernyő az aktuális és a legújabb verziójú belső vezérlőprogrammal jelennek meg. A frissítéshez kövesse a [belső vezérlőprogram frissítése](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) útmutató.

![Jeleníti meg az aktuális és a legújabb belső vezérlőprogrammal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Ennek célja egy egyszeri. Miután a DevKit elkezdje, és töltse fel az alkalmazás, a legújabb belső vezérlőprogrammal az alkalmazással fognak érkezni.

### <a name="test-various-sensors"></a>Különböző érzékelők tesztelése

Tesztelje az érzékelők B gomb megnyomásával. Továbbra is lenyomásával, és tehessen közzé minden érzékelő válthat a B gomb.

![B gomb és az érzékelő megjelenítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

### <a name="install-azure-iot-workbench"></a>Az Azure IoT Workbench telepítése

Javasoljuk, hogy [Azure IoT Workbench](https://aka.ms/iot-workbench) fejleszthet a a fejlesztői készlet Visual Studio Code-bővítmény.

Az Azure IoT Workbench IoT-megoldások fejlesztéséhez integrált megoldást nyújt. Mindkét Azure IoT-és egyéb szolgáltatásokkal az eszköz és a felhőbeli fejlesztési nyújt segítséget. Ez megtekinthet [Channel 9-videók](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code) kell azt a áttekintését.

Kövesse az alábbi lépéseket a fejlesztési környezet előkészítése a fejlesztői készlet:

1. Töltse le és telepítse [Arduino IDE](https://www.arduino.cc/en/Main/Software). A szükséges eszközlánc biztosít a kódja lefordításának és Arduino kód feltöltése.
    * **Windows**: használata Windows Installer verziószámát. Ne telepítse az app store áruházból.
    * **macOS**: áthúzása a kinyert **Arduino.app** be `/Applications` mappát.
    * **Ubuntu**: például Unzip mappába `$HOME/Downloads/arduino-1.8.5`

2. Telepítés [Visual Studio Code](https://code.visualstudio.com/), a nagy hatékonyságú fejlesztői eszközök, például az IntelliSense kódkiegészítés és hibakeresése platformfüggetlen nyílt forráskódú Kódszerkesztő.

3. Keressen **Azure IoT Workbench** a bővítmény Marketplace-en, és telepítse.
    ![Az Azure IoT Workbench telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) az IoT Workbench együtt más függő bővítmények lesz telepítve.

4. Konfigurálja a Visual Studio Code Arduino beállításokkal. 

    A Visual Studio Code-ban kattintson **fájl > Beállítások > Beállítások**. Kattintson a **...**  és **nyissa meg a settings.json**. 
    
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
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

5. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **Arduino: tábla Manager**. Keresse meg **AZ3166** és telepítse a legújabb verziót.
    ![Fejlesztői készlet SDK telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link-illesztőprogramok telepítése

[ST-hivatkozás/V2](http://www.st.com/en/development-tools/st-link-v2.html) az USB-felület, amely IoT DevKit segítségével kommunikál a fejlesztői gépen. Operációsrendszer-specifikus kövesse az eszköz a gép hozzáférésének engedélyezéséhez.

* **Windows**: Töltse le és telepítse az USB-illesztőprogramot az [STMicroelectronics webhely](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: nincs illesztőprogram nem szükséges a macOS-hez.
* **Ubuntu**: terminált, és a kijelentkezési és a bejelentkezési csoport a módosítás érvénybe léptetéséhez futtassa a következőt:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Most már minden készen állunk előkészítése és a fejlesztési környezet konfigurálása. Ossza meg velünk hozhat létre a "Hello, World" példával IoT: hőmérséklet telemetriai adatok küldését az Azure IoT hubra.

## <a name="build-your-first-project"></a>Az első-projekt létrehozása

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Az alsó megfelelő állapotsávban látható, ellenőrizze a **MXCHIP AZ3166** jelenik meg, mint a kiválasztott táblához és a soros port **STMicroelectronics** szolgál.
    ![Válassza ki a tábla- és COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: Példák**. Válassza ki **IoT DevKit** , tábla.

1. Keresse meg az IoT Workbench példáit tartalmazó oldalon, **Ismerkedés** kattintson **nyílt minta**. Ezután kiválasztja a mintakód letöltése az alapértelmezett elérési utat.
    ![Nyissa meg a minta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Ha a VS Code telepítése Arduino bővítmény nincs, kattintson a **telepítése** az értesítési panelen.
    ![Arduino-bővítményének telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. Kattintson az új ablakban megnyitott projekt `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: Felhő**, majd válassza **Azure kiépítése**. Kövesse a részletes útmutató az Azure IoT Hub provisioning és az eszköz létrehozásának befejezéséhez.
    ![Felhő üzembe helyezése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: eszköz**, majd válassza ki **konfigurációs beállítások > konfigurációs eszköz kapcsolati karakterláncának > Válasszon IoT Hub eszköz kapcsolati karakterláncának**.

1. A fejlesztői készlet, tartsa lenyomva a **gombra A**, push és kiadása a **alaphelyzetbe** gombra, majd engedje **gombra A**. A fejlesztői készlet konfigurációs módra vált, és menti azokat a kapcsolati karakterláncot.
    ![Kapcsolati karakterlánc](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kattintson a `F1` ismét gépelje be, és válassza ki **IoT Workbench: eszköz**, majd **eszköz feltöltése**.
    ![Arduino feltöltése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE]
> Ha hibák vagy megszakítások, mindig helyreállíthatja futtassa újra a parancsot.

## <a name="test-the-project"></a>A projekt tesztelése

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Az Azure IoT hubra küldött telemetriai adatok megtekintése

Kattintson az állapotsorban nyissa meg a soros figyelő power beépülő ikonjára: ![soros figyelője](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A mintaalkalmazás sikeresen fut, amikor megjelenik a következő eredmények:

* A soros figyelő az IoT hubnak küldött üzeneteket jeleníti meg.
* Az MXChip IoT DevKit a LED villogó van.

![Soros figyelő kimenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Az Azure IoT Hub által fogadott telemetriai adatok megtekintése

Használhat [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) az eszközről a felhőbe (D2C) üzenetek az IoT Hub figyeléséhez.

1. A Visual Studio Code-ban keresse meg **Azure IoT-eszközkészlet** a bővítmény Marketplace-en, és telepítse.

1. Jelentkezzen be [az Azure portal](https://portal.azure.com/), keresse meg az IoT Hub létrehozott.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Az a **megosztott elérési házirendek** ablaktáblán kattintson a **iothubowner házirend**, és jegyezze fel az IoT hub kapcsolati karakterláncát.
    ![Az Azure IoT Hub kapcsolati karakterláncra](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Bontsa ki a Visual Studio Code- **AZURE IOT HUB-eszközök** a bal alsó sarokban kattintson **állítsa be IoT Hub kapcsolati karakterláncra**.
    ![Azure IoT Hub kapcsolati karakterlánc beállítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Kattintson a **IoT: D2C üzenet monitorozásának megkezdése** helyi menüben.

1. A **kimeneti** panelen láthatja a bejövő D2C-messages az IoT hubnak.
    ![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, ellenőrizheti a megoldás a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Akkor is is küldjön visszajelzést Megjegyzés Ha ezen az oldalon.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta az MXChip IoT DevKit az IoT hubhoz, és az IoT hubnak küldött rögzített érzékelőadatokat.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
