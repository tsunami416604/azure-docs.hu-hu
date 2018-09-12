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
ms.openlocfilehash: 35ef6ef02e5ae8a4b9231121615f44e0dc00ad15
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378737"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit AZ3166 csatlakozni az IoT távoli figyelési megoldásgyorsító

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebben az oktatóanyagban elsajátíthatja, hogyan futtathat egy mintaalkalmazást az érzékelő adatokat küldeni a megoldásgyorsító DevKit.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) Visual Studio Code-ban. És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) segítséget nyújtanak prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Olvassa el a [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) és **fejezze be a következő szakaszok**:

* Készítse elő a hardvert
* Wi-Fi konfigurálása
* A fejlesztői készlet használatának megkezdése
* A fejlesztési környezet előkészítése


## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Hozzon létre egy Azure IoT távoli figyelési megoldásgyorsító

Ebben az oktatóanyagban létrehozott AZ3166 eszköz adatokat küld a távoli figyelési megoldásgyorsító egy példányát. Ha, még nem még egy példányt az Azure-fiókjában, kövesse a [rövid](https://docs.microsoft.com/azure/iot-accelerators/quickstart-remote-monitoring-deploy) ehhez az útmutató.

A távoli figyelési megoldás az üzembe helyezést követően nyissa meg a megoldás irányítópultján.

![A megoldás irányítópultja](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard-info.png)

## <a name="add-a-device-to-the-remote-monitoring-solution"></a>Hozzáad egy eszközt a távoli figyelési megoldás

1. Az irányítópulton, lépjen a **eszközök** szakaszt, és kattintson a **új eszköz**.
   ![Új eszköz hozzáadása](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device.png)

2. Az eszköz konfigurálása az alábbi információk segítségével, és kattintson a **alkalmaz**.
  * Eszköz típusa: **fizikai**
  * Eszközazonosító: **AZ3166**
  * Hitelesítés típusa: **szimmetrikus kulcs**
  * Hitelesítési kulcs: **kulcsok automatikus létrehozása**
  
  ![Új eszköz képernyő hozzáadása](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-new-device-form.png)

3. Az új eszköz létrehozása után másolja a **kapcsolati karakterlánc – elsődleges kulcs**. Ez az, hogy az eszközt, hogy az Azure IoT Hub alatt létrehozott.
  
  ![Eszköz kapcsolati karakterláncát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-new-device-connstring.png)

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Nyissa meg a távoli figyelési minta a VS Code-ban

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Kattintson a `F1` nyissa meg a parancskatalógus, írja be és válassza ki a **IoT Workbench: Példák**. Válassza ki **IoT DevKit** , tábla.

1. Keresés **távoli figyelési** kattintson **nyílt minta**. Egy új VS Code-ablak azt a projektmappában nyílik.
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

1. Kattintson a lap jobb oldalán található, ahol megtekintheti az érzékelő állapot DevKit a valós idejű irányítópulton megnyílik az eszköz nevét (AZ3166).
  ![Érzékelőktől kapott adatok az Azure IoT Suite-ban](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>C2D üzenet küldése

Távoli figyelés megoldás lehetővé teszi, hogy az eszköz távoli metódus meghívása. A sxample kódot közzéteszi az is látható három módszert a **metódus** szakaszt, amikor az érzékelő van kiválasztva.

![IoT DevKit módszerek](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Ossza meg velünk próbálja ki az egyik módszerrel "LedColor" DevKit LED-ek színének módosítása.

1. Válassza ki a **AZ3166** az eszközök listáját, majd kattintson a a **feladatok**.

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