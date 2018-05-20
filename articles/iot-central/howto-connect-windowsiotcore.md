---
title: A Windows IoT Core eszköz csatlakoztatása az Azure IoT központi alkalmazás |} Microsoft Docs
description: Eszköz fejlesztőként útmutató egy MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT központi alkalmazás.
services: iot-central
author: miriamb
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: c38231f97eeb1c4511702bf3e788f72918cab045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>A Windows IoT Core eszköz csatlakoztatása az Azure IoT központi alkalmazás

Ez a cikk ismerteti, hogyan fejlesztőként eszköz, a Windows IoT Core eszköz kapcsolódni a Microsoft Azure IoT központi alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. A létrehozott Azure IoT központi alkalmazást a **minta Devkits** alkalmazássablon. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
2. A Windows 10 IoT Core operációs rendszert futtató eszköz. Ennél a bemutatónál fogjuk használni egy málna Pi

A létrehozott alkalmazást, a **minta Devkits** alkalmazás sablon tartalmaz egy **Windows IoT Core** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | egység  | Minimális | Maximum | Tizedeshelyen |
| -------------- | ------ | ------- | ------- | -------------- |
| nedvességtartalma       | %      | 0       | 100     | 0              |
| TEMP           | ° C     | tartsuk ott -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | egység | Tizedeshelyen | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás hozzáadása a valódi eszközről a **Windows IoT Core** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterlánc. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Az IoT-Core Windows-eszköz előkészítése

Beállítása egy Windows IoT Core eszköz kérjük, kövesse a részletes útmutató: [Windows IoT Core eszköz beállítása] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás hozzáadása a valódi eszközről a **Windows IoT Core** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterlánc. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>A Windows 10 IoT Core eszköz előkészítése

### <a name="what-youll-need"></a>Szükséges eszközök

Egy fizikai Windows 10 IoT Core eszköz beállításához, akkor először a Windows 10 IoT Core futtató eszközzel rendelkezik. Ismerje meg, hogyan állíthat be egy Windows 10 IoT Core eszköz [Itt](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Konfigurálnia kell egy ügyfélalkalmazást, amely képes kommunikálni Azure IoT központi. A saját egyéni alkalmazás az Azure SDK használatával létrehozhatja és telepítheti az eszközön, a Visual Studio használatával, vagy letöltheti egy [előre elkészített minta](https://developer.microsoft.com/en-us/windows/iot/samples) és egyszerűen telepíteni, és futtassa az eszközön. 

### <a name="deploying-the-sample-client-application"></a>A mintaalkalmazás ügyfél telepítése

Az ügyfélalkalmazás az előző lépésben a Windows 10 IoT-eszközön ahhoz, hogy felkészítse telepítéséhez:

**Győződjön meg arról, a kapcsolati karakterlánc az ügyfélalkalmazáshoz használata az eszközön tárolt**
* Az asztalon a kapcsolódási karakterlánc mentése connection.string.iothub nevű fájlt.
* A szöveges fájl másolása az eszközön a dokumentum mappába: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Miután ezt megtette, meg kell nyitnia a [Windows Eszközportál](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal) http://[device-IP-address]:8080 írja be a böngészőbe.

Mert és, ahogy azt az alábbi Ha érdemes:
1. Bontsa ki a "Alkalmazások" csomópontot a bal oldalon.
2. Kattintson a "gyors futtatási minták".
3. Kattintson az "Azure IoT Hub ügyfél".
4. Kattintson a "Központi telepítése és futtatása" elemre.

![Az Azure IoT Hub-ügyfél Windows-eszköz portál GIF](./media/howto-connect-windowsiotcore/iothubapp.gif)

Ha sikeres, az alkalmazás indítsa el az eszközön, és néznek ki:

![Képernyőkép a Azure IoT Hub ügyfélalkalmazás](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

A Azure IoT központi megtekintheti, hogy a kód a málna Pi futó hogyan működjön együtt az alkalmazás:

* Az a **mérések** lap az valós eszközhöz, lásd: a telemetriai adatokat.
* Az a **tulajdonságok** lapon megtekintheti a jelentett szám Die tulajdonság értéke.
* Az a **beállítások** lapon módosíthatja a málna Pi feszültség és ventilátor sebesség például különböző beállításait.

## <a name="download-the-source-code"></a>A forráskód letöltése

Ha szeretné vizsgálatát, és módosítsa a forráskódot, az ügyfélalkalmazáshoz, letöltheti azt a Githubról [Itt](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Ha azt tervezi, a kód módosítására, kövesse ezeket az utasításokat az információs fájl [Itt](https://github.com/Microsoft/Windows-iotcore-samples) az asztali operációs rendszerhez.

> [!NOTE]
> Ha **git** nincs telepítve a fejlesztői környezetben, letöltheti a [ https://git-scm.com/download ](https://git-scm.com/download).
