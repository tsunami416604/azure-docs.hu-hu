---
title: A Windows IoT Core-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Eszköz a fejlesztők megtudhatja, hogyan az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617852"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>A Windows IoT Core-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők, a Windows IoT Core-eszköz csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).

- A Windows 10 IoT Core operációs rendszert futtató eszköz. További információkért lásd: [a Windows 10 IoT Core-eszköz beállítása](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- A fejlesztői gépen való [Node.js](https://nodejs.org/) 8.0.0 verzió vagy újabb verziója szükséges. Futtathat `node --version` a parancssorban a verzió ellenőrzéséhez. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="the-sample-devkits-application"></a>A minta Devkits alkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Windows IoT Core** eszköz sablon a következő jellemzőkkel:

- Az eszköz telemetriai mérések: **Páratartalom**, **hőmérséklet**, és **nyomás**.
- A beállítással szabályozhatja **ventilátor sebesség**.
- Egy adott eszköztulajdonság **Die szám** és a egy felhőbeli tulajdonság **hely**.

Részletes információ az eszköz sablon konfigurálása, lásd: [Windows IoT Core-eszköz sablon részleteinek](#device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás használatához a **Device Explorer** lapot, a valós eszközöknek a **Windows 10 IoT Core** eszköz sablont. Jegyezze fel az eszköz kapcsolati adatok (**hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**). További információkért lásd: [kapcsolati adatok lekéréséhez](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Az eszköz előkészítése

Az eszköz csatlakozik az IoT-központ szükséges egy kapcsolati karakterláncot.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Az eszköz kódot, amely a kapcsolati karakterláncot, mentse azt egy nevű fájlban **connection.string.iothub** mappában `C:\Data\Users\DefaultAccount\Documents\` az eszközén Windows 10 IoT Core.

Másolása a **connection.string.iothub** asztali gépén a fájlt a `C:\Data\Users\DefaultAccount\Documents\` mappát az eszközön, használhatja a [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. A webböngésző használatával keresse meg a Windows Device Portal, az eszközön.
1. Keresse meg az eszközön a fájlokat, válassza a **alkalmazások > fájlkezelő**.
1. Navigáljon a **felhasználói Folders\Documents**. Ezután töltse fel a **connection.string.iothub** fájlt:

    ![Töltse fel a kapcsolati karakterlánc](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Üzembe helyezése és futtatása

Üzembe helyezését, és futtassa a mintaalkalmazást az eszközön, használhatja a [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. A webböngésző használatával keresse meg a Windows Device Portal, az eszközön.
1. Helyezhet üzembe és futtathat a **Azure IoT Hub ügyfél** alkalmazást, válassza a **alkalmazások > gyors futtatási minták**. Válassza a **Azure IoT Hub ügyfél**.
1. Válassza a **üzembe helyezése és futtatása**.

    ![Üzembe helyezése és futtatása](media/howto-connect-windowsiotcore/quick-run.png)

Néhány perc múlva a telemetriai adatokat megtekintheti az eszközről az IoT-központ alkalmazásában.

A [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) olyan eszközöket tartalmaz, az eszköz hibaelhárításához használhatja:

- A **alkalmazások manager** lap lehetővé teszi az eszközön futó alkalmazások.
- Ha az eszköz csatlakozik egy figyelő nincs, akkor használhatja a **eszközbeállítások** oldalon az eszközről képernyőképek rögzítése. Példa:

    ![Alkalmazás képernyőképe](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Letöltheti a forráskódot

Ha szeretne felfedezéséhez és módosításához az ügyfél alkalmazás forráskódja, letöltheti azt a [Windows-iotcore-minták GitHub-adattár](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Eszköz-sablon részletei

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Windows IoT Core** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| TEMP           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Tulajdonságok

| Typo            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Földrajzi egység     | location   | –       |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre megtudhatja, hogyan [állítson be egy egyéni sablont](howto-set-up-template.md) a saját IoT-eszköz.