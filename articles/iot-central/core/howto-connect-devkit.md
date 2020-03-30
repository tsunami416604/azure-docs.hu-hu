---
title: Fejlesztői eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz | Microsoft dokumentumok
description: Eszközfejlesztőként megtudhatja, hogyan csatlakoztathat egy MXChip IoT DevKit-eszközt az Azure IoT Central alkalmazáshoz az IoT Plug and Play (előzetes verzió) használatával.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158584"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip IoT DevKit-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk bemutatja, hogyan csatlakoztathat egy MXChip IoT DevKit (DevKit) eszközt egy Azure IoT Central alkalmazáshoz. Az eszköz a hitelesített IoT Plug and Play (előzetes verzió) modellt használja a DevKit-eszközhöz az IoT Centralhoz való csatlakozás konfigurálásához.

Ebben az útmutató cikkben:

- A kapcsolat részleteit az IoT Central alkalmazás.
- Készítse elő az eszközt, és csatlakoztassa az IoT Central alkalmazáshoz.
- Tekintse meg a telemetriai adatokat és tulajdonságokat az eszközről az IoT Central ban.

## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

- Egy [DevKit eszköz.](https://aka.ms/iot-devkit-purchase)
- Egy IoT-központi alkalmazás. Az IoT Central alkalmazás létrehozása című részben ismertetett lépéseket [követheti.](./quick-deploy-iot-central.md)

## <a name="get-device-connection-details"></a>Az eszközkapcsolat részleteinek beszerezni

1. Az Azure IoT Central alkalmazásban válassza az **Eszközsablonok** lapot, és válassza **a + Új**lehetőséget. Az **Előre konfigurált eszközsablon használata**szakaszban válassza az **MXChip IoT DevKit**lehetőséget.

    ![Eszközsablon az MXChip IoT DevKit-hez](media/howto-connect-devkit/device-template.png)

1. Válassza a **Tovább: Testreszabás,** majd **létrehozás lehetőséget.**

1. Válassza **az Eszközök** lapot. Az eszközök listájában válassza az **MXChip IoT DevKit** lehetőséget, és válassza **a + Új** lehetőséget, ha új eszközt hoz létre a sablonból.

    ![Új eszköz](media/howto-connect-devkit/new-device.png)

1. Az előugró ablakban adja meg az `SampleDevKit` **eszközazonosítót** és az eszköznevét a ( **eszközneve)** formában. `MXChip IoT DevKit - Sample` Győződjön meg arról, hogy a **Szimulált** beállítás ki van kapcsolva. Ezután válassza **a Létrehozás lehetőséget.**

    ![Eszközazonosító és név](media/howto-connect-devkit/device-id-name.png)

1. Jelölje ki a létrehozott eszközt, majd válassza a **Csatlakozás**lehetőséget. Jegyezze fel az **azonosító hatókört,** **az eszközazonosítót**és az **elsődleges kulcsot.** Szüksége van ezekre az értékekre később ebben az útmutató cikkben.

    ![Eszközkapcsolat adatai](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>A készülék előkészítése

1. Töltse le a [legújabb, előre elkészített Azure IoT Central Plug and Play (előzetes verzió) belső vezérlőprogramját](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) a DevKit-eszközhöz a GitHubról.

1. Csatlakoztassa a DevKit-eszközt a fejlesztői géphez USB-kábellel. A Windows rendszerben egy fájlkezelő ablak nyílik meg a DevKit-eszköz tárolójához leképezett meghajtón. A meghajtó neve lehet például **AZ3166 (D:)**.

1. Húzza a **iotc_devkit.bin** fájlt a meghajtóablakra. Amikor a másolás befejeződött, az eszköz újraindul az új belső vezérlőprogrammal.

    > [!NOTE]
    > Ha hibákat lát a képernyőn, például a **Nincs Wi-Fi**, annak az az oka, hogy a DevKit még nem csatlakozott a WiFi-hez.

1. A DevKiten tartsa lenyomva a **B gombot,** nyomja meg és engedje fel a **Reset** gombot, majd engedje fel a **B gombot.** Az eszköz most hozzáférési pont módban van. A megerősítéshez a képernyőn megjelenik az "IoT DevKit - AP" és a konfigurációs portál IP-címe.

1. A számítógépen vagy táblagépen csatlakozzon az eszköz képernyőjén látható Wi-Fi hálózat névhez. A WiFi hálózat az **AZ-** és a MAC-cím után indul. Amikor csatlakozik ehhez a hálózathoz, nincs internet-hozzáférése. Ez az állapot várható, és csak rövid ideig csatlakozik ehhez a hálózathoz az eszköz konfigurálása közben.

1. Nyissa meg a webböngészőt, és keresse meg a t. [http://192.168.0.1/](http://192.168.0.1/) A következő weboldal on-már látható:

    ![Konfigurációs felhasználói felület](media/howto-connect-devkit/config-ui.png)

    A weboldalon írja be a következőt:

    - A WiFi-hálózat (SSID) neve.
    - A WiFi hálózati jelszó.
    - A kapcsolat részletei: adja meg az **eszközazonosítót,** **az azonosító hatókört**és a korábban feljegyzést készített **SAS elsődleges kulcsot.**

    > [!NOTE]
    > Jelenleg az IoT DevKit csak 2,4 GHz-es Wi-Fi-hálózathoz tud csatlakozni, az 5 GHz-es szolgáltatás hardverkorlátozások miatt nem támogatott.

1. Válassza **az Eszköz konfigurálása**lehetőséget, a DevKit-eszköz újraindul, és futtatja az alkalmazást:

    ![Felhasználói felület újraindítása](media/howto-connect-devkit/reboot-ui.png)

    A DevKit képernyő jeegy megerősítést, hogy az alkalmazás fut:

    ![A DevKit futása](media/howto-connect-devkit/devkit-running.png)

A DevKit először regisztrál egy új eszközt az IoT Central alkalmazásban, majd elindítja az adatok küldését.

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

Ebben a lépésben megtekintheti a telemetriai adatokat az Azure IoT Central alkalmazásban.

Az IoT Central alkalmazásban válassza az **Eszközök** fület, válassza ki a hozzáadott eszközt. Az **Áttekintés** lapon láthatja a telemetriai adatokat a DevKit-eszközről:

![IoT Central-eszköz – áttekintés](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>A kód áttekintése

A kód áttekintéséhez vagy módosításához és fordításához nyissa meg a [Kódminták című részhez.](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy DevKit-eszközt az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés az, hogy ismerje meg, hogyan [állíthat be egyéni eszközsablont](./howto-set-up-template.md) a saját IoT-eszközéhez.
