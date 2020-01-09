---
title: Fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: A MXChip IoT fejlesztői készlet-eszköznek az Azure IoT Central-alkalmazáshoz való csatlakoztatásáról a IoT Plug and Play használatával tájékozódhat.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 9ba68e90b5ac64c7bdb08af2b567f00c1a076808
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453918"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>MXChip-IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz (előzetes verziójú funkciók)

Ez a cikk bemutatja, hogyan csatlakoztatható egy MXChip IoT fejlesztői készlet (fejlesztői készlet) eszköz egy Azure IoT Central-alkalmazáshoz. Az eszköz a fejlesztői készlet-eszköz Certified IoT Plug and Play modelljét használja a IoT Centralhoz való kapcsolódás konfigurálásához.

Ebben a útmutatóban a következőket kell tennie:

- A IoT Central alkalmazás kapcsolati adatainak beolvasása.
- Készítse elő az eszközt, és kapcsolódjon a IoT Central alkalmazáshoz.
- Az eszköz telemetria és tulajdonságainak megtekintése IoT Centralban.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő erőforrásokra van szükség:

- Egy [fejlesztői készlet-eszköz](https://aka.ms/iot-devkit-purchase).
- Az **előnézeti alkalmazás** sablonjában létrehozott IoT Central alkalmazás. Követheti a [IoT Plug and Play alkalmazás létrehozása](./quick-deploy-iot-central.md)című témakör lépéseit.

## <a name="get-device-connection-details"></a>Eszköz kapcsolati adatainak beolvasása

1. Az Azure IoT Central alkalmazásban válassza az **eszközök sablonok** fület, és válassza az **új**lehetőséget. Az **előre konfigurált eszköz használata**című szakaszban válassza a **MXChip IoT fejlesztői készlet** elemet a listából. Majd válassza a **Tovább: testreszabás** és **Létrehozás**lehetőséget.

    ![Eszköz sablonja a MXChip IoT fejlesztői készlet](media/howto-connect-devkit/device-template.png)

1. Válassza az **eszközök** lap eszközök listájában a **MXChip IoT fejlesztői készlet** lehetőséget, majd az **új** elemre kattintva hozzon létre egy új eszközt az eszköz sablonból.

    ![Új eszköz](media/howto-connect-devkit/new-device.png)

1. Az előugró ablakban adja meg az **eszköz azonosítóját** `SampleDevKit` és **eszköznév** `MXChip IoT DevKit - Sample`ként. Győződjön meg arról, hogy a **szimulált** beállítás ki van kapcsolva. Ezután kattintson a **Létrehozás** elemre.

    ![Eszköz azonosítója és neve](media/howto-connect-devkit/device-id-name.png)

1. Kattintson az imént létrehozott eszközre, és válassza a **kapcsolat**lehetőséget. Jegyezze fel az **azonosító hatókörét**, az **eszköz azonosítóját** és az **elsődleges kulcsot**.

    ![Eszköz csatlakoztatási adatai](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Az eszköz előkészítése

1. Töltse le a legújabb, [előre elkészített Azure IoT Central Plug and Play belső vezérlőprogram](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) a fejlesztői készlet-eszközhöz a githubról.

1. Csatlakoztassa a fejlesztői készlet eszközt a fejlesztői számítógéphez egy USB-kábellel. A Windowsban egy fájlkezelő ablak nyílik meg a fejlesztői készlet eszközön lévő tárolóhoz rendelt meghajtón. Előfordulhat például, hogy a meghajtó neve **AZ3166 (D:)** .

1. Húzza a **iotc_devkit. bin** fájlt a meghajtó ablakára. A másolás befejezésekor az eszköz újraindul az új belső vezérlőprogram.

    > [!NOTE]
    > Ha a képernyőn olyan hibák jelennek meg, mint például a **Wi-Fi**, ennek az az oka, hogy a fejlesztői készlet még nem kapcsolódott a WiFi szolgáltatáshoz.

1. A fejlesztői készlet tartsa lenyomva a **b gombot**, nyomja le és szabadítsa fel az **Alaphelyzetbe állítás** gombot, majd a **b gombot**. Az eszköz most már hozzáférési pont módban van. A megerősítéshez a képernyő a "IoT fejlesztői készlet-AP" és a konfigurációs portál IP-címét jeleníti meg.

1. A számítógépen vagy a táblaszámítógépen kapcsolódjon az eszköz képernyőjén megjelenő WiFi-hálózathoz. A Wi-Fi-hálózat az **az-** és a MAC-címe után kezdődik. Ha csatlakozik ehhez a hálózathoz, nem rendelkezik internet-hozzáféréssel. Ez az állapot várható, és az eszköz konfigurálásakor csak rövid idő alatt csatlakozhat ehhez a hálózathoz.

1. Nyissa meg a webböngészőt, és navigáljon [http://192.168.0.1/](http://192.168.0.1/). A következő weblap jelenik meg:

    ![Konfiguráció felhasználói felülete](media/howto-connect-devkit/config-ui.png)

    A weblapon írja be a következőt:

    - A WiFi-hálózat (SSID) neve.
    - A WiFi hálózati jelszava.
    - A kapcsolat részletei: írja be az **eszköz azonosítóját**, az **azonosító hatókörét** és az **sas elsődleges kulcsát** , amelyet korábban jegyzett készített.

    > [!NOTE]
    > Jelenleg a IoT fejlesztői készlet csak a 2,4 GHz-es Wi-Fi-hez tud csatlakozni, az 5 GHz-es hardveres korlátozások miatt nem támogatott.

1. Válassza az **eszköz konfigurálása**lehetőséget, majd a fejlesztői készlet eszköz újraindul, és futtatja az alkalmazást:

    ![Felhasználói felület újraindítása](media/howto-connect-devkit/reboot-ui.png)

    A fejlesztői készlet képernyőn az alkalmazás futtatásának megerősítése látható:

    ![Futó fejlesztői készlet](media/howto-connect-devkit/devkit-running.png)

A fejlesztői készlet először regisztrál egy új eszközt IoT Central alkalmazásban, majd elkezdi az adatok küldését.

## <a name="view-the-telemetry"></a>A telemetria megtekintése

Ebben a lépésben megtekinti a telemetria az Azure IoT Central alkalmazásban.

A IoT Central alkalmazásban válassza az **eszközök** fület, és válassza ki a hozzáadott eszközt. Az **Áttekintés** lapon a telemetria a fejlesztői készlet eszközről tekintheti meg:

![IoT Central eszköz áttekintése](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>A kód áttekintése

A kód áttekintéséhez vagy módosításához és fordításához nyissa meg a [kód mintáit](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan csatlakoztatható egy fejlesztői készlet-eszköz az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [állítható be egy egyéni eszköz-sablon](./howto-set-up-template.md) a saját IoT-eszközhöz.
