---
title: Linux-eszközök kiépítése a távoli monitorozáshoz a C-Azure-ban | Microsoft Docs
description: Ismerteti, hogyan csatlakoztatható egy eszköz a távoli figyelési megoldás-gyorsító alkalmazáshoz egy Linuxon futó, C-ben írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454494"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Az eszköz csatlakoztatása a távoli figyelési megoldáshoz (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható valódi eszköz a távoli figyelési megoldáshoz.

Csakúgy, mint a korlátozott eszközökön futó legtöbb beágyazott alkalmazás esetében, a rendszer a C nyelven írja be az eszközhöz tartozó ügyfélszoftvert. Ebben az oktatóanyagban az alkalmazást Ubuntu (Linux) rendszerű gépen hozza létre.

Ha inkább egy eszközt szeretne szimulálni, tekintse meg [az új szimulált eszköz létrehozását és tesztelését](iot-accelerators-remote-monitoring-create-simulated-device.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez szüksége lesz egy, az Ubuntu 15,04-es vagy újabb verzióját futtató eszközre. A továbblépés előtt [állítsa be a Linux fejlesztési környezetét](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>A kód megtekintése

Az útmutatóban használt [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) az Azure IoT C SDK GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>Töltse le a forráskódot, és készítse elő a projektet

A projekt előkészítéséhez klónozott vagy töltse le az [Azure IoT C SDK-tárházat](https://github.com/Azure/azure-iot-sdk-c) a githubról.

A minta a Samples **/Solutions/remote_monitoring_client** mappában található.

Nyissa meg a **remote_monitoring. c** fájlt a **Samples/Solutions/remote_monitoring_client** mappában egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

A következő lépések azt ismertetik, hogyan lehet a *CMAK* használatával felépíteni az ügyfélalkalmazás alkalmazást. A távoli figyelési ügyfélalkalmazás az SDK létrehozási folyamatának részeként készült.

1. Szerkessze a **remote_monitoring. c** fájlt úgy, hogy a lecserélje `<connectionstring>` a útmutató elején megjegyzett eszköz-kapcsolódási karakterláncot, amikor hozzáad egy eszközt a megoldás-gyorsító eszközhöz.

1. Navigáljon az [Azure IoT C SDK adattár](https://github.com/Azure/azure-iot-sdk-c) -tárház klónozott példányának gyökeréhez, és futtassa a következő parancsokat az ügyfélalkalmazás létrehozásához:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Futtassa az ügyfélalkalmazás alkalmazást, és küldjön telemetria a IoT Hubba:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    A konzol az alábbiak szerint jeleníti meg az üzeneteket:

    - Az alkalmazás minta-telemetria küld a megoldás-gyorsító számára.
    - Válaszol a megoldás irányítópultján meghívott metódusokra.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
