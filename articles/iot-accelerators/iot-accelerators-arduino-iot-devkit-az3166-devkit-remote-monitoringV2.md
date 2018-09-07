---
title: A felhőbe – IoT DevKit IoT DevKit AZ3166 kapcsolódni a távoli figyelési IoT-megoldásgyorsítók |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan érzékelők állapotát küldjön az IoT DevKit AZ3166 a távoli figyelési IoT megoldásgyorsító a monitorozási és a Vizualizáció.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: d3175290c1a7fca5e35f4438392f29324868f1a3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054916"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit AZ3166 csatlakozni az IoT távoli figyelési megoldásgyorsító


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebben az oktatóanyagban elsajátíthatja, hogyan futtathat egy mintaalkalmazást az érzékelő adatokat küldeni a megoldásgyorsító DevKit.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Arduino a Visual Studio Code-bővítmény](https://aka.ms/arduino). És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) segítséget nyújtanak prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Olvassa el a [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) és **fejezze be a következő szakaszok**:

* Készítse elő a hardvert
* Wi-Fi konfigurálása
* A fejlesztői készlet használatának megkezdése
* A fejlesztési környezet előkészítése


## <a name="open-the-remotemonitoring-sample-in-vs-code"></a>Nyissa meg a RemoteMonitoring minta a VS Code-ban

1. Az MXChip fejlesztői készlet leválasztása a számítógépre, ha csatlakoztatva van.

2. Indítsa el a VS Code.

3. Az MXChip fejlesztői készlet csatlakoztatása a számítógéphez.

4. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

 5. Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **RemoteMonitoringv2**. Egy új VS Code-ablak nyílik, a projektmappa.

  > [!NOTE]
  > Ha Ön nem látható **példák az MXCHIP**, használja `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), nyissa meg a parancskatalógus és a típus **Arduino tábla Manager**. Válassza ki, majd keresse meg **AZ3166** az üzenőfal-kezelő belül. Majd tudnia kell a példákat, és ismételje meg a fenti 5. lépés.

  ![Távoli figyelés projekt megnyitása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)

  > [!NOTE]
  > Zárja be a panelt az MSDN-előfizetőknek, megnyithatja azt. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="build-and-upload-the-device-code-to-your-mxchip"></a>Hozhat létre és tölt fel az eszköz kódot az MXChip

1. Használat `Ctrl+P` (macOS: `Cmd + P`), és írja be **tevékenység-config-eszközkapcsolat**.

  ![Válassza ki az Azure-előfizetés és az IoT hubhoz](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. A terminálban rákérdez, hogy szeretné-e használni az IoT-eszközök kapcsolati karakterláncot szeretne használni. Válassza ki *létrehozása új*, és illessze be azt.

  ![illessze be a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. A terminálban néha kéri, hogy adja meg a konfigurációs mód. Ehhez tartsa lenyomva a **gombra A**, majd továbbítsa és kiadása a **alaphelyzetbe állítása gomb** és engedje a gomb rögzíti. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

  ![Eszköz DevKit képernyő](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > A kapcsolati karakterláncot a vágólapra kell menteni, ha követte az oktatóanyag utolsó szakasza. Ha nem, akkor kell az Azure Portalon, és keresse meg az IoT Hub, a távoli figyelési erőforráscsoport. Itt láthatja az IoT Hub csatlakoztatott eszközök és az eszköz kapcsolati karakterlánc másolása.

  ![Keresse meg a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)

  Most már sikeresen csatlakoztatva, és ellenőrizte az MXChip eszközt az IoT Hub. Tekintse meg az "Azure IoT Hub-eszközök" a VS Code szakaszában az új fizikai eszközt, hogy le kell töltenie a [Azure IoT-eszközkészlet bővítmény.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 

  Most megjelenik az új fizikai eszköz az "Azure IoT Hub-eszközök" a VS Code szakaszban:

  ![Figyelje meg, hogy az új IoT Hub-eszköz](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

4. Végül a RemoteMonitoringV2.ino kód feltölti az alakzatot, megkezdheti az IoT-megoldásgyorsítók adatok küldését az MxChip. Használat `Ctrl + Shift + P` (macOS: `Cmd + Shift + P`), és írja be **Arduino feltöltése**. A VS Code ezután a kód az MXChip alakzatot a feltöltés elindításához, és értesítést küld, amikor végzett. 

## <a name="test-the-project"></a>A projekt tesztelése

A mintaalkalmazás futtatásakor a MXChip fejlesztői készlet keresztül Wi-Fi érzékelői adatokat küld az IoT-megoldásgyorsítók. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az IoT-megoldásgyorsítók, és kattintson a **IRÁNYÍTÓPULT**.

2. A megoldás gyorsító IoT konzolon látni fogja az MXChip fejlesztői készlet érzékelő állapota. 

![Érzékelőktől kapott adatok valós IoT-megoldásgyorsítók](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Az érzékelő neve (AZ3166) kattintva a lap jobb oldalán található, ahol megtekintheti a MXChip érzékelők diagram valós idejű irányítópulton nyílik meg.


## <a name="send-a-c2d-message"></a>C2D üzenet küldése
Távoli figyelés v2 lehetővé teszi, hogy az eszköz távoli metódus meghívása.
MX lapka példakód tesz közzé az érzékelő kiválasztásakor láthatja a metódus szakaszban három módszert.

![Metódusok MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Módosíthatja a MX lapka LED-ek a metódussal "LedColor" egyik színét. Ezt, jelölje be a jelölőnégyzetet az eszköz és kattintson az ütemezés gombra. 

![Metódusok MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Válassza ki a legördülő listában, ahol mindazokat a módszereket jelenik meg, egy nevet, és alkalmazni a ChangeColor meghívott metódus.

![Legördülő lista MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Néhány másodpercen belül, a fizikai MX lapkával kell a RGB LED színének módosítása (alább a gomb)

![LED MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot, a következő csatornákon:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan lehet egy DevKit eszköz csatlakoztatása az IoT-megoldásgyorsítók és jelenítheti meg az érzékelő adatokat, az alábbiakban a javasolt következő lépések:

* [IoT a megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Az MXChip IoT DevKit eszköz csatlakoztatása a Microsoft IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/)
