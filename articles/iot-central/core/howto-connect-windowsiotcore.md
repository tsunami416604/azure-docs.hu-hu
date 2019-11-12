---
title: A Windows IoT Core összekötése az Azure IoT Central alkalmazással | Microsoft Docs
description: A MXChip IoT fejlesztői készlet-eszközök Azure IoT Central-alkalmazáshoz való csatlakoztatását bemutató útmutató.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 43e99c54249738436f24369ed3525e78ff971a12
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930204"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Windows IoT Core-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan lehet egy Windows IoT Core-eszközt a Microsoft Azure IoT Central alkalmazáshoz csatlakozni az eszköz fejlesztői számára.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- A **mintául szolgáló Devkits** létrehozott Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).

- A Windows 10 IoT Core operációs rendszert futtató eszköz. További információ: [a Windows 10 IoT Core-eszköz beállítása](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- A [Node. js](https://nodejs.org/) 8.0.0 vagy újabb verzióját futtató fejlesztői gép. Az `node --version` a parancssorban futtatva ellenőrizhető a verzió. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="the-sample-devkits-application"></a>A minta Devkits alkalmazás

A **mintául szolgáló Devkits** létrehozott alkalmazások egy **Windows IoT Core** -eszközt tartalmaznak a következő jellemzőkkel:

- Telemetria mérések az eszközhöz: **páratartalom**, **hőmérséklet**és **nyomás**.
- A **ventilátor sebességének**szabályozására szolgáló beállítás.
- Egy eszköz tulajdonságának **Megnyomási száma** és egy Felhőbeli tulajdonság **helye**.

Az eszköz sablonjának konfigurálásával kapcsolatos részletes információkért lásd: a [Windows IoT Core-eszköz sablonjának részletei](#device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban használja a **Device Explorer** lapot, és adjon hozzá egy valódi eszközt a **Windows 10 IoT Core** -eszköz sablonhoz. Jegyezze fel az eszköz kapcsolatának részleteit (a**hatókör azonosítóját**, az **eszköz azonosítóját**és az **elsődleges kulcsot**).

## <a name="prepare-the-device"></a>Az eszköz előkészítése

Ahhoz, hogy az eszköz csatlakozhasson IoT Centralhoz, kapcsolati karakterláncra van szüksége:

1. Használja a `dps-keygen` parancssori segédprogramot egy kapcsolódási karakterlánc létrehozásához:

    A [Key Generator segédprogram](https://github.com/Azure/dps-keygen)telepítéséhez futtassa a következő parancsot:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Kapcsolati karakterlánc létrehozásához futtassa a következő parancsot a korábban feljegyzett kapcsolati adatok használatával:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Másolja a kapcsolódási karakterláncot az eszköz kódjában használandó `dps-keygen` kimenetből.

Ahhoz, hogy az eszköz kódja hozzáférhessen a kapcsolati karakterlánchoz, mentse azt egy **kapcsolat. string. iothub** nevű fájlba a Windows 10 IoT Core-eszközén lévő mappában `C:\Data\Users\DefaultAccount\Documents\`.

Ha a **kapcsolat. string. iothub** fájlt az asztali gépről az eszköz `C:\Data\Users\DefaultAccount\Documents\` mappájába kívánja másolni, a [Windows-eszköz portált](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)használhatja:

1. A webböngésző segítségével nyissa meg az eszközön a Windows-eszközök portálját.
1. Az eszközön található fájlok tallózásához válassza az **alkalmazások > fájlkezelő**lehetőséget.
1. Navigáljon a **felhasználói Folders\Documents**. Ezután töltse fel a. **String. iothub** fájlt:

    ![A kapcsolatok karakterláncának feltöltése](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Üzembe helyezés és Futtatás

A minta alkalmazás eszközön való üzembe helyezéséhez és futtatásához használja a [Windows-eszközök portálját](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. A webböngésző segítségével nyissa meg az eszközön a Windows-eszközök portálját.
1. Az **Azure IoT hub ügyfélalkalmazás** üzembe helyezéséhez és futtatásához válassza az **alkalmazások > a gyors futtatású minták**elemet. Ezután válassza az **Azure IoT hub-ügyfél**lehetőséget.
1. Ezután válassza **az üzembe helyezés és Futtatás**lehetőséget.

    ![Üzembe helyezés és Futtatás](media/howto-connect-windowsiotcore/quick-run.png)

Néhány perc elteltével megtekintheti a telemetria az eszközéről a IoT Central alkalmazásban.

A [Windows-eszközök portálon](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) olyan eszközök találhatók, amelyek segítségével elháríthatja az eszközt:

- Az **alkalmazások kezelője** lapon vezérelheti az eszközön futó alkalmazásokat.
- Ha nem rendelkezik csatlakoztatott monitorral az eszközhöz, az **eszközbeállítások lapon rögzítheti az eszköz** képernyőképeit. Például:

    ![Alkalmazás képernyőképe](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Forráskód letöltése

Ha szeretné felderíteni és módosítani az ügyfélalkalmazás forráskódját, letöltheti azt a [Windows-iotcore-Samples GitHub-adattárból](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Az eszköz sablonjának részletei

A **mintául szolgáló Devkits** létrehozott alkalmazások egy **Windows IoT Core** -eszközt tartalmaznak a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| ideiglenes           | °C     | – 40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállítások

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Data type |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonsága | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | N/A       |

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan csatlakoztatható egy Windows IoT Core-eszköz az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [állíthat be egyéni eszközöket](howto-set-up-template.md) a saját IoT-eszköze számára.
