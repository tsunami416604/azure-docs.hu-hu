---
title: IoT DevKit a felhőbe – IoT MXChip fejlesztői készlet csatlakoztatása Azure IoT hubra |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldhet az IoT DevKit AZ3166 érzékelők állapotát az Azure IoT távoli figyelési megoldásgyorsító.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 954a57bb9253e4f18d61c3a699b6e721568da292
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157409"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Az MXChip IoT DevKit csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító

Ebben az oktatóanyagban elsajátíthatja, hogyan futtathat egy mintaalkalmazást az érzékelő adatokat küldeni az Azure IoT távoli figyelési megoldásgyorsító DevKit.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Arduino a Visual Studio Code-bővítmény](https://aka.ms/arduino). És az egyre növekvő együtt származik [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) segítséget nyújtanak prototípus Internet of Things (IoT) megoldás, amely a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Fejezze be a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) való:

* A fejlesztői készlet Wi-Fi csatlakozik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha nem rendelkezik egy, a két módszer egyikével keresztül regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próbafiókra Microsoft Azure](https://azure.microsoft.com/free/)

* Jogcím a [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio-előfizetők

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Hozzon létre egy Azure IoT távoli figyelési megoldásgyorsító

1. Lépjen a [Azure IoT-megoldás megoldásgyorsítók hely](https://www.azureiotsolutions.com/) kattintson **hozzon létre egy új megoldás**.

   ![Válassza ki az Azure IoT-megoldás gyorsító típusa](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Alapértelmezés szerint ez a minta akkor hoz létre S2 IoT Hub, egy IoT távoli figyelési megoldásgyorsító létrehozása után. Ha az IoT-központ nem használatos az eszközök nagy mennyiségben, erősen ajánlott alacsonyabbra S2-ről S1-re, és az IoT távoli figyelési megoldásgyorsító törölni, így a kapcsolódó az IoT Hub is törölhetők, amikor már nincs szüksége. 

2. Válassza ki **távoli megfigyelés**.

3. Adja meg a megoldás nevét, válassza ki az előfizetést és a egy régiót, és kattintson **megoldás létrehozása**. A megoldás eltarthat egy ideig, ki kell építeni.
  
   ![Megoldás létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Kiépítés befejeztét követően kattintson **indítsa el a**. A megoldás egyes szimulált eszközök a kiépítési folyamat során jön létre. Kattintson a **eszközök** , nézze meg őket.

   ![Irányítópult](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konzol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kattintson a **eszköz hozzáadása**.

6. Kattintson a **hozzáadhat új** a **egyéni eszköz**.
  
   ![Új eszköz hozzáadása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kattintson a **Meghatározom a saját Eszközazonosítómat**, adja meg `AZ3166`, és kattintson a **létrehozás**.
  
   ![Azonosító létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Jegyezze fel a **IoT Hub-eszköznév**, és kattintson a **kész**.

## <a name="open-the-remotemonitoring-sample"></a>Nyissa meg a RemoteMonitoring minta

1. Ha csatlakoztatva van, válassza le a DevKit a számítógépről.

2. Indítsa el a VS Code.

3. A fejlesztői készlet csatlakoztatása a számítógéphez. A VS Code automatikusan észleli a DevKit, és megnyílik a következő lapokon:

  * A fejlesztői készlet bevezetés oldalt.
  * Arduino példák: Gyakorlati példák DevKit használatának megkezdéséhez.

4. Bontsa ki a bal oldali **ARDUINO példák** területén keresse meg a **példák az MXCHIP AZ3166 > AzureIoT**, és válassza ki **RemoteMonitoring**. Egy új VS Code-ablak nyílik, a projektmappa.

   > [!NOTE]
   > Zárja be a panelt az MSDN-előfizetőknek, megnyithatja azt. Használat `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) a parancskatalógus megnyitásához, írja be a **Arduino**, keresse meg és válassza a **Arduino: Példák**.

## <a name="provision-required-azure-services"></a>Szükséges Azure-szolgáltatások üzembe helyezése

A megoldás ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision` a megadott szövegmezőben.

A VS Code terminálban egy interaktív parancssori végigvezeti a szükséges Azure-szolgáltatások kiépítése.

![Az Azure-erőforrások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Hozhat létre és töltse fel az eszköz kódot

1. Használat `Ctrl+P` (macOS: `Cmd + P`), és írja be **tevékenység-config-eszközkapcsolat**.

2. A terminálban megkérdezi, hogy szeretné-e, amely lekéri a kapcsolati karakterlánc használata a `task cloud-provision` . lépés. "Új létrehozása..." gombra kattintva is sikerült megadhatja a saját eszközök kapcsolati karakterláncot

3. A terminálban kéri, hogy adja meg a konfigurációs mód. Ehhez A gombot lenyomva, majd küldje le, és engedje a Visszaállítás gombra. A képernyőn megjelenik a fejlesztői készlet azonosítója és a "Konfiguráció".

   ![A bemeneti kapcsolati karakterlánc](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Miután `task config-device-connection` futtatása befejeződik, kattintson a `F1` betölteni a VS Code parancsokat, és válassza ki `Arduino: Upload`. A VS Code annak ellenőrzésére, és töltse fel az Arduino rajz elindul.
  
   ![Ellenőrzés és feltöltése az Arduino áttekintése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

A fejlesztői készlet újraindul, és elindítja a kód futtatása.

## <a name="test-the-project"></a>A projekt tesztelése

A mintaalkalmazás futtatásakor a DevKit Wi-Fi kapcsolaton érzékelői adatokat küld az Azure IoT távoli figyelési megoldásgyorsító. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure IoT távoli figyelési megoldásgyorsító, és kattintson a **IRÁNYÍTÓPULT**.

2. A távoli figyelési megoldás konzolon láthatja a DevKit érzékelő állapotát.

   ![Az Azure IoT távoli figyelési megoldásgyorsító érzékelőktől kapott adatok](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Változás-Eszközazonosító

Ha meg szeretné változtatni a szoftveresen kötött **AZ3166** egy egyéni Eszközazonosító a kódban, módosítsa a megjelenő kódsort a [távoli monitorozási példa](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg [az IoT fejlesztői készlet – gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy fel velünk a kapcsolatot a következő csatornák használatával:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan jelenítheti meg az érzékelő adatokat és a egy DevKit eszköz csatlakoztatása az Azure IoT távoli figyelési megoldásgyorsító, Íme a javasolt következő lépések:

* [Az Azure IoT a megoldásgyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)

* [Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/) 
