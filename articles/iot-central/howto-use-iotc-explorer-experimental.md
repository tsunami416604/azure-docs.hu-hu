---
title: Az Azure IoT Central Explorer használatával eszközkapcsolatok figyelése
description: Figyelheti az eszközre, és tekintse meg az eszközön ikereszköz megváltoztatja az IoT Central Explorer CLI-n keresztül.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 167a7b008af521c5f4e403a3786b004138d69da9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311151"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Az Azure IoT Central Explorer használatával eszközkapcsolatok figyelése

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Az IoT Central Explorer CLI segítségével megtekintheti az eszközök IoT-központ küldi, és vizsgálja meg az IoT Hub ikereszköz változásai üzeneteket. A nyílt forráskódú eszköz segítségével mélyebb betekintést az eszköz kapcsolati állapotát, és diagnosztizálhatja a problémákat, az eszköz üzenetek nem éri el a felhőben vagy az eszköz nem válaszol az ikereszköz módosításokat.

[Látogasson el a iotc-explorer tárházban a Githubon.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Előfeltételek

+ NODE.js-verzió 8.x vagy újabb - https://nodejs.org
+ Az alkalmazás az rendszergazdájának kell, hogy a iotc-explorer hozzáférési jogkivonat létrehozásához.

## <a name="install-iotc-explorer"></a>Iotc-kezelő telepítése

Futtassa a következő parancsot a parancssorból történő telepítéséhez:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Jellemzően futtatnia kell a telepítési parancs `sudo` Unix-hoz hasonló környezetekben.

## <a name="run-iotc-explorer"></a>Iotc-kezelő futtatása

A következő szakaszok ismertetik a gyakori parancsok és beállítások futtatásakor használható `iotc-explorer`. A teljes körű parancsok és beállítások megtekintéséhez adjon át `--help` való `iotc-explorer` vagy az alparancsok.

### <a name="login"></a>Bejelentkezés

Mielőtt kezdheti, szüksége lesz az IoT-központ alkalmazás használatra hozzáférési jogkivonatot kapjon a rendszergazda. A rendszergazda hajtja végre az alábbi lépéseket:

1. Navigáljon a **felügyeleti** majd **hozzáférési jogkivonatokat**.
1. Válassza ki **jogkivonatot**.
    ![Hozzáférési jogkivonat lap képernyőképe](media/howto-use-iotc-explorer-experimental/accesstokenspage.png)

1. Adja meg a jogkivonat nevét, válassza ki **tovább**, majd **másolási**.
    > [!NOTE]
    > A jogkivonat értéke csak akkor jelenik meg egyszer, a párbeszédpanel bezárása előtt lehet másolni. A párbeszédpanel bezárása, után ez soha nem jelenik meg újból.

    ![Másolja a hozzáférési jogkivonat párbeszédpanel képernyőképe](media/howto-use-iotc-explorer-experimental/copyaccesstoken.png)

A jogkivonat segítségével jelentkezzen be a parancssori felület a következőképpen:

```cmd/sh
iotc-explorer login "<Token value>"
```

Ha inkább nem rendelkezik a token rendszerhéj előzményekben megőrzött, hagyhatja, hogy a jogkivonat útvonalakat, és Ehelyett adja meg, amikor a rendszer kéri:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>A figyelő eszközüzenetek

Megtekintheti az egy adott eszköz vagy az alkalmazást a lévő összes eszközre érkező üzeneteket a `monitor-messages` parancsot. Ez a parancs elindítja egy figyelő, amely folyamatosan új üzenetek érkezésekor:

Tekintse meg az alkalmazás összes eszközt, futtassa a következő parancsot:

```cmd/sh
iotc-explorer monitor-messages
```

Kimenet:

![a figyelő-üzenetek parancs kimenete](media/howto-use-iotc-explorer-experimental/monitormessages.png)

Tekintse meg egy adott eszközre, csak adja hozzá az eszköz azonosítója, a parancs végén:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Több gép mobilbarát formátum hozzáadásával is készíthető a `--raw` lehetőség a következő paranccsal:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Ikereszköz beolvasása

Használhatja a `get-twin` parancsot az IoT-központ eszköz az ikereszköz tartalmának lekérése. Ehhez futtassa a következő parancsot:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Kimenet:

![Get-twin parancs kimenete](media/howto-use-iotc-explorer-experimental/getdevicetwin.png)

A `monitor-messages`, egy további gép mobilbarát átadásával kimeneti beszerezheti a `--raw` lehetőséget:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az IoT Central kezelő, a javasolt következő lépésre megismeréséhez [IoT Central-eszközök kezelésének](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
