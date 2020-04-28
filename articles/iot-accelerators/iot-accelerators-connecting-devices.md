---
title: Windows-eszközök kiépítése a távoli monitorozáshoz a C-Azure-ban | Microsoft Docs
description: Ismerteti, hogyan csatlakoztatható egy eszköz a távoli figyelési megoldáshoz a Windows rendszeren futó C-ben írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "61450218"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Az eszköz csatlakoztatása a távoli figyelési megoldáshoz (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható valódi eszköz a távoli figyelési megoldáshoz.

Csakúgy, mint a korlátozott eszközökön futó legtöbb beágyazott alkalmazás esetében, a rendszer a C nyelven írja be az eszközhöz tartozó ügyfélszoftvert. Ebben az oktatóanyagban a Windows rendszerű gépen hozza létre az eszköz-ügyfélalkalmazás alkalmazást.

Ha inkább egy eszközt szeretne szimulálni, tekintse meg [az új szimulált eszköz létrehozását és tesztelését](iot-accelerators-remote-monitoring-create-simulated-device.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek végrehajtásához kövesse a [Windows fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) a szükséges fejlesztői eszközök és kódtárak a Windows rendszerű géphez való hozzáadásának lépéseit.

## <a name="view-the-code"></a>A kód megtekintése

Az útmutatóban használt [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) az Azure IoT C SDK GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>Töltse le a forráskódot, és készítse elő a projektet

A projekt előkészítéséhez [klónozott az Azure IoT C SDK-tárházat](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) a githubról.

A minta a Samples **/Solutions/remote_monitoring_client** mappában található.

Nyissa meg a **remote_monitoring. c** fájlt a **Samples/Solutions/remote_monitoring_client** mappában egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Szerkessze a **remote_monitoring. c** fájlt úgy `<connectionstring>` , hogy a lecserélje a útmutató elején megjegyzett eszköz-kapcsolódási karakterláncot, amikor hozzáad egy eszközt a megoldás-gyorsító eszközhöz.

1. Az SDK és a távoli figyelés ügyfélalkalmazás létrehozásához kövesse a [C SDK létrehozása a Windowsban](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) című témakör lépéseit.

1. A megoldás létrehozásához használt parancssorban futtassa a következőt:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    A konzol az alábbiak szerint jeleníti meg az üzeneteket:

    - Az alkalmazás minta-telemetria küld a megoldás-gyorsító számára.
    - Válaszol a megoldás irányítópultján meghívott metódusokra.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
