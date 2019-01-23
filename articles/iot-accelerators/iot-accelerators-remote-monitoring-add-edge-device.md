---
title: Távoli megfigyelési megoldás hozzáadása az Edge-eszköz – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a távoli figyelési megoldásgyorsító IoT Edge-eszköz hozzáadása
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 5172ba9881f80e202bdc69689e67befb79101dc0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450660"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító IoT Edge-eszköz hozzáadása

Hozzáadása egy [IoT Edge](../iot-edge/about-iot-edge.md) eszközt, hogy a megoldásgyorsító, hajtsa végre a következő lépéseket:

1. Az Edge-eszköz hozzáadásához kattintson a **eszközök** oldal a távoli figyelési megoldás gyorsító webes felhasználói felületen.
1. Telepítse az IoT Edge-futtatókörnyezet az Edge-eszköz.

## <a name="add-the-iot-edge-device"></a>Az IoT Edge-eszköz hozzáadása

A távoli figyelési megoldásgyorsító IoT Edge-eszköz hozzáadásához lépjen a **eszközök** a webes felhasználói felületen oldalra, majd kattintson a **+ új eszköz**.

Az a **új eszköz** panel, válassza a **IoT Edge-eszköz**. Hagyhatja az alapértelmezett értékeket a többi beállítás esetében. Ezután kattintson az **Apply** (Alkalmaz) gombra:

![IoT Edge-eszköz hozzáadása](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Adja hozzá az IoT Edge-eszköz egyéb módjai

Akkor is közvetlenül a megoldásgyorsító az IoT Hub-példány az IoT Edge-eszköz regisztrálása. Érdemes tudni a megoldásgyorsító az IoT hub nevére, mielőtt elvégezné az alábbi útmutatókat:

- [Az Azure Portalról egy új Azure IoT Edge-eszköz regisztrálása](../iot-edge/how-to-register-device-portal.md)
- [Egy új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](../iot-edge/how-to-register-device-cli.md)
- [A Visual Studio Code-ból egy új Azure IoT Edge-eszköz regisztrálása](../iot-edge/how-to-register-device-vscode.md)

Amikor regisztrál egy eszközt a távoli figyelési megoldásgyorsító IoT hubbal, az megjelenik a **eszközök** oldal a webes felhasználói felületen.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Modulok az Edge-eszközön telepíteni, telepítenie kell az IoT Edge-futtatókörnyezet a valódi eszközön. A következő útmutatók bemutatják, hogyan telepítheti a futtatókörnyezet közös eszközplatformok:

- [Telepítse az Azure IoT Edge-futtatókörnyezet (x64) linuxon](../iot-edge/how-to-install-iot-edge-linux.md)
- [Telepítse az Azure IoT Edge-futtatókörnyezet (ARM32v7/armhf) Linux rendszeren](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Az Azure IoT Edge-futtatókörnyezet telepíthető Windows használható a Windows-tárolókkal](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Telepítse az Azure IoT Edge-modul Windows, Linux-tárolók használata](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Az IoT Edge-futtatókörnyezet telepítéséhez Windows IoT Core-on](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>További lépések

Most, hogy előkészítette az IoT Edge-eszköz, a következő lépés az modulokkal történő üzembe helyezéséhez. Lásd: [egy IoT Edge-csomag importálása a távoli figyelési megoldásgyorsító](iot-accelerators-remote-monitoring-import-edge-package.md)
