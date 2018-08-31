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
ms.openlocfilehash: 92ce85a3cc94702468a13348d3a41667498c68f5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43187391"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit AZ3166 csatlakozni az IoT távoli figyelési megoldásgyorsító


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebben az oktatóanyagban elsajátíthatja, hogyan futtathat egy mintaalkalmazást az érzékelő adatokat küldeni a megoldásgyorsító DevKit.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Arduino a Visual Studio Code-bővítmény](https://aka.ms/arduino). És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) segítséget nyújtanak prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) való:

* A fejlesztői készlet Wi-Fi csatlakozik
* A fejlesztési környezet előkészítése


## <a name="open-the-remotemonitoring-sample"></a>Nyissa meg a RemoteMonitoring minta

1. Ha csatlakoztatva van, válassza le a DevKit a számítógépről.

2. Indítsa el a VS Code.

3. A fejlesztői készlet csatlakoztatása a számítógéphez. A VS Code automatikusan észleli a DevKit, és megnyílik a következő lapokon:
  * A fejlesztői készlet bevezetés oldalt.
  * Arduino példák: Gyakorlati minták DevKit használatának megkezdéséhez.

4. Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **RemoteMonitoringv2**. Egy új VS Code-ablak nyílik, a projektmappa.

  ![Távoli figyelés projekt megnyitása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Zárja be a panelt az MSDN-előfizetőknek, megnyithatja azt. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="add-a-new-physical-device"></a>Fizikai eszköz hozzáadása

A portálon lépjen a **eszközök** szakaszt, és kattintson a a **+ új eszköz** gombra. 

![Új eszköz hozzáadása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

A *az új eszköz űrlap* ki kell tölteni.
1. Kattintson a **fizikai** a a *eszköztípus* szakaszban.
2. Saját Eszközazonosító megadása (például *MXChip* vagy *AZ3166*).
3. Válasszon **kulcsok automatikus létrehozása** a a *hitelesítési kulcs* szakaszban.
4. Kattintson a *alkalmaz* gombra.

![Új eszköz képernyő hozzáadása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Várjon, amíg a portál befejezi az új eszköz kiépítését.

![Új eszköz kiépítése ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Majd a konfiguráció az új eszköz jelenik meg.
Másolás a **kapcsolati karakterlánc** jön létre.

![Eszköz kapcsolati karakterláncát](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Ez a kapcsolati karakterlánc a következő szakaszban használható.





## <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

Lépjen vissza a Visual Studio Code: 

1. Használat `Ctrl+P` (macOS: `Cmd + P`), és írja be **tevékenység-config-eszközkapcsolat**.

  ![Válassza ki az Azure-előfizetés és az IoT hubhoz](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. A terminálban rákérdez, hogy szeretné-e használni az IoT-eszközök kapcsolati karakterláncot szeretne használni. Válassza ki *létrehozása új*, és illessze be azt.

  ![illessze be a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. A terminálban néha kéri, hogy adja meg a konfigurációs mód. Ehhez A gombot lenyomva, majd leküldéses és a Visszaállítás gombra kiadási és engedje a gomb A. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

  ![Eszköz DevKit képernyő](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > A kapcsolati karakterláncot a vágólapra kell menteni, ha követte az oktatóanyag utolsó szakasza. Ha nem, akkor kell az Azure Portalon, és keresse meg az IoT Hub, a távoli figyelési erőforráscsoport. Itt láthatja az IoT Hub csatlakoztatott eszközök és az eszköz kapcsolati karakterlánc másolása.

  ![Keresse meg a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Most megjelenik az új fizikai eszköz az "Azure IoT Hub-eszközök" a VS Code szakaszban:

![Figyelje meg, hogy az új IoT Hub-eszköz](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>A projekt tesztelése

A mintaalkalmazás futtatásakor a DevKit Wi-Fi keresztül érzékelői adatokat küld az IoT-megoldásgyorsítók. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az IoT-megoldásgyorsítók, és kattintson a **IRÁNYÍTÓPULT**.

2. Az IoT gyorsító megoldáskonzol a fejlesztői készlet érzékelő állapot jelennek meg. 

![Érzékelőktől kapott adatok valós IoT-megoldásgyorsítók](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Az érzékelő neve (AZ3166) kattintva a lap jobb oldalán található, ahol megtekintheti az MX-lapkához érzékelők diagram valós idejű irányítópulton nyílik meg.


## <a name="send-a-c2d-message"></a>C2D üzenet küldése
Távoli figyelés v2 lehetővé teszi, hogy az eszköz távoli metódus meghívása.
MX lapka példakód tesz közzé az érzékelő kiválasztásakor láthatja a metódus szakaszban három módszert.

![Metódusok MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Módosíthatja a metódussal "LedColor" MX-lapkához LED-ek valamelyikét színét. Ezt, jelölje be a jelölőnégyzetet az eszköz és kattintson az ütemezés gombra. 

![Metódusok MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Válassza ki a legördülő listában, ahol mindazokat a módszereket jelenik meg, egy nevet, és alkalmazni a ChangeColor meghívott metódus.

![Legördülő lista MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Néhány másodpercen belül, a fizikai MX lapkával kell a vezetett RGB színének módosítása (alább a gomb)

![Következtében MX-lapkával](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot, a következő csatornákon:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan lehet egy DevKit eszköz csatlakoztatása az IoT-megoldásgyorsítók és jelenítheti meg az érzékelő adatokat, az alábbiakban a javasolt következő lépések:

* [IoT a megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Az MXChip IoT DevKit eszköz csatlakoztatása a Microsoft IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/)
