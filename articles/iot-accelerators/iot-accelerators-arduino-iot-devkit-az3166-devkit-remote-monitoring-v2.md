---
title: IoT-fejlesztői készlet összekötése távoli figyelési megoldással – Azure | Microsoft Docs
description: Ebben a útmutatóban megtudhatja, hogyan küldhet telemetria a IoT fejlesztői készlet AZ3166 eszközről a távoli figyelési megoldáshoz a figyeléshez és a vizualizációhoz.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73888867"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>IoT fejlesztői készlet-eszköz csatlakoztatása a távoli figyelési megoldáshoz

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez a útmutató bemutatja, hogyan futtathat egy minta alkalmazást a IoT fejlesztői készlet-eszközön. A mintakód a fejlesztői készlet-eszköz érzékelők telemetria küldi a megoldás-gyorsító eszközt.

A [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) egy all-in-One Arduino-kompatibilis tábla, amely gazdag perifériákkal és érzékelőkkel rendelkezik. A Visual Studio Code-ban az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy az [Azure IoT Tools](https://aka.ms/azure-iot-tools) Extension Pack használatával fejlesztheti azt. A [projects Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a IoT-megoldások prototípusát segítő példákat tartalmaz.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek elvégzéséhez először hajtsa végre a következő feladatokat:

* Készítse elő a fejlesztői készlet a [IoT fejlesztői készlet AZ3166 és a felhőben lévő Azure IoT hub összekapcsolásához](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)szükséges lépéseket követve.

## <a name="open-sample-project"></a>Minta projekt megnyitása

A távoli figyelési minta megnyitása a VS Code-ban:

1. Győződjön meg arról, hogy a IoT fejlesztői készlet nem a számítógépe. Először indítsa el a VS Code-ot, majd kapcsolja össze a fejlesztői készlet a számítógéppel.

1. Kattintson a `F1` parancs palettájának megnyitásához, írja be a parancsot, majd válassza az **Azure IoT Device Workbench: Megnyitás példák..**. lehetőséget. Ezután válassza a **IoT fejlesztői készlet** lehetőséget.

1. Keresse meg a **távoli figyelést** , és kattintson a **minta megnyitása**lehetőségre. Megnyílik egy új VS Code ablak, amely a projekt mappáját mutatja:

   ![IoT Workbench, válassza a távoli figyelés példát](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Az eszköz konfigurálása

IoT Hub eszköz kapcsolódási karakterláncának konfigurálása a fejlesztői készlet-eszközön:

1. Állítsa be a IoT fejlesztői készlet **konfigurációs módba**:

    * Tartsa lenyomva **A**gombot.
    * Az **Alaphelyzetbe állítás** gomb leküldése és felszabadítása

1. A képernyőn megjelenik a fejlesztői készlet azonosítója és a `Configuration` .

    ![IoT fejlesztői készlet-konfigurációs mód](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Nyomja le az **F1** billentyűt a parancs palettájának megnyitásához, írja be a parancsot, majd válassza az **Azure IoT Device Workbench: eszközbeállítások konfigurálása... > konfigurációs eszköz kapcsolódási karakterláncát**.

1. Illessze be a korábban átmásolt kapcsolódási karakterláncot, majd nyomja le az **ENTER** billentyűt az eszköz konfigurálásához.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz kódjának létrehozása és feltöltése:

1. Nyomja meg `F1` a parancs palettájának megnyitásához írja be a parancsot, majd válassza az **Azure IoT Device Workbench: az eszköz feltöltési kódja**:

1. A VS Code lefordítja és feltölti a kódot a fejlesztői készlet-eszközre:

    ![IoT Workbench: eszköz – > feltöltve](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. A fejlesztői készlet-eszköz újraindul, és futtatja a feltöltött kódot.

## <a name="test-the-sample"></a>A minta tesztelése

A következő lépések végrehajtásával ellenőrizheti, hogy a fejlesztői készlet-eszközre feltöltött minta alkalmazás működik-e:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>A távoli figyelési megoldásnak eljuttatott telemetria megtekintése

Ha a minta alkalmazás fut, a fejlesztői készlet-eszköz Wi-Fi-kapcsolaton keresztül elküldi a telemetria a megoldás-gyorsító felé. A telemetria megtekintéséhez:

1. Nyissa meg a megoldás irányítópultját, és kattintson a **Device Explorer**elemre.

1. Kattintson a fejlesztői készlet eszköz nevére. a jobb oldali lapon valós időben láthatja a telemetria a fejlesztői készlet:

    ![Szenzor-adatAzure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>A fejlesztői készlet eszköz vezérlése

A távoli figyelési megoldás gyorsítása lehetővé teszi az eszköz távoli vezérlését. A mintakód három olyan metódust valósít meg, amelyet a **metódus** szakaszban láthat, amikor kiválasztja az eszközt a **Device Explorer** lapon:

![IoT fejlesztői készlet metódusok](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

A fejlesztői készlet-LED-ek egyikének színének módosításához használja a **LedColor** metódust:

1. Válassza ki az eszköz nevét az eszközök listájából, majd kattintson a **feladatokra**:

    ![Feladatok létrehozása](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurálja a feladatokat a következő értékekkel, és kattintson az **alkalmaz**gombra:

   * Válassza ki a feladatot: **metódus futtatása**
   * Metódus neve: **LedColor**
   * Feladatok neve: **ChangeLedColor**

     ![Feladatbeállítások](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Néhány másodperc elteltével az RGB LED színe (az A gomb alatt) változik a fejlesztői készlet:

    ![IoT fejlesztői készlet piros LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha tovább kíván lépni a további oktatóanyagokra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a Kiépített megoldások lapról. Ehhez jelölje ki, majd kattintson a Megoldás törlése gombra:

![Megoldás törlése](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha bármilyen probléma merül fel, tekintse meg [a IoT fejlesztői készlet gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) című témakört, vagy forduljon hozzánk a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan csatlakoztatható egy fejlesztői készlet-eszköz a távoli figyelési megoldás-gyorsító szolgáltatáshoz, néhány javasolt lépés:

* [Az Azure IoT megoldás-gyorsítók áttekintése](https://docs.microsoft.com/azure/iot-accelerators/)
* [A felhasználói felület testreszabása](iot-accelerators-remote-monitoring-customize.md)
* [IoT-fejlesztői készlet összekötése az Azure IoT Central-alkalmazással](../iot-central/core/howto-connect-devkit.md)