---
title: 'A felhőben az IoT-DevKit: IoT DevKit AZ3166 csatlakozzon a távoli figyelési IoT megoldásgyorsító |} Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan IoT DevKit AZ3166 a küldendő érzékelők állapotának figyelése és a képi megjelenítés távoli figyelési IoT megoldásgyorsító.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 8aa4d660fbf785a4caf99bcdeddc86aa9929c50d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627739"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Az IoT távoli megfigyelési megoldásgyorsító MXChip IoT DevKit AZ3166 kapcsolódni


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebben az oktatóanyagban elsajátíthatja egy mintaalkalmazást futtatásáról a DevKit érzékelő adatokat küldeni a megoldásgyorsító.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy mindent egy Arduino kompatibilis board gazdag perifériák és érzékelők van. A használatával fejleszthet [Arduino Visual Studio Code kiterjesztése](https://aka.ms/arduino). Az érkezett egy növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) hogy végigvezesse prototípus az eszközök internetes hálózatát (IoT) megoldások, amelyek a Microsoft Azure-szolgáltatások előnyeit.

## <a name="what-you-need"></a>Mi szükséges

Befejezés a [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) számára:

* A Wi-Fi csatlakozik DevKit rendelkezik
* A fejlesztési környezet előkészítése


## <a name="open-the-remotemonitoring-sample"></a>Nyissa meg a RemoteMonitoring minta

1. Válassza le a DevKit a számítógépről, ha csatlakoztatva van.

2. Indítsa el a Visual STUDIO Code.

3. A DevKit kapcsolódni a számítógéphez. Visual STUDIO Code automatikusan észleli a DevKit, és megnyílik a következő lapokon:
  * A DevKit bemutató lapja.
  * Arduino példák: Gyakorlati minták DevKit használatába.

4. Bontsa ki a bal oldali **ARDUINO példák** szakaszban, keresse meg a **MXCHIP AZ3166 példák > AzureIoT**, és válassza ki **RemoteMonitoringv2**. A projektmappa azt egy új Visual STUDIO Code ablak nyílik meg.

  ![Távoli megfigyelési projekt megnyitása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Ha véletlenül bezárja a panelen, akkor nyissa meg újra. Használjon `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) nyissa meg a parancs palettát, írja be a következőt **Arduino**, keresése és válassza a **Arduino: Példák**.

## <a name="add-a-new-physical-device"></a>Fizikai eszköz hozzáadása

A portálon lépjen a **eszközök** szakaszt, és kattintson a a **+ új eszköz** gombra. 

![Új eszköz hozzáadása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

A *új eszköz űrlap* ki kell tölteni.
1. Kattintson a **fizikai** a a *eszköztípus* szakasz.
2. Saját Eszközazonosító megadása (például *MXChip* vagy *AZ3166*).
3. Válasszon **automatikus kulcsok létrehozása** a a *hitelesítési kulcs* szakasz.
4. Kattintson a *alkalmaz* gombra.

![Új eszköz űrlap hozzáadása](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Várjon, amíg a portál befejeződik, az új eszköz kiépítését.

![Új eszköz kiépítése ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Majd az új eszköz, a konfiguráció akkor jelenik meg.
Másolás a **kapcsolati karakterlánc** jön létre.

![Eszköz kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Ez a kapcsolati karakterlánc a következő szakaszban használható.





## <a name="build-and-upload-the-device-code"></a>Build, és töltse fel a kód

Lépjen vissza a Visual Studio Code: 

1. Használjon `Ctrl+P` (macOS: `Cmd + P`) és típus **tevékenység-config-eszköz-kapcsolat**.

  ![Válassza ki az Azure-előfizetés és az IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. A Terminálszolgáltatások megkérdezi, hogy szeretné-e használni a kapcsolati karakterlánc az IoT-eszközök szeretne használni. Válassza ki *új*, és illessze be azt.

  ![illessze be a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. A Terminálszolgáltatások néha megadását kéri konfigurációs módja. Ehhez az szükséges, tartsa lenyomva a gombot A, majd leküldéses és kiadási a Visszaállítás gombra, majd engedje fel a gomb azonosítójához. A képernyőn megjelenik a DevKit azonosító és a "Konfiguráció".

  ![Eszköz DevKit képernyője](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > A kapcsolati karakterláncot a vágólapra kell menteni, ha követte az oktatóanyag utolsó szakasza. Ha nem kell Azure-portálon végezhető, és keresse meg az IoT-központ, a távoli megfigyelési erőforráscsoport. Itt tekintheti meg az IoT hubhoz csatlakoztatott eszközök, és másolja az eszköz kapcsolati karakterláncot.

  ![Keresse meg a kapcsolati karakterlánc](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Most az új fizikai eszköz a Visual STUDIO Code "Azure IoT Hub-eszközöknek" szakaszban látható:

![Figyelje meg, az új IoT Hub eszköz](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>A projekt tesztelése

A mintaalkalmazás futtatásakor DevKit érzékelő adatokat küld Wi-Fi keresztül az IoT-megoldás gyorsítókra. Az eredmény megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az IoT megoldásgyorsító, és kattintson a **IRÁNYÍTÓPULT**.

2. Az IoT megoldás gyorsító konzolon látni fogja a DevKit érzékelő állapotát. 

![Az IoT-megoldás gyorsítók érzékelőadatok](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Ha az érzékelő neve (AZ3166) kattintva a lap nyílik meg az irányítópultot, ahol láthatja a valós idejű MX lapka érzékelők diagram jobb oldalán.


## <a name="send-a-c2d-message"></a>C2D üzenet küldése
Távoli figyelés v2 lehetővé teszi az eszköz távoli metódus meghívására.
Az MX lapka példakód közzéteszi az érzékelő kiválasztásakor lásd: a metódus szakaszban három módszert.

![Módszerek MX lapka használata](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Módosíthatja a a módszerrel "LedColor" MX lapka LED színe. A azt, jelölje be a jelölőnégyzetet, az eszköz, és kattintson az ütemezés gombra. 

![Módszerek MX lapka használata](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Válassza ki a legördülő menüből, ahol a módszerek jelenik meg, egy nevet, és alkalmazza a ChangeColor hívott metódus.

![Legördülő lista MX lapka használata](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Néhány másodpercen belül, a fizikai MX lapka kell módosítani a kereslet az olyan RGB színét (alatt az a gomb)

![Következtében MX lapka használata](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Módosítási eszköz azonosítója

Módosíthatja az Eszközazonosítót az IoT-központ a következő [Ez az útmutató](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy kapcsolatba velünk a következő csatornák:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte a DevKit eszköz csatlakoztatása az IoT-megoldás gyorsítók és jelenítheti meg az érzékelők adataiból, az alábbiakban a javasolt lépéseket:

* [Az IoT-megoldás gyorsítók áttekintése](https://docs.microsoft.com/azure/iot-suite/)
* [Csatlakoztasson egy MXChip IoT DevKit eszközt a Microsoft IoT Central alkalmazás](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
