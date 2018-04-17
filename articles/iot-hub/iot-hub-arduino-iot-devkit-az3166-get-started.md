---
title: 'A felhőben az IoT-DevKit: IoT DevKit AZ3166 csatlakozzon az Azure IoT Hub |} Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan beállítását, és csatlakozzon az IoT DevKit AZ3166 Azure IoT Hub, adatokat küldhet az Azure felhőalapú platform.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 3d83875171c7c097bac5a70852f049a6ce4219b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 csatlakozni az Azure IoT Hub a felhőben

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Használhatja a [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) fejlesztéséhez és prototípus az eszközök internetes hálózatát (IoT) megoldások, amelyek a Microsoft Azure-szolgáltatások előnyeit. Ez magában foglalja egy Arduino-kompatibilis board gazdag perifériák és érzékelők, egy nyílt forráskódú board csomag és egy növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Mit
Csatlakozás a [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) az Azure IoT-központ az Ön által létrehozott, az adatgyűjtés a hőmérséklet és a páratartalom a érzékelők, és elküldheti az adatokat az IoT-központ.

Még nem rendelkezik egy DevKit? Próbálja [DevKit szimulátor](https://azure-samples.github.io/iot-devkit-web-simulator/) vagy [lekérni egy](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Ismertetett témák

* Az IoT-DevKit csatlakoztatása a vezeték nélküli hozzáférési pont, és a fejlesztőkörnyezet előkészítése.
* Létrehoz egy IoT-központot, és egy eszköz regisztrálásához a MXChip IoT DevKit módjáról.
* Megtudhatja, hogyan futtatja a mintaalkalmazás az MXChip IoT DevKit érzékelő adatok gyűjtéséért felelős ügyfélfeladatot.
* Az érzékelő adatokat küldeni az IoT hub módjáról.

## <a name="what-you-need"></a>Mi szükséges

* Egy MXChip IoT DevKit kártya Micro-USB-kábellel. [Most töltse le innen](https://aka.ms/iot-devkit-purchase).
* A Windows 10 vagy macOS 10.10 + rendszert futtató számítógépen.
* Aktív Azure-előfizetés. [Egy ingyenes 30 napos próba Microsoft Azure-fiók aktiválása](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Készítse elő a hardvert

A hardvert, hogy a számítógép a számítógéphez.

Ehhez a hardverhez lesz szüksége:

* DevKit tábla
* Micro-USB-kábellel

![Szükséges hardver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

A DevKit kapcsolódni a számítógéphez:

1. Az USB-end kapcsolódni a számítógéphez.
2. A DevKit Micro-USB vége csatlakozni.
3. A zöld LED bekapcsolási megerősíti, hogy a kapcsolat.

![Hardver kapcsolatok](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi konfigurálása

IoT-projektek internetkapcsolat támaszkodnak. Wi-Fi csatlakozni DevKit konfigurálásához kövesse az alábbi utasításokat.

### <a name="enter-ap-mode"></a>Ázsia és a Csendes üzemmód megadása

Tartsa lenyomva a gombot a B kiszolgálóra, leküldéses és kiadási a Visszaállítás gombra, majd engedje fel gombra a b kiszolgálóra. A DevKit Ázsia és a Csendes módban kerül, Wi-Fi konfigurálásához. A képernyőn látható szolgáltatáskészlet-azonosító (SSID) a DevKit és a konfigurációs portál IP-címe.

![Gombra, a B gomb és SSID alaphelyzetbe állítása](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Ázsia és a Csendes DevKit kapcsolódni

Most egy másik Wi-Fi engedélyezése (számítógép vagy telefonon keresztül) eszköz használatával csatlakozhat a DevKit SSID (az előző ábrán kiemelt). Hagyja üresen a jelszót.

![Hálózat adatai és a Csatlakozás gombra](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Wi-Fi konfigurálása a DevKit

Nyissa meg az IP-címet a számítógépen vagy a mobiltelefon böngésző DevKit képernyőn látható, válassza ki a Wi-Fi hálózathoz való kapcsolódáshoz DevKit, és írja be a jelszót. Kattintson a **Csatlakozás** gombra.

![Jelszó és a Csatlakozás gombra](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

A csatlakozás sikeres, ha a DevKit néhány másodperc múlva újraindul. Ekkor megjelenik a Wi-Fi nevét és IP-címet a képernyőn:

![Wi-Fi nevét és IP-cím](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Az IP-cím jelenik meg a fénykép nem egyeznek a tényleges IP-cím hozzárendelése és a DevKit képernyőn jelenik meg. Ez nem jelent problémát, mert a Wi-Fi IP-címek dinamikus hozzárendelése DHCP használatával.

Wi-Fi beállítások konfigurálása után a hitelesítő adatok az eszközön, hogy a kapcsolat megmarad, még akkor is, ha az eszköz nincs csatlakoztatva. Például ha az otthoni Wi-Fi a DevKit konfigurálása, majd hajtsa végre a DevKit az Office, szüksége lesz a DevKit kapcsolódás a Microsoft office Wi-Fi újrakonfigurálás Ázsia és a Csendes módban (a "Adja meg Ázsia és a Csendes módban" című lépés kezdve). 

## <a name="start-using-the-devkit"></a>A DevKit használatának megkezdése

Az alapértelmezett alkalmazás a DevKit futó ellenőrzi a belső vezérlőprogramjának legújabb verzióját, és meg néhány érzékelő diagnosztikai adatait jeleníti meg.

### <a name="upgrade-to-the-latest-firmware"></a>A legújabb belső vezérlőprogram frissítése

> [!NOTE] 
> 1.1, óta DevKit engedélyezi ST SZÁLBIZTOS rendszertöltő. Belső vezérlőprogram frissítése, ha le v1.1 ahhoz, hogy a helyes működéséhez valószínűleg szüksége.

Ha a belső vezérlőprogram frissítése, a képernyő megjeleníti az aktuális és a legújabb belső vezérlőprogramjának következő verziójával. A frissítéshez kövesse a [belső vezérlőprogram frissítése](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) útmutató.

![Megjeleníti a jelenlegi és a legújabb belső vezérlőprogramjának következő verziójával](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Ez egy egyszeri beavatkozást. Miután a DevKit elkezdje, és töltse fel az alkalmazást, a legújabb belső vezérlőprogrammal határozza meg az alkalmazással.

### <a name="test-various-sensors"></a>Különböző érzékelők tesztelése

Nyomja le az érzékelők teszteléséhez B gomb. Továbbra is lenyomásával, és tehessen közzé minden érzékelő válthat a B gomb.

![B gomb és érzékelő megjelenítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Miután beállította a fejlesztőkörnyezetet ideje: eszközök és a csomagok elkészítéséhez hatásos IoT-alkalmazásokhoz. Kiválaszthatja a Windows vagy macOS verziója az operációs rendszer alapján.

### <a name="windows"></a>Windows

Javasoljuk, hogy a fejlesztési környezet előkészítése a telepítési csomag használatára. Ha bármilyen problémába ütközik, kövesse a [manuális](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) Letöltés helye történik.

#### <a name="download-the-latest-package"></a>A legújabb csomag

A letöltött .zip fájl tartalmazza a szükséges eszközök és a csomagok DevKit fejlesztési.

> [!div class="button"]
[Letöltés](https://aka.ms/devkit/prod/installpackage/latest)

A zip-fájlt a következő eszközök és csomagokat tartalmazza. Ha már rendelkezik néhány összetevőt telepíteni, a parancsfájl azonosítja, és hagyja ki őket.

* NODE.js és a Yarn: futásidejű a beállítási parancsfájlt és automatikus feladatokat.
* [Az Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): platformfüggetlen parancssori élmény Azure-erőforrások kezeléséhez. Az MSI függő Python és a pip tartalmazza.
* [A Visual Studio Code](https://code.visualstudio.com/) (Visual STUDIO Code): DevKit fejlesztési egyszerűsített kód szerkesztése.
* [Visual Studio Code kiterjesztése Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): bővítményt, amely lehetővé teszi, hogy a Visual Studio Code Arduino fejlesztésbe.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): eszköz, amely a bővítményt Arduino támaszkodik.
* DevKit Bizottság csomag: Eszköz kapcsolódik, könyvtárak és a DevKit projekteket.
* St. csatolású segédprogram: Alapvető eszközök és illesztőprogramok.

#### <a name="run-the-installation-script"></a>A telepítési parancsfájl futtatása

A Windows Fájlkezelőben keresse meg a .zip fájlt, és bontsa ki. Található `install.cmd`, a jobb gombbal kattintson rá, és válassza ki **Futtatás rendszergazdaként**.

![A Fájlkezelőben](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

A telepítés során minden egyes eszköz vagy a csomag előrehaladásának megtekintéséhez.

![Folyamatban lévő telepítés](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> A használt környezettől függően néha kap hiba Arduino IDE telepítésekor. Ebben az esetben megpróbálhatja újból [Arduino IDE külön-külön telepíteni](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) install.cmd futtassa újból. Ellenkező esetben hajtsa végre a [manuális](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) szükséges eszközök és a csomagok telepítése.

#### <a name="install-drivers"></a>Illesztőprogramok telepítése

A Visual STUDIO kódot Arduino kiterjesztés az Arduino IDE támaszkodik. Ha ez az Arduino IDE telepíti először, felszólítást megfelelő illesztőprogramok telepítése:

![első lépések-illesztőprogram](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Telepítési gyorsabban körülbelül 10 percig, attól függően, hogy az internet-hozzáféréssel. A telepítés befejezése után meg kell jelennie a Visual Studio Code és Arduino IDE parancsikonok az asztalon.

> [!NOTE] 
> Alkalmanként amikor elindítja a Visual STUDIO Code, megkéri hiba történt, hogy nem található a Arduino IDE vagy a kapcsolódó tábla csomag. Oldja meg azt, zárja be a Visual STUDIO Code, és indítsa újra az Arduino IDE. VS kódot kell majd keresse meg a Arduino IDE elérési megfelelően.

### <a name="macos"></a>macOS

Javasoljuk, hogy az egy kattintással telepítési élmény segítségével készítse elő a fejlesztési környezetet. Ha bármilyen problémába ütközik, kövesse a [manuális](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) Letöltés helye történik.

#### <a name="install-homebrew"></a>Homebrew telepítése

> [!NOTE] 
> Ha telepítette a Homebrew, kihagyhatja ezt a lépést.

Kövesse a [Homebrew telepítési utasításokat](https://docs.brew.sh/Installation.html) a telepítéshez.

#### <a name="download-the-latest-package"></a>A legújabb csomag
A letöltött .zip fájl tartalmazza a szükséges eszközök és a csomagok DevKit fejlesztési.

> [!div class="button"]
[Letöltés](https://aka.ms/devkit/prod/installpackage/mac/latest)

A zip-fájlt a következő eszközök és csomagokat tartalmazza. Ha már rendelkezik néhány összetevőt telepíteni, a parancsfájl azonosítja, és hagyja ki őket.

* NODE.js és a Yarn: futásidejű a beállítási parancsfájlt és automatikus feladatokat.
* [Az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): platformfüggetlen parancssori élmény Azure-erőforrások kezeléséhez.
* [A Visual Studio Code](https://code.visualstudio.com/) (Visual STUDIO Code): DevKit fejlesztési egyszerűsített kód szerkesztése.
* [Visual Studio Code kiterjesztése Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): bővítményt, amely lehetővé teszi, hogy a Visual Studio Code Arduino fejlesztésbe.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): eszköz, amely a bővítményt Arduino támaszkodik.
* DevKit Bizottság csomag: Eszköz kapcsolódik, könyvtárak és a DevKit projekteket.
* St. csatolású segédprogram: Alapvető eszközök és illesztőprogramok.

#### <a name="run-the-installation-script"></a>A telepítési parancsfájl futtatása

A Keresőben keresse meg a .zip, és bontsa ki:

![macOS kereső](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Indítsa el a Terminálszolgáltatások alkalmazást, bontsa ki a .zip fájlt, és futtassa a mappa megkereséséhez:

```bash
./install.sh
```

![macOS telepítése](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Ha megfelel a Homebrew engedély hiba, futtassa `brew doctor` Letöltés helye rögzített. Ellenőrizze [gyakran ismételt kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) további részleteket.

Most már rendelkezik a szükséges eszközök és a macOS telepített csomagok.


## <a name="open-the-project-folder"></a>Nyissa meg a projekt mappát

### <a name="start-vs-code"></a>Indítsa el a Visual STUDIO Code

Győződjön meg arról, hogy a DevKit nincs csatlakoztatva. Indítsa el a Visual STUDIO Code először, és a DevKit kapcsolódni a számítógéphez. Visual STUDIO Code automatikusan megkeresi a DevKit és bemutatása lap megnyitása:

![Bemutató lapja](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Alkalmanként amikor elindítja a Visual STUDIO Code, megkéri hiba történt, hogy nem található a Arduino IDE vagy a kapcsolódó tábla csomag. Zárja be a Visual STUDIO Code, és indítsa újra az Arduino IDE. VS kódot kell majd keresse meg a Arduino IDE elérési megfelelően.


### <a name="open-the-arduino-examples-folder"></a>Nyissa meg a Arduino példák

Az a **Arduino példák** lapján keresse meg a **MXCHIP AZ3166 példák** > **AzureIoT**, és válassza ki **GetStarted**.

![Arduino példák lap](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Ha véletlenül bezárja a panelen, akkor nyissa meg újra. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldás-ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision`.

A Visual STUDIO Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése:

![Interaktív parancssor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Build és a Arduino vázlat feltöltése

### <a name="windows"></a>Windows

1. Használjon `Ctrl+P` futtatásához `task device-upload`.
2. A Terminálszolgáltatások konfigurációs módja megadását kéri. Ehhez az szükséges, tartsa lenyomva a gombot A, majd leküldéses és kiadási a Visszaállítás gombra. A képernyőn megjelenik a DevKit azonosító és a "Konfiguráció".

Ez is, hogy a kapcsolati karakterláncot, amely kikeresi `task cloud-provision` lépés.

Ezután a Visual STUDIO Code elindítja ellenőrzése, majd ismét feltölteni a Arduino ábrát:

![Ellenőrzési és a Arduino rajz feltöltése](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

A DevKit újraindul, és elindul a kódot.

> [!NOTE] 
> Alkalmanként, akkor a hibaüzenet "hiba: AZ3166: Ismeretlen csomag". Ennek az az oka a tábla csomagindexet nem frissül. Ellenőrizze a [gyakran ismételt kérdések lépéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) megoldására azt.

### <a name="macos"></a>macOS

1. DevKit konfigurációs üzemmódba: tartsa lenyomva a gombot A, majd leküldéses és kiadás a Visszaállítás gombra. A képernyőn megjelenik a "Konfiguráció".
2. Használjon `Cmd+P` futtatásához `task device-upload`.

Ez is, hogy a kapcsolati karakterláncot, amely kikeresi `task cloud-provision` lépés.

Ezután a Visual STUDIO Code elindítja ellenőrzése, majd ismét feltölteni a Arduino ábrát:

![eszköz-feltöltése](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

A DevKit újraindul, és elindul a kódot.

> [!NOTE] 
> Alkalmanként, akkor a hibaüzenet "hiba: AZ3166: Ismeretlen csomag". Ennek az az oka a tábla csomagindexet nem frissül. Ellenőrizze a [gyakran ismételt kérdések lépéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) megoldására azt.


## <a name="test-the-project"></a>A projekt tesztelése

Visual STUDIO Code, következő megnyitásához, és állítsa be a soros figyelő lépéseket:

1. Kattintson a `COM[X]` az állapotsoron a megfelelő COM-port az a word `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Energiagazdálkodási plug ikonra az állapotsoron a soros figyelő megnyitásához: ![soros-figyelő](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Az állapotsorban, kattintson az átviteli sebesség a számát, és állítsa `115200`: ![átviteli sebesség](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

A mintaalkalmazás sikeresen futása közben a következő eredményt látja:

* A soros figyelő ugyanazokat az információkat az alábbi képernyőképen látható tartalom jeleníti meg.
* A MXChip IoT DevKit LED villogó van.

![Végső kimenetet a Visual STUDIO Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, található [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Akkor is is küldjön visszajelzést Megjegyzés mivel ezen a lapon.

## <a name="next-steps"></a>További lépések

Egy MXChip IoT DevKit sikeresen csatlakozott az IoT hub, és a rögzített érzékelő adatokat küldött az IoT hub.

Ismerkedés az Azure IoT Hub folytatja, és más IoT-forgatókönyvek esetén eltérések felfedezése, lásd:

- [Eszközök felhőalapú üzenetkezelése az iothub-explorerrel](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [IoT Hub-üzenetek mentése Azure-adattárba](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Az Azure IoT Hub valós idejű érzékelőadatok megjelenítése a Power BI használatával](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Az Azure IoT Hub valós idejű érzékelőadatok megjelenítése a Web Apps használatával](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Az érzékelő adatokat az IoT hub használata az Azure Machine Learning előrejelzési időjárási](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Eszközkezelés az iothub-explorerrel](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Távoli figyelés és értesítések a Logic Apps használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
