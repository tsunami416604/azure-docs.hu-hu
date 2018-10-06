---
title: Távoli figyelési C – az Azure Linux rendszerű eszközök kiépítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy eszköz csatlakoztatása a távoli figyelési megoldásgyorsító a linuxon futó C nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 5faa91f054e62e2b3d9d317efe57f2d3f659cee6
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829834"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító.

Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az Ügyfélkód az eszköz alkalmazás írt c-hez Ebben az oktatóanyagban hozza létre az alkalmazást egy Ubuntu (Linux) rendszert futtató gépen.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége 15.04 vagy újabb verzió Ubuntu-es eszközök. A folytatás előtt [a Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>A kód megtekintéséhez

A [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) használja a jelen útmutató az Azure IoT C SDK-k GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>Letöltheti a forráskódot, és készítse elő a projekt

A projekt elkészítéséhez Klónozás vagy letöltés a [Azure IoT C SDK-k tárház](https://github.com/Azure/azure-iot-sdk-c) a Githubról.

A mintában található a **samples/megoldások/remote_monitoring_client** mappát.

Nyissa meg a **remote_monitoring.c** fájlt a **samples/megoldások/remote_monitoring_client** mappát egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Az alábbi lépések bemutatják, hogyan használható *CMake* ügyfélalkalmazás hozhat létre. A távoli figyelési ügyfélalkalmazás az SDK-val készült a buildelési folyamat részeként.

1. Szerkessze a **remote_monitoring.c** fájlban cserélje le `<connectionstring>` az eszköz kapcsolati karakterlánccal feljegyzett elején. Ez az útmutató egy eszközt a megoldásgyorsító való felvételekor.

1. Klónozott másolatának gyökérkönyvtárában nyissa meg a [Azure IoT C SDK-k tárház](https://github.com/Azure/azure-iot-sdk-c) tárházat, és futtassa a következő parancsokat az ügyfél-alkalmazás létrehozásához:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Futtassa az ügyfélalkalmazást és telemetriát küldjön az IoT hubnak:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    A konzolon, üzeneteket jelenít meg:

    - Az alkalmazás minta telemetriai adatokat küld a megoldásgyorsító.
    - A megoldás irányítópultjáról indított metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
