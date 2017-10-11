---
title: "SensorTag eszköz & Azure IoT átjáró - hibaelhárítása |} Microsoft Docs"
description: "Intel NUC átjáró hibaelhárítási lap"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az IOT problémák, internet dolgot problémák"
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting"></a>Hibaelhárítás

## <a name="hardware-issues"></a>Hardverproblémák

### <a name="ti-sensortag-cannot-be-connected"></a>Nem lehet csatlakozni a TI SensorTag

SensorTag kapcsolódási problémák elhárításához használja a [SensorTag app](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Probléma lehet az Intel NUC

A rendszerindítási problémák megoldásához tekintse meg [Intel NUC nem rendszerindító hibaelhárítási](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Operációs rendszerrel kapcsolatos problémák megoldásához tekintse meg [Intel NUC operációs rendszer hibaelhárítási](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Egyéb problémák elhárításához tekintse meg [villogni és az Intel NUC-e sípoló kódokat](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>NODE.js csomag problémák

### <a name="no-response-during-gulp-tasks"></a>Gulp műveletek során nincs válasz.

Ha a futó feladatok gulp problémákat tapasztal, adhat hozzá a `--verbose` hibakeresési lehetőséget. Állítsa le a jelenlegi gulp feladatok használatával próbál `Ctrl + C`, és futtassa a következő parancsot a konzolablakban tekintse meg a hibakeresési üzeneteket. A részletes hibaüzenetek a konzol kimeneti jelenhet meg.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Eszköz-felderítési problémák

A gyakori problémák elhárításában súgó a `discover-sensortag` parancs, ellenőrizze a [wiki lapján](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>npm problémák

Próbálja meg frissíteni az npm-csomagot a következő parancs futtatásával:

```bash
npm install -g npm
```

Ha a probléma továbbra is fennáll, hagyja meg a megjegyzéseit, ez a cikk végén, vagy hozzon létre egy GitHub probléma a [minta tárház](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Távoli hibakeresés
> Alább utasításokat úgy van kialakítva, ebben az oktatóanyagban használt node.js parancsfájlokat hibakereséshez.
### <a name="run-the-sample-application-in-debug-mode"></a>Futtassa a mintaalkalmazást hibakeresési módban

Futtassa a mintaalkalmazást hibakeresési módban a következő parancs futtatásával:

```bash
gulp run --debug
```

Amikor készen áll a hibakeresési motor, megjelennie `Debugger listening on port 5858` a konzol kimeneti.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Visual Studio Code kapcsolódni a távoli eszköz konfigurálása

1. Nyissa meg a **Debug** a bal oldali panelen.
2. Kattintson a zöld **Start Debugging** (F5) gombra. A Visual Studio Code megnyílik egy `launch.json` fájlt.
3. Frissítés a `launch.json` fájlt az alábbi tartalommal. Cserélje le `[device hostname or IP address]` , a tényleges eszköz IP-címét vagy állomásnevét nevét.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Távoli hibakeresési konfigurálása](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>A távoli alkalmazás csatlakoztatása

Kattintson a zöld **Start Debugging** a hibakeresés (F5) gombra.

Olvasási [Visual STUDIO Code JavaScript](https://code.visualstudio.com/docs/languages/javascript#_debugging) további információt a hibakereső.

![Hibakeresési BLA-minta](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Az Azure CLI-problémák

Az Azure parancssori felület (CLI) preview build. Megoldásokat keres, használhatja a [Preview telepítése útmutató](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Ha az eszköz hibáit, a fájl egy [probléma](https://github.com/Azure/azure-cli/issues) a a **problémák** a GitHub-tárház szakasza.

A Súgó gyakori problémák elhárításához, tekintse meg a [információs](https://github.com/Azure/azure-cli/blob/master/README.rst).

Ha megfelel a "Egy olyan verzióra, amely eleget tesz a követelmény nem található", futtassa a következő parancsot a pip frissítése a legújabb verzióra.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python telepítési problémák

### <a name="legacy-installation-issues-macos"></a>A hagyományos telepítési problémák (macOS)

A pip telepítése, amikor egy engedély hibát vált ki, ha régebbi csomagok vannak telepítve a **su** engedélyek. Ebben az esetben az okozza, hogy a Python brew (macOS) használatával egy korábban elindított telepítése nincs teljesen eltávolítva. A korábbi telepítés egyes pip csomagok létrehozásakor a gyökérszintű, amely azt eredményezi, az engedély hiba. A megoldás, hogy távolítsa el azokat a legfelső szintű telepített csomagokat. Ez a feladat befejezéséhez tegye a következőket:

1. Nyissa meg a következőt: `/usr/local/lib/python2.7/site-packages`
2. Legfelső szintű által létrehozott csomagok:`ls -l | grep root`
3. A 2. lépésben csomagok eltávolításához:`sudo rm -rf {package name}`
4. Telepítse újra a Python.

## <a name="azure-iot-hub-issues"></a>Az Azure IoT Hub-problémák

Ha már sikeresen létesített az Azure IoT hub, az Azure parancssori felület segítségével, és kezelheti az eszközöket, amelyek csatlakozik, az IoT hub eszköz van szüksége, próbálkozzon a következő eszközök.

### <a name="device-explorer"></a>Eszköz Explorer

[Eszköz Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) Windows helyi gépen fut, és az Azure IoT hub csatlakozik. A következő kommunikál [IoT-központok végpontjai](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Az IoT hub regisztrált eszköz Identitáskezelés szeretnék telepíteni és kezelni az eszközöket.
- Eszköz-felhő, figyelheti az IoT hub az eszközről küldött üzenetek fogadása
- Így küldhet üzeneteket az eszközök az IoT hub a felhőből eszközre küldése

Az IoT hub kapcsolati karakterlánc ebből az eszközből készülék képességeinek használatához konfigurálása.

### <a name="iothub-explorer"></a>IOT hubbal-explorer

[IOT hubbal-explorer](https://github.com/Azure/iothub-explorer) egy minta többplatformos parancssori felület eszköz eszközügyfeleken kezelésére. Az eszköz segítségével az identitásjegyzékhez lévő eszközök kezeléséhez, eszköz-a-felhőbe küldött üzeneteket figyelése és felhő eszközparancsok küldése.

Az IOT hubbal-explorer eszköz legújabb (előzetes) verziójának telepítéséhez futtassa a következő parancsot:

```bash
npm install -g iothub-explorer@latest
```

További segítség minden IOT hubbal-explorer parancsot és paramétereket, futtassa a következő parancsot:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Az Azure-portálon

Egy teljes parancssori felület segítségével létrehozhatja és az Azure erőforrások kezeléséhez. Érdemes lehet használni a [Azure-portálon](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) kiépítéséhez, kezeléséhez, és hibakeresése az Azure-erőforrások.

## <a name="azure-storage-issues"></a>Az Azure tárolási problémák

[A Microsoft Azure Tártallózó (előzetes verzió)](http://storageexplorer.com/) egy különálló alkalmazás, a Microsoft, amelyek segítségével a Windows, a macOS és a Linux Azure Storage-adatokkal dolgozni. Ez az eszköz segítségével, a tábla csatlakozhat, és azt az adatok megtekintéséhez. Az eszköz segítségével az Azure Storage problémák elhárításához.
