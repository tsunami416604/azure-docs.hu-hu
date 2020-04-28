---
title: Távoli figyelési megoldás Edge-eszköz hozzáadása – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá IoT Edge eszközt egy távoli figyelési megoldás-gyorsító eszközhöz
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "72965373"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>IoT Edge-eszköz hozzáadása a távoli figyelési megoldáshoz – gyorssegéd

Ha [IoT Edge](../iot-edge/about-iot-edge.md) eszközt szeretne hozzáadni a megoldás-gyorsító eszközhöz, hajtsa végre a következő két lépést:

1. Adja hozzá a peremhálózati eszközt a távoli figyelési megoldás webes felhasználói felületének **Device Explorer** lapján.
1. Telepítse a IoT Edge futtatókörnyezetet a peremhálózati eszközén.

## <a name="add-the-iot-edge-device"></a>A IoT Edge eszköz hozzáadása

Ha IoT Edge eszközt szeretne hozzáadni a távoli figyelési megoldás-gyorsító eszközhöz, navigáljon a webes felületen található **Device Explorer** lapra, és kattintson az **+ új eszköz**elemre.

Az **új eszköz** panelen válassza a **IoT Edge eszköz**elemet. A többi beállítás alapértelmezett értékeit is meghagyhatja. Ezután kattintson az **Apply** (Alkalmaz) gombra:

![IoT Edge eszköz hozzáadása](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>IoT Edge-eszköz hozzáadásának alternatív módjai

IoT Edge eszköz közvetlenül is regisztrálható a megoldás-gyorsító IoT Hub példányával. Az alábbi útmutató-útmutatók követése előtt ismernie kell az IoT hub nevét a megoldás-gyorssegédben:

- [Új Azure IoT Edge-eszköz regisztrálása a Azure Portal](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Új Azure IoT Edge-eszköz regisztrálása az Azure CLI-vel](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Új Azure IoT Edge eszköz regisztrálása a Visual Studio Code-ból](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Ha közvetlenül regisztrálja az eszközt az IoT hub-ban a távoli figyelési megoldás-gyorsító eszközben, a rendszer a webes felhasználói felület **Device Explorer** oldalán találja.

## <a name="install-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet telepítése

Mielőtt üzembe helyezi a modulokat a peremhálózati eszközön, telepítenie kell a IoT Edge futtatókörnyezetet a valós eszközre. A következő útmutatók bemutatják, hogyan telepítheti a futtatókörnyezetet az általános eszköz-platformokon:

- [A Azure IoT Edge Runtime telepítése Linux rendszeren (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Azure IoT Edge futtatókörnyezet telepítése Windows rendszerű tárolókkal való használatra](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Telepítse a Windows rendszerhez készült Azure IoT Edge futtatókörnyezetet Linux-tárolókkal való használatra.](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [A IoT Edge Runtime telepítése a Windows IoT Core-on](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>További lépések

Most, hogy előkészítette a IoT Edge eszközt, a következő lépés a modulok üzembe helyezése. Lásd: [IoT Edge csomag importálása a távoli figyelési megoldás-gyorsító](iot-accelerators-remote-monitoring-import-edge-package.md)
