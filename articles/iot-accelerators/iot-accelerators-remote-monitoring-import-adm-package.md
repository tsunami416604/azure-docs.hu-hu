---
title: Távoli figyelési megoldás importálási csomagja - Azure | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan importálhat automatikus eszközfelügyeleti csomagot a távfigyelési megoldásgyorsítóba
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443334"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Automatikus eszközfelügyeleti csomag importálása a távfigyelési megoldás gyorsítójába

Az automatikus eszközkezelési konfiguráció határozza meg az eszközök egy csoportjára való üzembe helyezés konfigurációs módosításait. Ez a cikk feltételezi, hogy a szervezet egyik fejlesztője már létrehozott egy automatikus eszközkezelési konfigurációt. Ha többet szeretne megtudni arról, hogy egy fejlesztő hogyan hoz létre konfigurációt, tekintse meg az alábbi IoT Hub útmutatócikkek egyikét:

- [IoT-eszközök konfigurálása és figyelése az Azure Portalhasználatával](../iot-hub/iot-hub-auto-device-config.md)
- [IoT-eszközök konfigurálása és figyelése az Azure CLI használatával](../iot-hub/iot-hub-auto-device-config-cli.md)

A fejlesztő létrehoz és tesztel egy automatikus eszközkezelési konfigurációt egy fejlesztői környezetben. Ha készen áll, importálhatja a konfigurációt a távfigyelési megoldás gyorsítójába.

## <a name="export-a-configuration"></a>Konfiguráció exportálása

Az Azure Portal használatával exportálhatja az automatikus eszközkezelési konfigurációt a fejlesztői környezetből:

1. Az Azure Portalon keresse meg az IoT-központot, amelyet az IoT-eszközök fejlesztéséhez és teszteléséhez használ. Kattintson **az IoT-eszköz konfigurációjára:**

    [![IoT-eszköz konfigurációja](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kattintson a használni kívánt konfigurációra. Az **Eszköz konfigurációjának részletei** lap a következőket jeleníti meg:

    [![IoT-eszköz konfigurációjának részletei](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kattintson **a Konfigurációs fájl letöltése gombra:**

    [![Konfigurációs fájl letöltése](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Mentse a JSON-fájlt **a configuration.json**nevű helyi fájlként.

Most már van egy fájl, amely tartalmazza az automatikus eszközfelügyeleti konfigurációt. A következő szakaszban ezt a konfigurációt csomagként importálja a távoli figyelési megoldásba.

## <a name="import-a-package"></a>Csomag importálása

Az alábbi lépéseket követve importáljon automatikus eszközkezelési konfigurációt csomagként a megoldásba:

1. Keresse meg a Távoli figyelés webes felhasználói ![ **felületének Csomagok** lapját](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kattintson **a + Új csomag**menük elemre, válassza a Konfiguráció **csomagtípusként** lehetőséget, majd a **Tallózás gombra** kattintva válassza ki az előző szakaszban mentett **configuration.json** fájlt:

    ![Konfiguráció kiválasztása](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. A **Feltöltés gombra** kattintva adja hozzá a csomagot a távfigyelési megoldáshoz:

    ![Feltöltött csomag](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Most feltöltött egy automatikus eszközkezelési konfigurációt csomagként. A **Központi telepítések** lapon telepítheti ezt a csomagot a csatlakoztatott eszközökre.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan hozhat létre konfigurációs csomagot, és hogyan importálhatja azt a távoli figyelési megoldásból, a következő lépés a [távoli figyeléshez tömegesen csatlakoztatott eszközök kezelése.](iot-accelerators-remote-monitoring-bulk-configuration-update.md)
