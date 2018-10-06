---
title: Távoli figyelési C – az Azure Windows-eszköz kiépítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy eszköz csatlakoztatása a távoli figyelési megoldásgyorsító futó Windows C nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 55c8ff799ba3ff7fe9691d46dc90a00d5182d390
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829410"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító.

Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az Ügyfélkód az eszköz alkalmazás írt c-hez Ebben az oktatóanyagban létrehozhat egy Windows rendszert futtató gépen az eszközügyfél-alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

Végezze el ez az Útmutató lépéseit kövesse a [a Windows fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) a szükséges fejlesztői eszközök és kódtárak hozzáadása a Windows-gépen.

## <a name="view-the-code"></a>A kód megtekintéséhez

A [mintakód](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) használja a jelen útmutató az Azure IoT C SDK-k GitHub-tárházban érhető el.

### <a name="download-the-source-code-and-prepare-the-project"></a>Letöltheti a forráskódot, és készítse elő a projekt

A projekt elkészítéséhez Klónozás vagy letöltés a [Azure IoT C SDK-k tárház](https://github.com/Azure/azure-iot-sdk-c) a Githubról.

A mintában található a **samples/megoldások/remote_monitoring_client** mappát.

Nyissa meg a **remote_monitoring.c** fájlt a **samples/megoldások/remote_monitoring_client** mappát egy szövegszerkesztőben.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Szerkessze a **remote_monitoring.c** fájlban cserélje le `<connectionstring>` az eszköz kapcsolati karakterlánccal feljegyzett elején. Ez az útmutató egy eszközt a megoldásgyorsító való felvételekor.

1. Kövesse a [hozhat létre az C SDK-t Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) hozhat létre az SDK-t és a távoli figyelési ügyfélalkalmazás.

1. A parancssor a megoldás felépítéséhez használt futtassa a következő:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    A konzolon, üzeneteket jelenít meg:

    - Az alkalmazás minta telemetriai adatokat küld a megoldásgyorsító.
    - A megoldás irányítópultjáról indított metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
