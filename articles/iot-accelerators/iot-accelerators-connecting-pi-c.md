---
title: Málna PI kiépítése a távoli Monitorozásba a C-Azure használatával | Microsoft Docs
description: Leírja, hogyan csatlakoztatható egy málna PI-eszköz a távoli figyelési megoldáshoz a C-ben írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454499"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>A málna PI-eszköz csatlakoztatása a távoli figyelési megoldáshoz (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható valódi eszköz a távoli figyelési megoldáshoz. Csakúgy, mint a korlátozott eszközökön futó legtöbb beágyazott alkalmazás esetében, a málna PI-eszközhöz tartozó ügyfél kódja C-ben van megírva. Ebben az oktatóanyagban az alkalmazást egy Raspbian operációs rendszert futtató málna PI-ben hozza létre.

Ha inkább egy eszközt szeretne szimulálni, tekintse meg [az új szimulált eszköz létrehozását és tesztelését](iot-accelerators-remote-monitoring-create-simulated-device.md)ismertető témakört.

### <a name="required-hardware"></a>Szükséges hardver

Egy asztali számítógép, amely lehetővé teszi, hogy távolról kapcsolódjon a málna PI parancssorához.

[Microsoft IoT Starter Kit málna PI 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy azzal egyenértékű összetevőkhöz. Ez az oktatóanyag a készlet következő elemeit használja:

- Málna PI 3
- MicroSD-kártya (NOOBs)
- Egy USB-s mini-kábel
- Egy Ethernet-kábel

### <a name="required-desktop-software"></a>Szükséges asztali szoftverek

Az asztali gépen szükség van az SSH-ügyfélre, hogy távolról hozzáférhessen a parancssorhoz a málna PI-on.

- A Windows nem tartalmaz SSH-ügyfelet. A [Putty](https://www.putty.org/)használatát javasoljuk.
- A legtöbb Linux-disztribúció és Mac OS tartalmazza a parancssori SSH-segédprogramot. További információ: [SSH Linux vagy Mac os használatával](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Szükséges málna PI szoftver

Ez a cikk azt feltételezi, hogy telepítette a [Raspbian operációs rendszer](https://www.raspberrypi.org/learning/software-guide/quickstart/)legújabb verzióját a málna PI-ra.

A következő lépések bemutatják, hogyan készítheti elő a málna PI-t egy olyan C-alkalmazás létrehozásához, amely a megoldás-gyorsító csatlakozik:

1. Kapcsolódjon a málna PI-hez az **SSH**használatával. További információ: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a [málna PI webhelyén](https://www.raspberrypi.org/).

1. A következő parancs használatával frissítheti a málna PI-t:

    ```sh
    sudo apt-get update
    ```

1. A útmutató lépéseinek végrehajtásához kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) című témakör lépéseit a szükséges fejlesztői eszközök és kódtárak a málna PI-hez való hozzáadásához.

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
