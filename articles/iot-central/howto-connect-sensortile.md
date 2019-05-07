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
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160905"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile.box eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők SensorTile.box eszköz csatlakozni a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előzetes teendők

A jelen cikkben ismertetett lépések végrehajtásához szüksége van az alábbi forrásanyagokat:

* Egy SensorTile.box eszközt, tekintse meg [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box) további információt.
* A ST Gedélyezése érzékelő az alkalmazás telepítve van az Android-eszközön, akkor [letölthető innen] (https://play.google.com/store/apps/details?id=com.st.bluems). További információk: [ST Gedélyezése érzékelő] ()http://www.st.com/stblesensor)
* A létrehozott Azure IoT Central alkalmazáshoz a **DevKits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Adja hozzá a **SensorTile.box** eszköz sablon funkcionáló az IoT-központ alkalmazásba a **eszközsablonok** lap, kattintson a **+ új**, és kiválasztja a **SensorTile** sablont.

### <a name="get-your-device-connection-details"></a>Az eszköz kapcsolat részleteinek beolvasása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **SensorTile.box** eszköz sablont, és jegyezze fel az eszköz kapcsolat részletei: **Hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs**:

1. A Device Explorer eszköz hozzáadásához. Válassza ki **+ új > valós** valós eszköz hozzáadásához.

    * Adjon meg egy kisbetűs **Eszközazonosító**, vagy használja a javasolt **Eszközazonosító**.
    * Adjon meg egy **eszköznév**, vagy használja a javasolt név

    ![Eszköz hozzáadása](media/howto-connect-sensortile/real-device.png)

1. Kérheti le az eszköz kapcsolat adatait, **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**válassza **Connect** az eszköz oldalon.

    ![Kapcsolat adatai](media/howto-connect-sensortile/connect-device.png)

1. Jegyezze fel a kapcsolati adatok. Ideiglenesen megszakadt a kapcsolat az internetről a következő lépésben DevKit eszközét előkészítésekor.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>A mobilalkalmazás-a SensorTile.box beállítása

Ebben a szakaszban megtudhatja, az eszközön az alkalmazás belső vezérlőprogram és elküldheti az eszközön lévő adatokat az IoT-központ a ST Gedélyezése érzékelő mobilalkalmazáson keresztül Bluetooth alacsony energia (táblázat) kapcsolatot.
1. Nyissa meg a ST Gedélyezése érzékelő alkalmazást, majd nyomja le az **hozzon létre egy új alkalmazást** gombra.

    ![App létrehozása](media/howto-connect-sensortile/create-app.png)

1. Válassza ki a **mérőeszköz** alkalmazás.
1. A Feltöltés gombra.

    ![Mérőeszköz feltöltése](media/howto-connect-sensortile/barometer-upload.png)

1. Kattintson a lejátszás gombra, a SensorTile.box társított.
1. A folyamat befejeződése után a SensorTile.box fog adatfolyam a hőmérsékletet, nyomás és páratartalom Gedélyezése keresztül.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>A SensorTile.box csatlakoztatása a felhőhöz

Ebben a szakaszban, megtudhatja, hogyan a SensorTile.box csatlakozhat a mobilalkalmazás, és csatlakoztatja a mobil-alkalmazást a felhőbe.
1. A bal oldali menüben válassza ki azt a **felhőalapú naplózás** gombra.

    ![A felhő-naplózás](media/howto-connect-sensortile/cloud-logging.png)

1. Válassza ki **Azure IoT Central** a felhőszolgáltatóként.
1. Helyezze be az Eszközazonosítót és a hatókör azonosítója, amely korábban észleltek.

    ![Hitelesítő adatok](media/howto-connect-sensortile/credentials.png)

1. Válassza ki a **Alkalmazáskulcsot** választógombot.
1. Kattintson a **Connect** , és válassza ki a feltölteni kívánt telemetriai adatokat.
1. Néhány másodperc elteltével az adatokat az IoT-központ irányítópult fog megjelenni.

## <a name="sensortilebox-device-template-details"></a>SensorTile.box eszköz sablon részletei

A következő jellemzőkkel SensorTile.box eszköz sablonból létrehozott alkalmazáshoz:

### <a name="telemetry"></a>Telemetria

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 30       | 90     | 1              |
| TEMP           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | A DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | A DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | A DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan lehet egy SensorTile.box csatlakozni az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre megtudhatja, hogyan [állítson be egy egyéni sablont](howto-set-up-template.md) a saját IoT-eszköz.
