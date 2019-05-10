---
title: SensorTile.box eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Eszköz a fejlesztők megtudhatja, hogyan SensorTile.box eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472168"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile.box eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők SensorTile.box eszköz csatlakozni a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben ismertetett lépések végrehajtásához szüksége van az alábbi forrásanyagokat:

* SensorTile.box eszköz. További információkért lásd: [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Az Android-eszközön telepített ST Gedélyezése érzékelő alkalmazást, akkor is [innen tölthet le](https://play.google.com/store/apps/details?id=com.st.bluems). A további információkat látogasson el: [St. BLA-érzékelő](https://www.st.com/stblesensor)
* A létrehozott Azure IoT Central alkalmazáshoz a **DevKits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Adja hozzá a **SensorTile.box** eszköz sablon funkcionáló az IoT-központ alkalmazásba a **eszközsablonok** lap, kattintson a **+ új**, és kiválasztja a **SensorTile.box** sablont.

### <a name="get-your-device-connection-details"></a>Az eszköz kapcsolat részleteinek beolvasása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **SensorTile.box** eszköz sablont, és jegyezze fel az eszköz kapcsolat részletei: **Hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**:

1. A Device Explorer eszköz hozzáadásához. Válassza ki **+ új > valós** valós eszköz hozzáadásához.

    * Adjon meg egy kisbetűs **Eszközazonosító**, vagy használja a javasolt **Eszközazonosító**.
    * Adjon meg egy **eszköznév**, vagy használja a javasolt név

    ![Eszköz hozzáadása](media/howto-connect-sensortile/real-device.png)

1. Kérheti le az eszköz kapcsolat adatait, **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**válassza **Connect** az eszköz oldalon.

    ![Kapcsolat részletei](media/howto-connect-sensortile/connect-device.png)

1. Jegyezze fel a kapcsolati adatok. Ideiglenesen megszakadt a kapcsolat az internetről a következő lépésben DevKit eszközét előkészítésekor.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>A mobilalkalmazás-a SensorTile.box beállítása

Ebben a szakaszban megismerheti, hogyan küldhet az eszközre az alkalmazás belső vezérlőprogram. Majd hogyan az eszközön lévő adatokat küldhet az IoT Central használatával Bluetooth alacsony energia (táblázat) kapcsolat ST Gedélyezése érzékelő mobilalkalmazáson keresztül.

1. Nyissa meg a ST Gedélyezése érzékelő alkalmazást, majd nyomja le az **hozzon létre egy új alkalmazást** gombra.

    ![App létrehozása](media/howto-connect-sensortile/create-app.png)

1. Válassza ki a **mérőeszköz** alkalmazás.
1. A Feltöltés gombra.

    ![Mérőeszköz feltöltése](media/howto-connect-sensortile/barometer-upload.png)

1. Kattintson a lejátszás gombra, a SensorTile.box társított.
1. A folyamat befejeződése után a a SensorTile.box keresztül Gedélyezése adatfolyamként elküldi a a hőmérsékletet, nyomás és páratartalom.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>A SensorTile.box csatlakoztatása a felhőhöz

Ebben a szakaszban megismerheti, hogyan a SensorTile.box csatlakozhat a mobilalkalmazás, és csatlakoztatja a mobil-alkalmazást a felhőbe.

1. A bal oldali menüben válassza ki azt a **felhőalapú naplózás** gombra.

    ![A felhő-naplózás](media/howto-connect-sensortile/cloud-logging.png)

1. Válassza ki **Azure IoT Central** a felhőszolgáltatóként.
1. Helyezze be az Eszközazonosítót és a hatókör azonosítója, amely korábban észleltek.

    ![Hitelesítő adatok](media/howto-connect-sensortile/credentials.png)

1. Válassza ki a **Alkalmazáskulcsot** választógombot.
1. Kattintson a **Connect** , és válassza ki a feltölteni kívánt telemetriai adatokat.
1. Néhány másodperc elteltével az adatokat az IoT-központ alkalmazás irányítópulton jelenik meg.

## <a name="sensortilebox-device-template-details"></a>SensorTile.box eszköz sablon részletei

A következő jellemzőkkel SensorTile.box eszköz sablonból létrehozott alkalmazáshoz:

### <a name="telemetry"></a>Telemetria

| Mezőnév     | Mértékegységek  | Minimum | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 30       | 90     | 1.              |
| TEMP           | °C     | 0     | 40     | 1.              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | A DPS   | -3276   | 3276    | 1.              |
| gyroscopeY     | A DPS   | -3276   | 3276    | 1.              |
| gyroscopeZ     | A DPS   | -3276   | 3276    | 1.              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan lehet egy SensorTile.box csatlakozni az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre-e további [beállítása egy egyéni sablon](howto-set-up-template.md) a saját IoT-eszköz.
