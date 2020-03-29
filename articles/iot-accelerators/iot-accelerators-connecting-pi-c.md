---
title: Raspberry Pi kiépítése a távoli figyeléshez C használatával - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként csatlakoztatható a Raspberry Pi-eszköz a távfigyelési megoldás gyorsítóhoz a C-ben írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454499"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>A Raspberry Pi eszköz csatlakoztatása a távfigyelési megoldás gyorsítóhoz (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat egy valódi eszközt a távoli figyelési megoldás gyorsítóhoz. A legtöbb beágyazott alkalmazáshoz, amelyek korlátozott eszközökön futnak, a Raspberry Pi eszközalkalmazás ügyfélkódja C-ben van megírva. Ebben az oktatóanyagban az alkalmazást a Raspbian operációs rendszert futtató Raspberry Pi-re építi fel.

Ha egy eszközt szeretne szimulálni, olvassa el az [Új szimulált eszköz létrehozása és tesztelése című témakört.](iot-accelerators-remote-monitoring-create-simulated-device.md)

### <a name="required-hardware"></a>Szükséges hardver

Asztali számítógép, amely lehetővé teszi, hogy távolról csatlakozzon a Raspberry Pi parancssorához.

[Microsoft IoT Starter Kit Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy azzal egyenértékű összetevőkhöz. Ez az oktatóanyag a következő elemeket használja a készletből:

- Raspberry Pi, 3.
- MicroSD-kártya (NOOBS-szal)
- USB Mini kábel
- Ethernet kábel

### <a name="required-desktop-software"></a>Szükséges asztali szoftver

SSH-ügyfélre van szüksége az asztali gépen, hogy távolról elérhesse a Raspberry Pi parancssorát.

- A Windows nem tartalmaz SSH-ügyfelet. A [PuTTY](https://www.putty.org/)használatát javasoljuk.
- A legtöbb Linux disztribúció és a Mac OS tartalmazza a parancssori SSH segédprogramot. További információ: [SSH Linux vagy Mac OS használatával.](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)

### <a name="required-raspberry-pi-software"></a>Szükséges Raspberry Pi szoftver

Ez a cikk feltételezi, hogy telepítette a Raspbian operációs rendszer legújabb verzióját [a Raspberry Pi készülékre.](https://www.raspberrypi.org/learning/software-guide/quickstart/)

A következő lépések bemutatják, hogyan készítheti elő a Raspberry Pi-t a megoldásgyorsítóhoz csatlakozó C-alkalmazás létrehozásához:

1. Csatlakozzon a Raspberry Pi segítségével **ssh**. További információ: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a [Raspberry Pi webhelyén.](https://www.raspberrypi.org/)

1. A Raspberry Pi frissítéséhez használja a következő parancsot:

    ```sh
    sudo apt-get update
    ```

1. Az útmutató ban leírt lépések végrehajtásához kövesse a [Linux-fejlesztői környezet beállításának](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) lépéseit a szükséges fejlesztői eszközök és kódtárak hozzáadásához a Raspberry Pi-hez.

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
