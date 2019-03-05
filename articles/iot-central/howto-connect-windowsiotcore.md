---
title: A Windows IoT Core-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Eszköz a fejlesztők megtudhatja, hogyan az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 85fba27c856561eb1270e719dcf24b88d2d5a01f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309910"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>A Windows IoT Core-eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők, a Windows IoT Core-eszköz csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
2. A Windows 10 IoT Core operációs rendszert futtató eszköz. Ebben a bemutatóban a Raspberry Pi használjuk.


## <a name="sample-devkits-application"></a>**Minta Devkits** alkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Windows IoT Core** eszköz sablon a következő jellemzőkkel: 

- Telemetriai adatokat, amely tartalmazza az eszköz a mérések **páratartalom**, **hőmérséklet** és **nyomás**. 
- Beállítások megjelenítése **ventilátor sebesség**.
- Eszköztulajdonság tartalmazó tulajdonságainak **die szám** és **hely** felhőbeli tulajdonság.


Tekintse meg a konfigurációs eszköz sablon kapcsolatos részletes [Windows IoT Core-eszköz sablon részletei](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Windows IoT Core** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterláncát. További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>A Windows IoT Core-eszköz előkészítése

Állítsa be a Windows IoT Core-eszköz kövesse a részletes útmutató: [Windows IoT Core-eszköz beállítása](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Windows IoT Core** eszköz sablont, és jegyezze fel az eszköz kapcsolat részleteinek (**hatókör azonosítója, az eszköz azonosítója, az elsődleges kulcs**). További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).

 > [!NOTE]
   > Az Azure IoT Central átváltott használatával az Azure IoT Hub Device Provisioning service (DPS) az összes eszköz kapcsolat, ezeket az utasításokat követve [az eszköz kapcsolati karakterláncának lekérése](concepts-connectivity.md#get-a-connection-string) és az oktatóanyag további részeinek folytatásához.

## <a name="prepare-the-windows-10-iot-core-device"></a>A Windows 10 IoT Core-eszköz előkészítése

### <a name="what-youll-need"></a>Az alábbiak szükségesek

Egy valódi Windows 10 IoT Core-eszköz beállításához kell rendelkeznie a Windows 10 IoT Core-es eszközök. Ismerje meg, hogyan állítható be a Windows 10 IoT Core-eszköz [Itt](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

Egy ügyfélalkalmazás, amely képes kommunikálni az Azure IoT Central is szüksége lesz. Az Azure SDK-val saját egyéni alkalmazások készítése és üzembe helyezése a Visual Studio-eszközök az, vagy letöltheti egy [előre elkészített mintát](https://developer.microsoft.com/windows/iot/samples) és egyszerűen telepítheti és futtathatja az eszközt. 

### <a name="deploying-the-sample-client-application"></a>A mintaalkalmazás-ügyfél telepítése

Az ügyfélalkalmazás az előző lépésben a Windows 10 IoT-eszközök annak érdekében, hogy előkészítéshez telepítéséhez:

**Győződjön meg arról, az ügyfélalkalmazás használata az eszközön tárolt kapcsolati karakterlánc**
* Az asztalon mentse a kapcsolati karakterlánc connection.string.iothub nevű szövegfájlba.
* A szöveges fájlt másolja az eszköz dokumentum mappába: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Miután ezt megtette, meg kell nyitnia a [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) bármely böngészőben http://[device-IP-address]:8080 beírásával.

Vannak, és a ha alább is látható, a szeretné:
1. Bontsa ki a **alkalmazások** csomópontot a bal oldalon.
2. Válassza ki **gyors futtatási minták**.
3. Válassza ki **az Azure IoT Hub ügyfél**.
4. Válassza ki **üzembe helyezése és futtatása**.

![Az Azure IoT Hub ügyfél Windows Device Portal GIF](./media/howto-connect-windowsiotcore/iothubapp.gif)

Ha sikeres, az alkalmazás az eszköz elindul, és néznek ki:

![Képernyőkép az Azure IoT Hub az ügyfélalkalmazás](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

Az Azure IoT Central láthatja, hogy a kód a Raspberry Pi-on futó hogyan működjön együtt az alkalmazás:

* Az a **mérések** lap a valós eszközhöz, tekintse meg a telemetriát.
* Az a **tulajdonságok** lapon láthatja a jelentett szám Die tulajdonság értékét.
* Az a **beállítások** lapon módosíthatja a Raspberry Pi feszültség és ventilátor sebesség például a különböző beállításait.

## <a name="download-the-source-code"></a>Letöltheti a forráskódot

Ha azt szeretné, és az ügyféloldali alkalmazás forráskódjának módosítása, letöltheti a Githubról [Itt](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Ha azt tervezi, a kód módosítása, kövesse ezeket az utasításokat az információs fájlban [Itt](https://github.com/Microsoft/Windows-iotcore-samples) az asztali operációs rendszerének.

> [!NOTE]
> Ha **git** nincs telepítve a fejlesztési környezet töltheti le a [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Windows IoT Core-eszköz sablon részletei

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
| Szöveg            | Hely     | location   | –       |
