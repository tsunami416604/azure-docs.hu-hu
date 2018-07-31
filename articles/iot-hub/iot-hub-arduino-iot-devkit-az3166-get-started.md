---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 csatlakozhat az Azure IoT Hub |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be és IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz, így adatokat küldhet az Azure felhőalapú platformján.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 34088440bf723fc65f4ff5c49f62182f405fa35a
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344545"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 csatlakoztatása Azure IoT hubhoz a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Használhatja a [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) fejlesztéséhez és prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja az Arduino-kompatibilis tábla az gazdag perifériák és érzékelők, egy nyílt forráskódú üzenőfal-csomagot, és egyre növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>TEENDŐ
Csatlakozás a [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) az Ön által létrehozott Azure IoT hub, az adatgyűjtési hőmérséklettel és páratartalommal kapcsolatos érzékelőktől és az IoT hubnak küldi az adatokat.

Még nem rendelkezik egy fejlesztői készlet? Próbálja ki [DevKit szimulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) vagy [igényelhet](https://aka.ms/iot-devkit-purchase).

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

A hardvert, hogy a számítógép kapcsolni.

A hardver lesz szüksége:

* Fejlesztői készlet tábla
* Micro – USB-kábelen keresztül

![Szükséges hardver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

A fejlesztői készlet a számítógéphez való kapcsolódáshoz:

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
> Óta v1.1 DevKit ST környezetben is biztonságos a rendszerbetöltőt lehetővé teszi. Belső vezérlőprogram frissítése annak érdekében, hogy a helyes működéséhez valószínűleg v1.1 belül fut, ha szüksége.

Ha a belső vezérlőprogram frissítése, a képernyő az aktuális és a legújabb verziójú belső vezérlőprogrammal jelennek meg. A frissítéshez kövesse a [belső vezérlőprogram frissítése](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) útmutató.

![Jeleníti meg az aktuális és a legújabb belső vezérlőprogrammal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Ennek célja egy egyszeri. Miután a DevKit elkezdje, és töltse fel az alkalmazás, a legújabb belső vezérlőprogrammal az alkalmazással fognak érkezni.

### <a name="test-various-sensors"></a>Különböző érzékelők tesztelése

Tesztelje az érzékelők B gomb megnyomásával. Továbbra is lenyomásával, és tehessen közzé minden érzékelő válthat a B gomb.

![B gomb és az érzékelő megjelenítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Most a fejlesztési környezet beállítása ideje: eszközök és lenyűgöző IoT-alkalmazások csomagjai. Kiválaszthatja a Windows vagy macOS-verzió az operációs rendszerének megfelelően.

### <a name="windows"></a>Windows

Javasoljuk, hogy a fejlesztési környezet előkészítése a telepítési csomag használatára. Ha problémákat tapasztal, kövesse a [manuális lépések](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) Letöltés kész.

#### <a name="download-the-latest-package"></a>Töltse le a legújabb csomag

A letöltött .zip fájl tartalmazza a szükséges eszközöket és a csomagok DevKit fejlesztéshez.

> [!div class="button"]
[Letöltés](https://aka.ms/devkit/prod/installpackage/latest)

A .zip-fájlban található, a következő eszközök és a csomagokat. Ha már rendelkezik néhány összetevő telepítve van, a parancsfájl érzékeli, és hagyja ki őket.

* NODE.js-t és a Yarn: futtatókörnyezet a telepítési parancsfájl és automatizált feladatokat.
* [Az Azure CLI 2.0-s MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): platformfüggetlen parancssori felülete Azure-erőforrások kezeléséhez. Az MSI függő Python és a pip tartalmazza.
* [A Visual Studio Code](https://code.visualstudio.com/) (a VS Code): könnyű Kódszerkesztő DevKit fejlesztéséhez.
* [A Visual Studio Code-bővítmény Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): bővítmény, amely a Visual Studio Code Arduino fejlesztést tesz lehetővé.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): eszköz, amely a bővítményt Arduino támaszkodik.
* Fejlesztői készlet tábla csomag: Láncok, könyvtárak és projektek eszköze, a fejlesztői készlet.
* ST-Link-segédprogram: Alapvető eszközök és illesztőprogramok.

#### <a name="run-the-installation-script"></a>A telepítési parancsfájl futtatása

A Windows Fájlkezelőben keresse meg a .zip-fájlt, és csomagolja. Keresés `install.cmd`, kattintson a jobb gombbal, és válassza ki **Futtatás rendszergazdaként**.

![Fájlkezelő](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

A telepítés során minden egyes eszköz vagy a csomag állapotának megtekintéséhez.

![Telepítés folyamatban](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> A környezettől függően néha kap hiba Arduino IDE telepítésekor. Ebben az esetben megpróbálhatja [Arduino IDE külön-külön telepíteni](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) install.cmd futtassa újból. Ellenkező esetben kövesse a [manuális lépések](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) szükséges eszközöket és a csomagok telepítése.

#### <a name="install-drivers"></a>Illesztőprogramok telepítése

A VS Code Arduino-bővítmény az Arduino IDE támaszkodik. Ha először telepíti az Arduino IDE, kéri, hogy a megfelelő illesztőprogramok telepítése:

![első lépések-illesztőprogram](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Telepítés körülbelül 10 percig, attól függően, az internetkapcsolat sebessége vesz igénybe. A telepítés befejezése után megjelenik a Visual Studio Code és Arduino IDE parancsikonokat az asztalon.

> [!NOTE] 
> Néha előfordul amikor elindítja a VS Code, kér egy hiba miatt, hogy nem található az Arduino IDE vagy kapcsolódó tábla csomag. Megoldhatja a problémát, zárja be a VS Code, és indítsa újra az Arduino IDE. A VS Code kell majd keresse meg az Arduino IDE elérési út megfelelően.

### <a name="macos"></a>macOS

Azt javasoljuk, hogy egyetlen kattintással telepítési élmény a fejlesztési környezet előkészítését. Ha problémákat tapasztal, kövesse a [manuális lépések](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) Letöltés kész.

#### <a name="install-homebrew"></a>Telepítse a homebrew-val

> [!NOTE] 
> Ha már telepítette a homebrew-val, kihagyhatja ezt a lépést.

Kövesse a [Homebrew telepítési útmutatója](https://docs.brew.sh/Installation.html) telepítheti.

#### <a name="download-the-latest-package"></a>Töltse le a legújabb csomag
A letöltött .zip fájl tartalmazza a szükséges eszközöket és a csomagok DevKit fejlesztéshez.

> [!div class="button"]
[Letöltés](https://aka.ms/devkit/prod/installpackage/mac/latest)

A .zip-fájlban található, a következő eszközök és a csomagokat. Ha már rendelkezik néhány összetevő telepítve van, a parancsfájl érzékeli, és hagyja ki őket.

* NODE.js-t és a Yarn: futtatókörnyezet a telepítési parancsfájl és automatizált feladatokat.
* [Az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): platformfüggetlen parancssori felülete Azure-erőforrások kezeléséhez.
* [A Visual Studio Code](https://code.visualstudio.com/) (a VS Code): könnyű Kódszerkesztő DevKit fejlesztéséhez.
* [A Visual Studio Code-bővítmény Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): bővítmény, amely a Visual Studio Code Arduino fejlesztést tesz lehetővé.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): eszköz, amely a bővítményt Arduino támaszkodik.
* Fejlesztői készlet tábla csomag: Láncok, könyvtárak és projektek eszköze, a fejlesztői készlet.
* ST-Link-segédprogram: Alapvető eszközök és illesztőprogramok.

#### <a name="run-the-installation-script"></a>A telepítési parancsfájl futtatása

A Keresőben keresse meg a .zip és csomagolja ki:

![macOS Keresőben](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Indítsa el a Terminálszolgáltatások alkalmazást, keresse meg a mappát, bontsa ki a .zip-fájlt, és futtassa:

```bash
./install.sh
```

![macOS-telepítés](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Ha a homebrew-val engedélyhiba megfelel, futtassa `brew doctor` hiba javításához. Ellenőrizze [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) további részletekért.

Most már rendelkezik a szükséges eszközöket és a MacOS rendszeren telepített csomagok.


## <a name="open-the-project-folder"></a>A projektmappa fájllistájának megnyitásához.

### <a name="start-vs-code"></a>Indítsa el a VS Code

Ellenőrizze, hogy a fejlesztői készlet nincs csatlakoztatva. Először indítsa el a VS Code és a fejlesztői készlet csatlakoztatása a számítógéphez. A VS Code automatikusan megkeresi a fejlesztői készlet és egy bemutató lap nyílik meg:

![Bemutató lapja](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Néha előfordul amikor elindítja a VS Code, kér egy hiba miatt, hogy nem található az Arduino IDE vagy kapcsolódó tábla csomag. Zárja be a VS Code, és indítsa újra az Arduino IDE. A VS Code kell majd keresse meg az Arduino IDE elérési út megfelelően.


### <a name="open-the-arduino-examples-folder"></a>Az Arduino példák mappa megnyitása

Az a **Arduino példák** lapra, keresse meg a **példák az MXCHIP AZ3166** > **AzureIoT**, és válassza ki **GetStarted**.

![Arduino példák lap](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Zárja be a panelt az MSDN-előfizetőknek, megnyithatja azt. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="provision-azure-services"></a>Azure-szolgáltatások üzembe helyezése

A megoldás ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision`.

A VS Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése:

![Interaktív parancssor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Hozhat létre és töltse fel az Arduino áttekintése

### <a name="windows"></a>Windows

1. Használat `Ctrl+P` futtatásához `task device-upload`.
2. A terminálban kéri, hogy adja meg a konfigurációs mód. Ehhez A gombot lenyomva, majd küldje le, és engedje a Visszaállítás gombra. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

Ez a beállítjuk a kapcsolati karakterláncot, amely lekéri a `task cloud-provision` . lépés.

A VS Code elindít annak ellenőrzésére, és töltse fel az Arduino ábrát:

![Ellenőrzés és feltöltése az Arduino áttekintése](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE] 
> Néha előfordul, hibaüzenetet "hiba: AZ3166: Ismeretlen csomag". Ez a tábla miatt van csomagindexet nem frissül. Ennek ellenőrzéséhez [– gyakori kérdések lépéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) megoldhatja a problémát.

### <a name="macos"></a>macOS

1. Fejlesztői készlet konfigurációs módba: tartsa lenyomva A gombra, majd leküldéses és -kiadási a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".
2. Használat `Cmd+P` futtatásához `task device-upload`.

Ez a beállítjuk a kapcsolati karakterláncot, amely lekéri a `task cloud-provision` . lépés.

A VS Code elindít annak ellenőrzésére, és töltse fel az Arduino ábrát:

![eszköz-feltöltést](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

> [!NOTE] 
> Néha előfordul, hibaüzenetet "hiba: AZ3166: Ismeretlen csomag". Ez a tábla miatt van csomagindexet nem frissül. Ennek ellenőrzéséhez [– gyakori kérdések lépéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) megoldhatja a problémát.


## <a name="test-the-project"></a>A projekt tesztelése

A VS Code-ban kövesse az alábbi lépéseket, nyissa meg, és a soros figyelő beállítása:

1. Kattintson a `COM[X]` szót tartalmaz, a megfelelő COM-port beállítása az állapotsor `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Kattintson az állapotsorban nyissa meg a soros figyelő power beépülő ikonjára: ![soros-figyelő](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Az állapotsoron kattintson a szám, az átviteli sebességet jelöl, és állítsa `115200`: ![átviteli sebesség](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

A mintaalkalmazás sikeresen fut, amikor megjelenik a következő eredmények:

* A soros figyelő ugyanazokat az információkat az alábbi képernyőképen a tartalom jeleníti meg.
* A az MXChip IoT DevKit RGB LED villogó van.

![Végeredmény a VS Code-ban](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, Észreveheti, hogy [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Akkor is is küldjön visszajelzést Megjegyzés Ha ezen az oldalon.

## <a name="next-steps"></a>További lépések

Sikeresen csatlakoztatta az MXChip IoT DevKit az IoT hubhoz, és az IoT hubnak küldött rögzített érzékelőadatokat.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
