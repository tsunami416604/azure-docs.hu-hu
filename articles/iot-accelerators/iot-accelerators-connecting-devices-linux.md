---
title: Linux-eszközök kiépítése a távfigyelő szolgáltatásba C - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként csatlakoztatható egy eszköz a távfigyelési megoldás gyorsítóhoz a Linux on futó C-ben írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454494"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Csatlakoztassa az eszközt a távfelügyeleti megoldásgyorsítóhoz (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat egy valódi eszközt a távoli figyelési megoldás gyorsítóhoz.

A legtöbb korlátozott eszközön futó beágyazott alkalmazáshoz ugyanúgy, mint az eszközalkalmazás ügyfélkódja C-ben íródik. Ebben az oktatóanyagban az alkalmazást egy Ubuntu (Linux) rendszert futtató gépen építi fel.

Ha egy eszközt szeretne szimulálni, olvassa el az [Új szimulált eszköz létrehozása és tesztelése című témakört.](iot-accelerators-remote-monitoring-create-simulated-device.md)

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához egy Ubuntu 15.04-es vagy újabb verzióját futtató eszközre van szükség. Mielőtt folytatná, [állítsa be a Linux fejlesztői környezetet.](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)

## <a name="view-the-code"></a>A kód megtekintése

Az ebben az útmutatóban használt [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) az Azure IoT C SDKs GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>A forráskód letöltése és a projekt előkészítése

A projekt előkészítése, klónozza vagy töltse le az [Azure IoT C SDKs tárház](https://github.com/Azure/azure-iot-sdk-c) a GitHubról.

A minta a **minták/megoldások/remote_monitoring_client** mappában található.

Nyissa meg a **remote_monitoring.c** fájlt a **minta/megoldások/remote_monitoring_client** mappában egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Az alábbi lépések azt ismertetik, hogyan használható a *CMake* az ügyfélalkalmazás létrehozásához. A távoli figyelési ügyfélalkalmazás az SDK létrehozási folyamatának részeként épül fel.

1. Az útmutató elején észlelt eszközkapcsolati karakterláncra cserélheti `<connectionstring>` a **remote_monitoring.c** fájlt, amikor eszközt adott a megoldásgyorsítóhoz.

1. Keresse meg az [Azure IoT C SDKs tárház](https://github.com/Azure/azure-iot-sdk-c) klónozott példányának gyökerét, és futtassa a következő parancsokat az ügyfélalkalmazás létrehozásához:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Futtassa az ügyfélalkalmazást, és küldje el a telemetriai adatokat az IoT Hubnak:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    A konzol az üzeneteket a következőképpen jeleníti meg:

    - Az alkalmazás minta telemetriát küld a megoldásgyorsító.
    - A megoldás irányítópultjáról meghívott metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
