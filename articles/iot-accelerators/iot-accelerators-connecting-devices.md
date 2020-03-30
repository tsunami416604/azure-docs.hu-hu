---
title: Windows-eszközök kiépítése távfelügyeletre C - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként csatlakoztatható eszköz a távfigyelési megoldás gyorsítójához a Windows rendszeren futó C nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450218"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Az eszköz csatlakoztatása a Távfigyelési megoldás gyorsítójával (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat egy valódi eszközt a távoli figyelési megoldás gyorsítóhoz.

A legtöbb korlátozott eszközön futó beágyazott alkalmazáshoz ugyanúgy, mint az eszközalkalmazás ügyfélkódja C-ben íródik. Ebben az oktatóanyagban az eszközügyfél-alkalmazást egy Windows rendszert futtató gépen építi fel.

Ha egy eszközt szeretne szimulálni, olvassa el az [Új szimulált eszköz létrehozása és tesztelése című témakört.](iot-accelerators-remote-monitoring-create-simulated-device.md)

## <a name="prerequisites"></a>Előfeltételek

Az útmutató ban leírt lépések végrehajtásához kövesse a [Windows fejlesztői környezet beállításának](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) lépéseit a szükséges fejlesztői eszközök és tárak Windows-géphez való hozzáadásához.

## <a name="view-the-code"></a>A kód megtekintése

Az ebben az útmutatóban használt [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) az Azure IoT C SDKs GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>A forráskód letöltése és a projekt előkészítése

A projekt előkészítése, [klónozza az Azure IoT C SDKtár a](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) GitHubról.

A minta a **minták/megoldások/remote_monitoring_client** mappában található.

Nyissa meg a **remote_monitoring.c** fájlt a **minta/megoldások/remote_monitoring_client** mappában egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Az útmutató elején észlelt eszközkapcsolati karakterláncra cserélheti `<connectionstring>` a **remote_monitoring.c** fájlt, amikor eszközt adott a megoldásgyorsítóhoz.

1. Kövesse a [C SDK létrehozása a Windows rendszerben](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) az SDK és a távoli figyelési ügyfélalkalmazás létrehozásához című lépéseit.

1. A megoldás létrehozásához használt parancssorban futtassa a következő parancsot:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    A konzol az üzeneteket a következőképpen jeleníti meg:

    - Az alkalmazás minta telemetriát küld a megoldásgyorsító.
    - A megoldás irányítópultjáról meghívott metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
