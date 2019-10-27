---
title: Az eszközök kapcsolatának figyelése az Azure IoT Central Explorer használatával
description: Az eszközök üzeneteinek figyelése és az eszköz két módosításának megfigyelése az IoT Central Explorer parancssori felületén keresztül.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ec3ccc0a9fa6d1ae5d6193aacd1b068f2d97afe0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949611"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Az eszközök kapcsolatának figyelése az Azure IoT Central Explorer használatával

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

Az IoT Central Explorer CLI használatával megtekintheti, hogy az eszközök elküldik-e a IoT Central és megfigyelheti a IoT Hub Twin-ben történt módosításokat. Ezzel a nyílt forráskódú eszközzel mélyebb betekintést nyerhet az eszközök kapcsolatának állapotával, és diagnosztizálhatja azokat az üzeneteket, amelyek nem érik el a felhőt, vagy amelyek nem válaszolnak a kettős módosításokra.

[Látogasson el az IOTC-Explorer adattárba a GitHubon.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Előfeltételek

+ Node. js 8. x vagy újabb verzió – https://nodejs.org
+ Az alkalmazás rendszergazdájának elő kell állítania egy hozzáférési jogkivonatot az IOTC-Explorerben való használatra.

## <a name="install-iotc-explorer"></a>Az IOTC telepítése – Explorer

Futtassa a következő parancsot a parancssorból a telepítéshez:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Az install parancsot általában UNIX-szerű környezetekben `sudo` kell futtatnia.

## <a name="run-iotc-explorer"></a>Az IOTC – Explorer futtatása

A következő szakaszok a `iotc-explorer`futtatásakor használható általános parancsokat és beállításokat ismertetik. A parancsok és beállítások teljes készletének megtekintéséhez adja át `--help` `iotc-explorer` vagy annak alparancsainak bármelyikét.

### <a name="login"></a>Bejelentkezés

Mielőtt elkezdené, rendelkeznie kell a IoT Central-alkalmazás rendszergazdájával, hogy hozzáférési jogkivonatot kapjon a használathoz. A rendszergazda a következő lépéseket hajtja végre:

1. Navigáljon az **Adminisztráció** elemre, majd a **hozzáférési tokenek**elemre.
1. Válassza a **jogkivonat előállítása**lehetőséget.
    ![hozzáférési jogkivonat lap képernyőképe](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Adja meg a jogkivonat nevét, válassza a **tovább**, majd a **Másolás**elemet.
    > [!NOTE]
    > A jogkivonat értéke csak egyszer jelenik meg, ezért a párbeszédpanel bezárása előtt át kell másolni. A párbeszédpanel bezárása után soha nem jelenik meg többé.

    ![Hozzáférési token másolása párbeszédpanel képernyőképe](media/howto-use-iotc-explorer/copyaccesstoken.png)

A token használatával a következő módon jelentkezhet be a CLI-be:

```cmd/sh
iotc-explorer login "<Token value>"
```

Ha azt szeretné, hogy a jogkivonat ne legyen megőrzött a rendszerhéj előzményeiben, hagyja ki a tokent, és adja meg azt, ha a rendszer kéri:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Eszköz üzeneteinek figyelése

A `monitor-messages` parancs használatával megtekintheti az adott eszközről vagy az alkalmazás összes eszközéről érkező üzeneteket. Ez a parancs egy figyelőt indít el, amely folyamatosan megjeleníti az új üzeneteket, amint megérkeznek:

Az alkalmazás összes eszközének megtekintéséhez futtassa a következő parancsot:

```cmd/sh
iotc-explorer monitor-messages
```

Kimenet:

![figyelő – üzenetek parancs kimenete](media/howto-use-iotc-explorer/monitormessages.png)

Egy adott eszköz megtekintéséhez csak adja hozzá az eszköz AZONOSÍTÓját a parancs végéhez:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

A `--raw` beállítás hozzáadásával a parancshoz hozzáadhatja a számítógép-barát formátumot is:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Eszköz dupla beolvasása

A `get-twin` parancs használatával lekérheti a Twin fájl tartalmát egy IoT Central eszközhöz. Ehhez futtassa a következő parancsot:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Kimenet:

![a Get-Twin parancs kimenete](media/howto-use-iotc-explorer/getdevicetwin.png)

A `monitor-messages`hoz hasonlóan a `--raw` beállítás megadásával több géppel is megtekinthető kimenetet kaphat:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a IoT Central Explorer használatát, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices.md)megismerése IoT Central.
