---
title: Távoli figyelés megoldás importálása Edge-csomag – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a távoli figyelési megoldásgyorsító egy IoT Edge-csomag importálása
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828320"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító egy IoT Edge-csomag importálása

A manifest nasazení határozza meg, hogy a modulok IoT Edge-eszköz üzembe helyezése. Ez a cikk feltételezi, hogy a fejlesztők a szervezet már rendelkezik létrehozott manifest nasazení. Hogyan hozza létre a egy fejlesztési jegyzékfájl kapcsolatos további információkért tekintse meg az alábbi IoT Edge-útmutatók egyikét:

- [Az Azure Portalról az Azure IoT Edge-modulok telepítése](../iot-edge/how-to-deploy-modules-portal.md)
- [Az Azure CLI-vel az Azure IoT Edge-modulok telepítése](../iot-edge/how-to-deploy-modules-cli.md)
- [A Visual Studio Code-ból az Azure IoT Edge-modulok telepítése](../iot-edge/how-to-deploy-modules-vscode.md)

Egy fejlesztő hoz létre, és a egy manifest nasazení teszteli a fejlesztői környezetben. Amikor készen áll, a jegyzék importálhatja a távoli figyelési megoldásgyorsító.

## <a name="export-a-manifest"></a>A jegyzékfájl exportálása

Az Azure portal használatával exportálja a fejlesztési környezet manifest nasazení:

1. Az Azure Portalon lépjen az IoT hub használatával fejlesztheti és tesztelheti az IoT Edge-eszközökön. Kattintson a **IoT Edge** , majd **IoT Edge-telepítések**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kattintson a használni kívánt központi telepítés konfigurálása a központi telepítést. A **üzembe helyezési adatok** oldal: ![IoT Edge üzembe helyezés részleteit](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kattintson a **töltse le az IoT Edge-jegyzékfájl**: ![letöltési manifest nasazení](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. A JSON-fájl mentése nevű helyi fájl **deploymentmanifest.json**.

Most már manifest nasazení tartalmazó fájl. A következő szakaszban Ön így importálja a jegyzékfájlban csomagként a távoli figyelési megoldás.

## <a name="import-a-package"></a>Csomag importálása

Az Edge manifest nasazení csomag importálásához a megoldás az alábbi lépésekkel:

1. Keresse meg a **csomagok** oldal a távoli figyelési webes felhasználói felületen: ![csomagok lap](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kattintson a **+ új csomag**, válassza a **Edge Manifest** csomag típusa, és kattintson **Tallózás** válassza ki a **deploymentmanifest.json** fájl az előző szakaszban mentett: ![válassza manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kattintson a **feltöltése** , adja hozzá a csomagot a távoli figyelési megoldás: ![feltöltött csomag](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Az IoT Edge manifest nasazení csomag most már feltöltötte. Az a **központi telepítések** lap, ezt a csomagot a csatlakoztatott IoT Edge-eszközökön telepítheti.

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan modulok, a távoli figyelési megoldás üzembe helyezése az IoT Edge-eszköz, a következő lépés további részleteket ismerhet meg-e [IoT Edge](../iot-edge/about-iot-edge.md).
