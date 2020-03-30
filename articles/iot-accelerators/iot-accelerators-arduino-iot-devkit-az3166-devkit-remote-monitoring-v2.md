---
title: Az IoT DevKit csatlakoztatása távfigyelési megoldáshoz - Azure | Microsoft dokumentumok
description: Ebben az útmutatóútmutatóban megtudhatja, hogyan küldhet telemetriát az IoT DevKit AZ3166-os eszköz érzékelőiről a távoli figyelési megoldásgyorsítóba figyelésése és vizualizációja érdekében.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888867"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>IoT DevKit-eszköz csatlakoztatása a távfigyelési megoldás gyorsítójához

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az útmutató bemutatja, hogyan futtathat egy mintaalkalmazást az IoT DevKit-eszközön. A mintakód telemetriát küld a DevKit-eszköz érzékelőitől a megoldásgyorsítónak.

Az [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy all-in-one Arduino kompatibilis alaplap gazdag perifériákkal és érzékelőkkel. Az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy [az Azure IoT Tools](https://aka.ms/azure-iot-tools) bővítménycsomag gal fejleszthet a Visual Studio-kódban. A [projektkatalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) mintaalkalmazásokat tartalmaz az IoT-megoldások prototípusának prototípusához.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek végrehajtásához először hajtsa végre a következő feladatokat:

* Készítse elő a DevKit-et az [IoT DevKit AZ3166 csatlakoztatása az Azure IoT Hubhoz a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)című lépések végrehajtásával.

## <a name="open-sample-project"></a>Mintaprojekt megnyitása

A távoli figyelési minta megnyitása a VS-kódban:

1. Győződjön meg arról, hogy az IoT DevKit nincs a számítógépén. Először indítsa el a VS-kódot, majd csatlakoztassa a DevKit-et a számítógéphez.

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT Device Workbench: Open Examples...** lehetőséget. Ezután válassza **az IoT DevKit** fórumon.

1. Keresse meg **a távoli figyelést,** és kattintson **a Minta megnyitása gombra.** Megnyílik egy új VS-kód ablak a projekt mappájával:

   ![IoT-munkaterület, válassza a Távoli figyelés példát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Az eszköz konfigurálása

Az IoT Hub-eszköz kapcsolati karakterláncának konfigurálása a DevKit-eszközön:

1. Váltson az IoT DevKit **konfigurációs módba:**

    * Tartsa lenyomva az **A**gombot.
    * Nyomja meg és engedje fel a **Reset** gombot.

1. A képernyőn megjelenik a DevKit-azonosító és `Configuration`a .

    ![IoT DevKit konfigurációs mód](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Nyomja meg az **F1 billentyűt** a parancspaletta megnyitásához, írja be és válassza az **Azure IoT Device Workbench: Device Settings... > Config Device Connection String**lehetőséget.

1. Illessze be a korábban másolt kapcsolati karakterláncot, és nyomja le az **Enter billentyűt** az eszköz konfigurálásához.

## <a name="build-the-code"></a>A kód létrehozása

Az eszközkód létrehozása és feltöltése:

1. Nyomja `F1` meg a parancspaletta megnyitásához írja be és válassza az **Azure IoT Device Workbench: Device Code feltöltése**lehetőséget:

1. A VS Code lefordítja és feltölti a kódot a DevKit-eszközre:

    ![IoT-munkaterület: Eszköz – > feltöltve](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. A DevKit-eszköz újraindul, és futtatja a feltöltött kódot.

## <a name="test-the-sample"></a>Tesztelje a mintát

Annak ellenőrzéséhez, hogy a DevKit-eszközre feltöltött mintaalkalmazás működik-e, hajtsa végre az alábbi lépéseket:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>A távoli figyelési megoldásnak küldött telemetriai adatok megtekintése

Amikor a mintaalkalmazás fut, a DevKit-eszköz telemetriai adatokat küld az érzékelők adatait Wi-Fi-n keresztül a megoldás gyorsító. A telemetriai adatok megtekintése:

1. Nyissa meg a megoldás irányítópultját, és kattintson **az Eszközkezelő gombra.**

1. Kattintson a DevKit-eszköz eszköznevére. a jobb oldali lapon valós időben láthatja a Fejlesztői készlet telemetriáját:

    ![Érzékelőadatok az Azure IoT Suite-ban](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>A DevKit-eszköz vezérlése

A Távfigyelés megoldásgyorsító lehetővé teszi az eszköz távoli vezérlését. A mintakód három módszert valósít meg, amelyek a **Metódus** szakaszban láthatók, amikor kiválasztja az eszközt az **Eszközkezelő** lapon:

![IoT DevKit-metódusok](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

A DevKit LED-ek színének módosításához használja a **LedColor** módszert:

1. Válassza ki az eszköz nevét az eszközlistából, és kattintson a **Feladatok**:

    ![Feladat létrehozása](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurálja a feladatokat az alábbi értékek használatával, és kattintson az **Alkalmaz gombra:**

   * Feladat kiválasztása: **Metódus futtatása**
   * Módszer neve: **LedColor**
   * Feladat neve: **ChangeLedColor**

     ![Feladatbeállítások](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Néhány másodperc múlva megváltozik az RGB LED színe (az A gomb alatt) a DevKit-en:

    ![IoT DevKit piros led](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a további oktatóanyagokra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a Kiépített megoldások lapról. Ehhez jelölje ki, majd kattintson a Megoldás törlése gombra:

![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha bármilyen problémába ütközik, olvassa el [az IoT DevKit gyakori kérdéseket,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy a következő csatornákon keresztül forduljon hozzánk:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy DevKit-eszközt a távoli figyelési megoldás gyorsítójához, az alábbi lépéseket javasolta:

* [Az Azure IoT-megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-accelerators/)
* [A felhasználói felület testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [Az IoT DevKit csatlakoztatása az Azure IoT Central alkalmazáshoz](../iot-central/core/howto-connect-devkit.md)