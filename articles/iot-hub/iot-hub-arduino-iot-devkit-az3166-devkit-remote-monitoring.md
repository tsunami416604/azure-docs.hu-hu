---
title: "A felhőben az IoT-DevKit: IoT DevKit AZ3166 csatlakozzon az Azure IoT Hub |} Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan küldhet az érzékelők állapotát az IoT DevKit AZ3166 Azure IoT Suite figyelési és a képi megjelenítés."
services: iot-hub
documentationcenter: 
author: liydu
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: abaad6514b24bd4bcc923d87a7b721cc80648c47
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-suite-for-remote-monitoring"></a>Azure IoT Suite IoT DevKit AZ3166 csatlakoztatni a távoli figyelése

Ebben az oktatóanyagban elsajátíthatja egy mintaalkalmazást futtatásáról a DevKit érzékelő adatokat küldeni az Azure IoT Suite.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, ezek két módszer használatával regisztrálhatja:

* Aktiválja a [ingyenes 30 napos próbafiókot Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html)
* Jogcím a [Azure-kreditjeinek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) MSDN vagy a Visual Studio előfizetői esetén

## <a name="create-an-azure-iot-suite"></a>Hozzon létre egy Azure IoT Suite

1. Nyissa meg a [Azure IoT Suite hely](https://www.azureiotsuite.com/) kattintson **hozzon létre egy új megoldás**.
  ![Azure IoT Suite típusának kiválasztása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!NOTE]
  > Alapértelmezés szerint ez a minta után létrehoz egy IoT Suite létrehoz egy S2 IoT-központot. Ha ez az IoT-központ nem használatos az eszközök nagy száma, erősen ajánlott a S2 megállapításában, S1, és az IoT Suite törlése, a kapcsolódó IoT-központ is törölheti, ha már nincs szüksége. 

2. Válassza ki **távoli megfigyelési**.

3. Adja meg a megoldás nevét, válassza ki az előfizetés és egy régiót, és kattintson **megoldás létrehozása**. A megoldás eltarthat egy kis ideig, úgy kell létrehozni.
  ![Megoldás létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. A kiépítés befejezése után kattintson **indítása**. Néhány szimulált eszköz a kiépítés során jönnek létre a megoldáshoz. Kattintson a **eszközök** kivétele őket. ![Irányítópult](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![konzol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kattintson a **HOZZÁAD egy ESZKÖZT**.

6. Kattintson a **hozzáadhat új** a **egyéni eszköz**.
  ![Új eszköz hozzáadásához](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kattintson a **saját Eszközazonosító megadása**, adja meg `AZ3166`, és kattintson a **létrehozása**.
  ![Hozzon létre eszköz azonosítója](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Jegyezze fel a **IoT Hub állomásnév**, és kattintson a **végzett**.

## <a name="open-the-remotemonitoring-sample"></a>Nyissa meg a RemoteMonitoring minta

1. Válassza le a DevKit a számítógépről, ha csatlakoztatva van.

2. Indítsa el a Visual STUDIO Code.

3. A DevKit kapcsolódni a számítógéphez. Visual STUDIO Code automatikusan észleli a DevKit, és megnyílik a következő lapokon:
  * A DevKit bemutató lapja.
  * Arduino példák: Gyakorlati minták DevKit használatába.

4. Bontsa ki a bal oldali **ARDUINO példák** szakaszban, keresse meg a **MXCHIP AZ3166 példák > AzureIoT**, és válassza ki **RemoteMonitoring**. A projektmappa azt egy új Visual STUDIO Code ablak nyílik meg.
  > [!NOTE]
  > Ha véletlenül bezárja a panelen, akkor nyissa meg újra. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="provision-required-azure-services"></a>Kiépítés szükséges Azure-szolgáltatások

A megoldás-ablakban futtassa a feladat `Ctrl+P` (macOS: `Cmd+P`) megadásával `task cloud-provision` a megadott szövegmezőben:

A Visual STUDIO Code terminálban egy interaktív parancssori végigvezeti Önt a szükséges Azure-szolgáltatások kiépítése:

![Azure-erőforrások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Build, és töltse fel a kód

1. Használjon `Ctrl+P` (macOS: `Cmd + P`) és típus **tevékenység-config-eszköz-kapcsolat**.

2. A Terminálszolgáltatások megkérdezi, hogy a kapcsolati karakterláncot, amely lekéri a használni kívánt `task cloud-provision` lépés. "Új létrehozása..." gombra kattintva sikerült is megadhatja a saját eszköz kapcsolati karakterláncot

3. A Terminálszolgáltatások konfigurációs módja megadását kéri. Ehhez az szükséges, tartsa lenyomva a gombot A, majd leküldéses és kiadási a Visszaállítás gombra. A képernyőn megjelenik a DevKit azonosító és a "Konfiguráció".
  ![Bemeneti kapcsolati karakterlánc](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Után `task config-device-connection` végzett, kattintson `F1` VS kódparancsok betölteni, és válassza ki `Arduino: Upload`, majd Visual STUDIO Code elindul, ellenőrzi, és a Arduino feltöltése vázlat: ![ellenőrzési és a Arduino rajz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

A DevKit újraindul, és elindul a kódot.

## <a name="test-the-project"></a>A projekt tesztelése

A mintaalkalmazás futtatásakor DevKit érzékelő adatokat küld Wi-Fi keresztül az Azure IoT Suite. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure IoT Suite, és kattintson a **IRÁNYÍTÓPULT**.

2. Az Azure IoT Suite megoldáskonzol DevKit érzékelő állapotát jelennek meg.

![Érzékelőadatok Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Módosítási eszköz azonosítója

Módosíthatja az Eszközazonosítót az IoT-központ a következő [Ez az útmutató](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Ha meg szeretné változtatni a szoftveresen kötött és **AZ3166** megfelelően beállított a kódban Eszközazonosító. [Itt](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) egy, a kód módosítására.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy kapcsolatba velünk a következő csatornák:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy egy DevKit eszköz csatlakoztatása az Azure IoT Suite és megjelenítheti a érzékelőadatok megtanulhatta, az alábbiakban a javasolt lépéseket:

* [Az Azure IoT Suite – áttekintés](https://docs.microsoft.com/azure/iot-suite/)
