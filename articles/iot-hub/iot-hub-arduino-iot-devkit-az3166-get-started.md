---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz, így adatokat küldhet az Azure felhőalapú platformján.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: wesmc
ms.openlocfilehash: 0e668c0b31defd5c945ddf2fec1b00f75d92f174
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990153"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Csatlakozás az Azure IoT Hub IoT DevKit AZ3166

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Használhatja a [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) fejlesztéséhez és prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja az Arduino-kompatibilis tábla az gazdag perifériák és érzékelők, egy nyílt forráskódú üzenőfal-csomagot, és egyre növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>TEENDŐ

A fejlesztői készlet csatlakozhat az Azure IoT hub által létrehozott. Ezután a hőmérséklettel és páratartalommal kapcsolatos adatokat gyűjteni az érzékelők, és az IoT hubnak küldi az adatokat.

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

![Hozzáférési mód beállítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Csatlakozás DevKit hozzáférési pont

Most már egy másik Wi-Fi engedélyezése (számítógép vagy mobiltelefon) eszköz használatával kapcsolódhat az DevKit SSID-t (az előző ábrán vannak kiemelve). Hagyja üresen a jelszót.

![Hálózati adatok és a csatlakozás gomb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>A fejlesztői készlet Wi-Fi konfigurálása

Nyissa meg a számítógépen vagy a mobiltelefon böngésző DevKit képernyőn megjelenő IP-címét, válassza ki a Wi-Fi hálózathoz való csatlakozáshoz a DevKit kívánt, és írja be a jelszót. Kattintson a **Csatlakozás** gombra.

![Jelszó mezőbe, és a csatlakozás gomb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Ha a kapcsolódás sikeres, a fejlesztői készlet újraindul, néhány másodperc alatt. Ekkor megjelenik a Wi-Fi neve és IP-címet a képernyőn:

![Wi-Fi neve és IP-cím](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Szüksége lesz egy 2,4 GHz-es hálózati IoT DevKit használata. Az IoT DevKit meg a Wi-Fi modul, ezért nem kompatibilis az 5 GHz-es hálózati. Ellenőrizze [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) további részletekért.

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

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Javasoljuk, hogy [Azure IoT-eszközök](https://aka.ms/azure-iot-tools) fejleszthet a a fejlesztői készlet Visual Studio Code készült bővítőcsomagjának. Az Azure IoT-eszközöket tartalmaz [Azure IoT-eszköz Workbench](https://aka.ms/iot-workbench) fejlesztésről és hibakeresésről IoT devkit különféle eszközökön és [Azure IoT Hub-eszközkészlet](https://aka.ms/iot-toolkit) kezelhesse és használhassa az Azure IoT Hub szolgáltatással.

Megtekinthet ezek [Channel 9](https://channel9.msdn.com/) rendelkeznie működésével kapcsolatos áttekintő videók:
* [A VS Code az új IoT Workbench-bővítmény bemutatása](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [Az IoT-eszközkészlet bővítmény a VS Code újdonságai](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

Kövesse az alábbi lépéseket a fejlesztési környezet előkészítése a fejlesztői készlet:

1. Telepítés [Arduino IDE](https://www.arduino.cc/en/Main/Software). A szükséges eszközlánc biztosít a kódja lefordításának és Arduino kód feltöltése.
    * **Windows**: Windows Installer verzióját használja. Ne telepítse az app store áruházból.
    * **macOS**: Oszlopmezők áthúzása a kinyert **Arduino.app** be `/Applications` mappát.
    * **Ubuntu**: Bontsa ki azt a mappába például `$HOME/Downloads/arduino-1.8.8`

2. Telepítés [Visual Studio Code](https://code.visualstudio.com/), a nagy hatékonyságú fejlesztői eszközök, például az IntelliSense kódkiegészítés és hibakeresése platformfüggetlen nyílt forráskódú Kódszerkesztő.

3. Indítsa el a VS Code, keresse meg a **Arduino** a bővítmény Marketplace-en, és telepítse. Ez a bővítmény Arduino platform fejlesztéséhez fokozott élményt biztosít.
    ![Arduino telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Keressen **Azure IoT-eszközök** a bővítmény Marketplace-en, és telepítse.
    ![Az Azure IoT-eszközök telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

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

[ST-hivatkozás/V2](http://www.st.com/en/development-tools/st-link-v2.html) az USB-felület, amely IoT DevKit segítségével kommunikál a fejlesztői gépen. Telepítenie kell azt a flash engedélyezése Windows a lefordított deivce kódot, a fejlesztői készlet. Operációsrendszer-specifikus kövesse az eszköz a gép hozzáférésének engedélyezéséhez.

* **Windows**: Töltse le és telepítse az USB-illesztőprogramot az [STMicroelectronics webhely](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Nincs illesztőprogram nem szükséges a macOS-hez.
* **Ubuntu**: Futtassa a következő terminálon és jelentkezzen ki, és jelentkezzen be a csoport módosítás érvénybe léptetéséhez:
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


1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Példák megnyitása...** . Válassza ki **IoT DevKit** , tábla.

1. Keresse meg az IoT Workbench példáit tartalmazó oldalon, **Ismerkedés** kattintson **nyílt minta**. Ezután kiválasztja a mintakód letöltése az alapértelmezett elérési utat.
    ![Nyissa meg a minta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Kattintson az új ablakban megnyitott projekt `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Azure-szolgáltatások üzembe helyezése...** . Kövesse a részletes útmutató az Azure IoT Hub provisioning és az IoT Hub-eszköz létrehozása gombra.
    ![Felhő üzembe helyezése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

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

1. Bontsa ki a **AZURE IOT HUB-eszközök** a jobb oldali ablaktáblán a jobb gombbal a létrehozott, és válassza ki az eszköz nevét **figyelési D2C üzenet Start**.
    ![D2C üzenet figyelése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. A **kimeneti** panelen láthatja a bejövő D2C-messages az IoT hubnak.
    ![D2C üzenet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, ellenőrizheti a megoldás a [IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Akkor is is küldjön visszajelzést Megjegyzés Ha ezen az oldalon.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta az MXChip IoT DevKit az IoT hubhoz, és az IoT hubnak küldött rögzített érzékelőadatokat.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
