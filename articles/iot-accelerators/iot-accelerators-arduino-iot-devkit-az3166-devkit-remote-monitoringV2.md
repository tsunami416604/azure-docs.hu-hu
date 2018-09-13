---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 kapcsolódni a távoli figyelési IoT-megoldásgyorsítók |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan érzékelők állapotát küldjön az IoT DevKit AZ3166 a távoli figyelési IoT megoldásgyorsító a monitorozási és a Vizualizáció.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720582"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit AZ3166 csatlakozni az IoT távoli figyelési megoldásgyorsító

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Megtudhatja, hogyan futtathat egy mintaalkalmazást az érzékelő adatokat küldeni a megoldásgyorsító IoT DevKit.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) Visual Studio Code-ban. És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) segítséget nyújtanak prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Olvassa el a [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) és **fejezze be a következő szakaszok**:

* Készítse elő a hardvert
* Wi-Fi konfigurálása
* A fejlesztői készlet használatának megkezdése
* A fejlesztési környezet előkészítése

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Nyissa meg a távoli figyelési minta a VS Code-ban

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

2. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: Példák**. Válassza ki **IoT DevKit** , tábla.

3. Keresés **távoli figyelési** kattintson **nyílt minta**. Egy új VS Code-ablak azt a projektmappában nyílik.
  ![IoT Workbench, válassza a távoli figyelési példa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Az IoT Hub eszköz kapcsolati karakterlánc konfigurálása

1. Az IoT DevKit való váltás **konfigurációs mód**. Ehhez tegye a következőket:
   * Tartsa lenyomva a gomb **A**.
   * Leküldéses és kiadása a **alaphelyzetbe** gombra.

2. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".
   
  ![IoT DevKit konfigurációs mód](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: eszköz > konfigurációs beállítások**.

4. Illessze be a kapcsolati karakterláncot az imént másolt kattintson `Enter` konfigurálásához.

## <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

1. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: eszköz > eszköz feltöltése**.
  ![IoT Workbench: Eszközök - > feltöltése](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. A VS Code majd elindítja a kódja lefordításának és a kódot a DevKit való feltöltését.
  ![IoT Workbench: Eszközök - > feltöltve.](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

## <a name="test-the-project"></a>A projekt tesztelése

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>A távoli figyelési megoldás küldött telemetriai adatok megtekintése

A mintaalkalmazás futtatásakor a DevKit Wi-Fi keresztül érzékelői adatokat küld a távoli figyelési megoldáshoz. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a megoldás irányítópultján, és kattintson a **eszközök**.

2. Kattintson az eszköz nevére, a jobb oldali lapon valós időben DevKit érzékelő állapota látható.
  ![Érzékelőktől kapott adatok az Azure IoT Suite-ban](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>C2D üzenet küldése

Távoli figyelés megoldás lehetővé teszi, hogy az eszköz távoli metódus meghívása. A sxample kódot közzéteszi az is látható három módszert a **metódus** szakaszt, amikor az érzékelő van kiválasztva.

![IoT DevKit módszerek](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Ossza meg velünk próbálja ki az egyik módszerrel "LedColor" DevKit LED-ek színének módosítása.

1. Válassza ki az eszköz nevét eszközeinek listájából, majd kattintson a a **feladatok**.

  ![Hozzon létre egy feladatot](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. A feladatok konfigurálása az alábbi, és kattintson a **alkalmaz**.
  * Válassza a feladat: **Run metódus**
  * Metódus neve: **LedColor**
  * Feladat neve: **ChangeLedColor**
  
  ![Feladatbeállítások](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Néhány másodpercen belül a fejlesztői készlet módosítani kell a RGB LED (alább a gomb A) színét.

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

Most, hogy megtanulhatta, hogyan jelenítheti meg az érzékelő adatokat és a egy DevKit eszköz csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító, Íme a javasolt következő lépések:

* [Az Azure IoT a megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [A felhasználói felület testreszabása](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit csatlakoztatása az Azure IoT Central alkalmazáshoz](../iot-central/howto-connect-devkit.md)