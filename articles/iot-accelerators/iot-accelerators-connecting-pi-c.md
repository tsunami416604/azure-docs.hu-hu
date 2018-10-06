---
title: Raspberry Pi távoli figyelési C – az Azure használatával üzembe helyezése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy Raspberry Pi-eszköz csatlakoztatása a távoli figyelési megoldásgyorsító c nyelven írt alkalmazás használatával
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: c20b1d5f3a84e950e37a3236272256db620a5985
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831100"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>A Raspberry Pi-eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító. Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az ügyfélkód a Raspberry Pi eszköz alkalmazás betöltőprogramot c-hez Ebben az oktatóanyagban hozza létre a Raspbian operációs rendszert futtató az alkalmazást egy Raspberry Pi-on.

### <a name="required-hardware"></a>Szükséges hardver

Asztali számítógép ahhoz, hogy távolról csatlakozhat a parancssorban a Raspberry Pi-on.

[A Microsoft IoT-Kezdőcsomag a Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy ezzel egyenértékű összetevőket. Ebben az oktatóanyagban a csomag a következő cikkeket:

- Raspberry pi 3 –
- (A NOOBS) MicroSD-kártyán
- Egy USB Mini-kábellel
- Egy Ethernet-kábelek

### <a name="required-desktop-software"></a>Szükséges asztali szoftverek

SSH-ügyfelet kell ahhoz, hogy a parancssor a Raspberry Pi-on érheti el távolról asztali gépén.

- Windows nem tartalmazza az SSH-ügyfelet. Azt javasoljuk, [PuTTY](http://www.putty.org/).
- A legtöbb Linux-disztribúciók és Mac OS tartalmazza az SSH parancssori segédprogramot. További információkért lásd: [SSH használatával Linux vagy Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Raspberry Pi szükséges szoftverek

Ez a cikk feltételezi, hogy telepítette a legújabb verzióját a [Raspbian operációs rendszer, a Raspberry Pi-on](https://www.raspberrypi.org/learning/software-guide/quickstart/).

A következő lépések bemutatják, hogyan készíti elő a Raspberry Pi a C alkalmazás, amely kapcsolódik a megoldásgyorsító létrehozásához:

1. Csatlakozzon a Raspberry Pi **ssh**. További információkért lásd: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a a [Raspberry Pi webhely](https://www.raspberrypi.org/).

1. A következő paranccsal a Raspberry Pi frissítése:

    ```sh
    sudo apt-get update
    ```

1. Végezze el ez az Útmutató lépéseit kövesse a [a Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) a szükséges fejlesztői eszközök és kódtárak hozzáadása a Raspberry Pi.

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
