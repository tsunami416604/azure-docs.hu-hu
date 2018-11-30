---
title: IoT DevKit csatlakoztatása a távoli figyelési megoldásgyorsító – Azure |} A Microsoft Docs
description: Ez az útmutató útmutatóban megismerheti, hogyan telemetriát küldjön az IoT DevKit AZ3166 eszközön az érzékelők a távoli figyelési megoldásgyorsító a monitorozási és a Vizualizáció a.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7f67868f6220ab2940aa8ac4d4bf24f82191cc22
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620251"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Egy IoT DevKit eszköz csatlakoztatása a távoli figyelési megoldásgyorsító

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az útmutató bemutatja, hogyan futtathat egy mintaalkalmazást az IoT DevKit eszközén. A mintakód telemetriai adatokat küld a megoldásgyorsító az érzékelőktől az DevKit eszközön.

A [IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) Visual Studio Code-ban. A [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) mintaalkalmazások prototípus IoT-megoldások segítségével tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a [IoT DevKet első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) fejezze be a következő szakaszokban:

* Készítse elő a hardvert
* Wi-Fi konfigurálása
* A fejlesztői készlet használatának megkezdése
* A fejlesztési környezet előkészítése

## <a name="open-the-sample"></a>Nyissa meg a minta

A távoli figyelési minta megnyitása a VS Code-ban:

1. Ellenőrizze, hogy az IoT fejlesztői készlet nincs a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: Példák**. Válassza ki **IoT DevKit** , tábla.

1. Keresés **távoli figyelési** kattintson **nyílt minta**. Egy új VS Code-ablak nyílik meg, a projektmappa fájllistájának megjelenítése:

  ![IoT Workbench, válassza a távoli figyelési példa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Az eszköz konfigurálása

Az IoT Hub eszköz kapcsolati karakterláncának konfigurálása DevKit eszközén:

1. Az IoT DevKit való váltás **konfigurációs mód**:

    * Tartsa lenyomva a gomb **A**.
    * Leküldéses és kiadása a **alaphelyzetbe** gombra.

1. A képernyőn megjelenik a fejlesztői készlet azonosítója és `Configuration`.

    ![IoT DevKit konfigurációs mód](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Nyomja meg **F1** nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: eszköz > konfigurációs beállítások**.

1. Illessze be a korábban kimásolt kapcsolati karakterláncot, és nyomja le az **Enter** beállítani az eszközt.

## <a name="build-the-code"></a>A kód létrehozása

Hozhat létre, és töltse fel az eszköz kódot:

1. Nyomja meg **F1**a(z) ** nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: eszköz > eszköz feltöltése**:

    ![IoT Workbench: Eszközök - > feltöltése](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. A VS Code fordítása sikeres és a kód feltölti az DevKit eszköz:

    ![IoT Workbench: Eszközök - > feltöltve.](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. A fejlesztői készlet eszköz újraindul, és a feltöltött kódot futtatja.

## <a name="test-the-sample"></a>A minta tesztelése

Győződjön meg arról, hogy működik-e a mintaalkalmazáshoz az DevKit eszköz feltöltött, hajtsa végre az alábbi lépéseket:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>A távoli figyelési megoldás küldött telemetriai adatok megtekintése

A mintaalkalmazás futtatásakor a DevKit eszköz telemetriai adatokat küld az érzékelők adataiból Wi-Fi keresztül a megoldásgyorsító. A telemetria megtekintése:

1. Nyissa meg a megoldás irányítópultján, és kattintson a **eszközök**.

1. Kattintson az eszköz neve, a fejlesztői készlet. a jobb oldali lapon láthatja a valós idejű DevKit származó telemetria:

    ![Érzékelőktől kapott adatok az Azure IoT Suite-ban](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>A fejlesztői készlet eszköz vezérléséhez

A távoli figyelési megoldásgyorsító lehetővé teszi, hogy az eszköz távolról vezérelheti. A mintakód látható három módszerrel valósítja meg a **metódus** szakaszban az eszköz a kiválasztásakor a **eszközök** oldalon:

![IoT DevKit módszerek](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

A fejlesztői készlet LED-ek valamelyikét színét módosításához használja a **LedColor** módszer:

1. Válassza ki az eszköz nevét eszközeinek listájából, majd kattintson a a **feladatok**:

    ![Hozzon létre egy feladatot](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. A feladatok a következő értékek használatával konfigurálja, és kattintson a **alkalmaz**:

    * Válassza a feladat: **Run metódus**
    * Metódus neve: **LedColor**
    * Feladat neve: **ChangeLedColor**

    ![Feladatbeállítások](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Néhány másodperc elteltével a RGB LED (alább a gomb A) a DevKit módosított színe:

    ![IoT DevKit red vezetett](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a további oktatóanyagokra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsító, törölje azt a kiépített megoldások lapon kiválasztja, és kattintson a megoldás törlése:

![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg [az IoT DevKit – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a következő csatornák használatával:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan lehet csatlakozni a távoli figyelési megoldásgyorsító DevKit eszköz, az alábbiakban néhány javasolt következő lépések:

* [Az Azure IoT a megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-accelerators/)
* [A felhasználói felület testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit csatlakoztatása az Azure IoT Central alkalmazáshoz](../iot-central/howto-connect-devkit.md)