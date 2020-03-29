---
title: Távoli figyelési megoldás importálása Edge-csomag - Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan importálhat egy IoT Edge-csomagot a távoli figyelési megoldásgyorsítóba
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442939"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>IoT Edge-csomag importálása a távoli figyelési megoldás gyorsítójába

A központi telepítési jegyzékfájl határozza meg az IoT Edge-eszköz üzembe helyezhető modulokat. Ez a cikk feltételezi, hogy a szervezet egyik fejlesztője már létrehozott egy központi telepítési jegyzékfájlt. Ha többet szeretne megtudni arról, hogy egy fejlesztő hogyan hoz létre jegyzékfájlt, tekintse meg az alábbi IoT Edge útmutató cikkek egyikét:

- [Azure IoT Edge-modulok üzembe helyezése az Azure Portalról](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure IoT Edge-modulok üzembe helyezése az Azure CLI-vel](../iot-edge/how-to-deploy-modules-cli.md)
- [Azure IoT Edge-modulok üzembe helyezése a Visual Studio-kódból](../iot-edge/how-to-deploy-modules-vscode.md)

A fejlesztő létrehoz és tesztel egy központi telepítési jegyzékfájlt egy fejlesztői környezetben. Ha készen áll, importálhatja a jegyzékfájlt a távoli figyelési megoldás gyorsítójába.

## <a name="export-a-manifest"></a>Jegyzékfájl exportálása

Az Azure Portal használatával exportálhatja a telepítési jegyzékfájlt a fejlesztői környezetből:

1. Az Azure Portalon keresse meg az IoT hubot, amelyet az IoT Edge-eszközök fejlesztéséhez és teszteléséhez használ. Kattintson **az IoT Edge,** majd ![az **IoT Edge-telepítések:** IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kattintson a használni kívánt központi telepítési konfigurációval rendelkező központi telepítésre. A **Telepítés részletei** ![lap a következőket jeleníti meg: IoT Edge-telepítés részletei](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kattintson **az IoT Edge-jegyzékfájl letöltése**elemre: ![Telepítési jegyzékfájl letöltése](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Mentse a JSON-fájlt **a deploymentmanifest.json**nevű helyi fájlként.

Most már rendelkezik egy fájllal, amely tartalmazza a központi telepítési jegyzék. A következő szakaszban importálja ezt a jegyzékfájlt csomagként a távoli figyelési megoldásba.

## <a name="import-a-package"></a>Csomag importálása

Az alábbi lépéseket követve importálja az Edge központi telepítési jegyzékfájlcsomagként a megoldásba:

1. Keresse meg a Távoli figyelés webes felhasználói ![ **felületének Csomagok** lapját](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kattintson **a + Új csomag**gombra , válassza az Edge **Manifest** lehetőséget a csomagtípusként, majd a **Tallózás gombra** kattintva jelölje ki az előző szakaszban mentett **telepítésimanifest.json** fájlt: Jelölje ki a jegyzékfájlt. ![](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. A **Feltöltés gombra** kattintva adja ![hozzá a csomagot a távoli figyelési megoldáshoz: Feltöltött csomag](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Most feltöltött egy IoT Edge-telepítési jegyzékfájl csomagot. A **Központi telepítések** lapon telepítheti ezt a csomagot a csatlakoztatott IoT Edge-eszközökre.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepítheti a modulokat egy IoT Edge-eszközre a távoli figyelési megoldásból, a következő lépés az IoT Edge további [megismerése.](../iot-edge/about-iot-edge.md)
