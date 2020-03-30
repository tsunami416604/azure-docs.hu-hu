---
title: Az MXChip IoT DevKit csatlakoztatása az Azure IoT Hub távoli figyeléséhez
description: Ebben az oktatóanyagban megtudhatja, hogyan küldheti el az IoT DevKit AZ3166 érzékelőinek állapotát az Azure IoT Remote Monitoring megoldásgyorsítónak.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 9eab035d494892671a2451866311ca06599ec030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953725"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit csatlakoztatása az Azure IoT távfigyelési megoldásgyorsítóhoz

Ebben az oktatóanyagban megtudhatja, hogyan futtathat egy mintaalkalmazást a DevKiten az Azure IoT Remote Monitoring megoldásgyorsítónak küldött érzékelőadatok kal.

Az [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy all-in-one Arduino kompatibilis alaplap gazdag perifériákkal és érzékelőkkel. Az [Arduino Visual Studio Code bővítményével](https://aka.ms/arduino)fejleszthet. És egy növekvő [projektkatalógussal](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) rendelkezik, amely a Microsoft Azure-szolgáltatások előnyeit kihasználó Dolgok internetes (IoT) megoldásainak prototípusát irányítja.

## <a name="what-you-need"></a>Mi szükséges

Az [első lépések útmutatójának](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) befejezése:

* A DevKit csatlakoztatása Wi-Fi-hálózathoz
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha nem rendelkezik ilyen, a következő két módszer egyikével regisztrálhat:

* [Ingyenes, 30 napos próbaverziós Microsoft Azure-fiók](https://azure.microsoft.com/free/) aktiválása

* [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) igénylése MSDN- vagy Visual Studio-előfizető esetén

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Hozzon létre egy Azure IoT távfigyelési megoldásgyorsítót

1. Nyissa meg az [Azure IoT-megoldásgyorsítók webhelyét,](https://www.azureiotsolutions.com/) és kattintson **az Új megoldás létrehozása**elemre.

   ![Válassza az Azure IoT-megoldásgyorsító típusát](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Alapértelmezés szerint ez a minta létrehoz egy S2 IoT Hub létrehozása után egy IoT távoli figyelési megoldás gyorsító. Ha ezt az IoT hubot nem használja nagyszámú eszközzel, javasoljuk, hogy csökkentse azt S2-ről S1-re, és törölje az IoT remote monitoring megoldásgyorsítót, így a kapcsolódó IoT Hub is törölhető, ha már nincs rá szüksége. 

2. Válassza **a Távoli figyelés**lehetőséget.

3. Adja meg a megoldás nevét, jelöljön ki egy előfizetést és egy régiót, majd kattintson **a Megoldás létrehozása gombra.** A megoldás kiépítése eltarthat egy ideig.
  
   ![Megoldás létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. A kiépítés befejezése után kattintson az **Indítás gombra.** Néhány szimulált eszközök jönnek létre a megoldás a kiépítési folyamat során. Kattintson **eszközök,** hogy nézd meg őket.

   ![Irányítópult](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konzol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kattintson **az ESZKÖZ HOZZÁADÁSA gombra.**

6. Kattintson **az Új hozzáadása** egyéni **eszközhöz gombra.**
  
   ![Új eszköz hozzáadása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kattintson **a Saját eszközazonosító definiálása**gombra, írja be `AZ3166`a lehetőséget, majd kattintson a **Létrehozás gombra.**
  
   ![Eszköz létrehozása azonosítóval](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Jegyezze fel az **IoT Hub állomásnevét,** és kattintson a **Kész**gombra.

## <a name="open-the-remotemonitoring-sample"></a>A RemoteMonitoring minta megnyitása

1. Válassza le a DevKit-et a számítógépről, ha csatlakoztatva van.

2. Indítsa el a VS Code-ot.

3. Csatlakoztassa a DevKit-et a számítógéphez. A VS Code automatikusan felismeri a DevKit et, és megnyitja a következő oldalakat:

   * A DevKit bevezető oldala.
   * Arduino-példák: Gyakorlati minták a DevKit első lépéseihez.

4. Bontsa ki a bal oldali **ARDUINO EXAMPLES szakaszt,** keresse meg **az MXCHIP AZ3166 példák > AzureIoT című szakaszt,** és válassza a **RemoteMonitoring**lehetőséget. Megnyit egy új VS Code ablakot egy projektmappával.

   > [!NOTE]
   > Ha véletlenül bezárja az ablaktáblát, újra megnyithatja. A `Ctrl+Shift+P` parancspaletta megnyitásához használja a (macOS: `Cmd+Shift+P`) parancsot, írja be az **Arduino**parancsot, majd keresse meg és válassza az **Arduino: Examples lehetőséget.**

## <a name="provision-required-azure-services"></a>Szükséges Azure-szolgáltatások biztosítása

A megoldásablakban futtassa `Ctrl+P` a feladatot `Cmd+P`a `task cloud-provision` (macOS: ) keresztül a megadott szövegmezőbe beírással.

A VS Code terminálon egy interaktív parancssor imitáta a szükséges Azure-szolgáltatások kiépítésén keresztül.

![Azure-erőforrások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Az eszközkód összeállítása és feltöltése

1. Használja `Ctrl+P` a (macOS: `Cmd + P`) és írja be a feladat **konfigurációs-eszköz-kapcsolatát.**

2. A terminál megkérdezi, hogy szeretne-e használni egy `task cloud-provision` kapcsolati karakterláncot, amelyet a lépésből olvas be. Saját eszközkapcsolati karakterláncát is beviheti az "Új létrehozása... " gombra kattintva.

3. A terminál kéri, hogy lépjen be a konfigurációs módba. Ehhez tartsa lenyomva az A gombot, majd nyomja meg és engedje fel a reset gombot. A képernyőn megjelenik a DevKit-azonosító és a "Konfiguráció".

   ![Bemeneti kapcsolati karakterlánc](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Miután `task config-device-connection` befejezte, `F1` kattintson a VS-kód parancsok betöltéséhez, és válassza a lehetőséget. `Arduino: Upload` A VS Code megkezdi az Arduino vázlat ellenőrzését és feltöltését.
  
   ![Az Arduino vázlat ellenőrzése és feltöltése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

A DevKit újraindul, és elindítja a kód futtatását.

## <a name="test-the-project"></a>A projekt tesztelése

Amikor a mintaalkalmazás fut, a DevKit elküldi az érzékelő adatokat Wi-Fi-n keresztül az Azure IoT Remote Monitoring megoldásgyorsítónak. Az eredmény megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure IoT Remote Monitoring megoldásgyorsítóját, és kattintson **a DASHBOARD**gombra.

2. A távoli figyelési megoldás konzolon a DevKit-érzékelő állapotát fogja látni.

   ![Érzékelőadatok az Azure IoT távfigyelési megoldásgyorsítóban](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Eszközazonosító módosítása

Ha a kódba kódolt **AZ3166-ot** testreszabott eszközazonosítóra szeretné módosítani a kódban, módosítsa a [távoli figyelési példában](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23)megjelenő kódsort.

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákat tapasztal, olvassa el [az IoT fejlesztői készletének gyIK-jeit,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy a következő csatornákon keresztül forduljon hozzánk:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy DevKit-eszközt az Azure IoT Remote Monitoring megoldásgyorsítóhoz, és hogyan jelenítheti meg az érzékelő adatokat, az alábbi lépéseket ismertetheti:

* [Az Azure IoT-megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)

* [MXChip IoT DevKit-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](/azure/iot-central/core/howto-connect-devkit)

* [IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/) 
