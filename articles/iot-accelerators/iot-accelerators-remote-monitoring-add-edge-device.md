---
title: Távoli figyelési megoldás hozzáadása Edge-eszköz - Azure | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan adhat hozzá IoT Edge-eszközt egy távoli figyelési megoldásgyorsítóhoz
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965373"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>IoT Edge-eszköz hozzáadása a távfigyelési megoldás gyorsítóhoz

[Ha IoT Edge-eszközt](../iot-edge/about-iot-edge.md) szeretne hozzáadni a megoldásgyorsítóhoz, hajtsa végre az alábbi két lépést:

1. Adja hozzá az Edge-eszközt az **Eszközkezelő** lapon a Távfigyelési megoldásgyorsító webes felhasználói felületén.
1. Telepítse az IoT Edge futásidejű az Edge-eszközön.

## <a name="add-the-iot-edge-device"></a>Az IoT Edge-eszköz hozzáadása

Ha IoT Edge-eszközt szeretne hozzáadni a távfigyelési megoldás gyorsítójához, keresse meg az **Eszközkezelő** lapot a webes felhasználói felületen, és kattintson a **+ Új eszköz gombra.**

Az **Új eszköz** panelen válassza az **IoT Edge-eszköz**lehetőséget. A többi beállítás alapértelmezett értékeit is meghagyhatja. Ezután kattintson az **Apply** (Alkalmaz) gombra:

![IoT Edge-eszköz hozzáadása](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>IoT Edge-eszköz hozzáadásának alternatív módjai

Az IoT Edge-eszköz regisztrálása közvetlenül az IoT Hub-példány a megoldásgyorsítóban. Az alábbi útmutatók követése előtt ismernie kell az IoT hub nevét a megoldásgyorsítóban:

- [Új Azure IoT Edge-eszköz regisztrálása az Azure Portalról](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Új Azure IoT Edge-eszköz regisztrálása a Visual Studio-kódból](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Ha közvetlenül az IoT-központtal regisztrál egy eszközt a távoli figyelési megoldásgyorsítóban, az megjelenik a webes felhasználói felület **Eszközkezelő** lapján.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge futásidejű telepítése

Mielőtt üzembe helyezheti a modulokat az Edge-eszközre, telepítenie kell az IoT Edge futásidejű a valós eszközön. Az alábbi útmutatóútmutatók bemutatják, hogyan telepítheti a futásidejűt a közös eszközplatformokra:

- [Az Azure IoT Edge futásidejű telepítésélinuxos (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Az Azure IoT Edge futásidejének telepítése Linuxon (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Telepítse az Azure IoT Edge futásidejét a Windows rendszeren a Windows-tárolókkal való használatra](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Telepítse az Azure IoT Edge futásidejét a Windows-ra linuxos tárolókkal való használatra](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Az IoT Edge futásidejű telepítéséwindowsos IoT Core-ra](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>További lépések

Most, hogy elkészítette az IoT Edge-eszközt, a következő lépés a modulok üzembe helyezése. Lásd: [IoT Edge-csomag importálása a távoli figyelési megoldás gyorsítójába](iot-accelerators-remote-monitoring-import-edge-package.md)
