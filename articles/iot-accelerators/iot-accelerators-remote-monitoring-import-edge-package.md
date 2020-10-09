---
title: Távoli figyelési megoldás importálás Edge-csomagja – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan importálhat egy IoT Edge csomagot a távoli figyelési megoldás-gyorsító
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "61442939"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>IoT Edge csomag importálása a távoli figyelési megoldás-gyorsító eszközbe

Az üzembe helyezési jegyzék meghatározza a IoT Edge eszközre telepítendő modulokat. Ez a cikk azt feltételezi, hogy a szervezet fejlesztője már létrehozott egy üzembe helyezési jegyzéket. Ha szeretné megtudni, hogyan hozza létre a fejlesztő a jegyzékfájlt, tekintse meg a következő IoT Edge útmutatók egyikét:

- [Azure IoT Edge modulok üzembe helyezése a Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure IoT Edge-modulok üzembe helyezése az Azure CLI-vel](../iot-edge/how-to-deploy-modules-cli.md)
- [Azure IoT Edge-modulok üzembe helyezése a Visual Studio Code-ból](../iot-edge/how-to-deploy-modules-vscode.md)

A fejlesztő egy fejlesztési környezetben hozza létre és teszteli az üzembe helyezési jegyzéket. Ha elkészült, importálhatja a jegyzékfájlt a távoli figyelési megoldás-gyorsító szolgáltatásba.

## <a name="export-a-manifest"></a>Jegyzék exportálása

A Azure Portal használatával exportálja az üzembe helyezési jegyzéket a fejlesztői környezetből:

1. A Azure Portal Navigáljon arra a IoT hub-ra, amelyet a IoT Edge-eszközök fejlesztéséhez és teszteléséhez használ. Kattintson **IoT Edge** majd **IoT Edge központi telepítések**: ![ IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kattintson arra a központi telepítésre, amely a használni kívánt központi telepítési konfigurációval rendelkezik. A **központi telepítés részletei** lap a következőket jeleníti meg: ![ IoT Edge központi telepítés részletei](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kattintson a **letöltés IoT Edge manifest**:  ![ telepítési jegyzék letöltése elemre.](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Mentse a JSON-fájlt egy **deploymentmanifest.js**nevű helyi fájlként.

Most már rendelkezik egy olyan fájllal, amely tartalmazza az üzembe helyezési jegyzéket. A következő szakaszban importálja ezt a jegyzékfájlt csomagként a távoli figyelési megoldásba.

## <a name="import-a-package"></a>Csomag importálása

Kövesse az alábbi lépéseket az Edge központi telepítési jegyzék csomagként történő importálásához a megoldásban:

1. Navigáljon a távoli figyelés webes felhasználói felületének **csomagok** lapjára:  ![ csomagok lap](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kattintson az **+ új csomag**elemre, válassza az **Edge manifest** lehetőséget csomag típusaként, majd kattintson a **Tallózás** gombra, és válassza ki az előző szakaszban mentett fájl **deploymentmanifest.jsét** :  ![ válassza a jegyzékfájlt](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kattintson a **feltöltés** gombra, és adja hozzá a csomagot a távoli figyelési megoldáshoz:  ![ feltöltött csomag](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Most feltöltött egy IoT Edge üzembe helyezési jegyzéket csomagként. A **központi telepítések** lapon telepítheti ezt a csomagot a csatlakoztatott IoT Edge-eszközökre.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepíthet modulokat egy IoT Edge eszközre a távoli figyelési megoldásból, a következő lépés a [IoT Edge](../iot-edge/about-iot-edge.md)megismerése.
