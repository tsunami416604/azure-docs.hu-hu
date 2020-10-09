---
title: Távoli figyelési megoldás importálási csomagja – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan importálhat egy automatikus Eszközkezelő csomagot a távoli figyelési megoldás-gyorsító
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "61443334"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Automatikus Eszközkezelő csomag importálása a távoli figyelési megoldás-gyorsító eszközbe

Az automatikus eszközkezelés konfigurációja meghatározza az eszközök egy csoportjára való központi telepítés konfigurációs módosításait. Ez a cikk azt feltételezi, hogy a szervezet egyik fejlesztője már létrehozott egy automatikus eszközkezelés-konfigurációt. Ha szeretné megtudni, hogyan hozza létre a fejlesztő a konfigurációt, tekintse meg a következő IoT Hub útmutatók egyikét:

- [IoT-eszközök konfigurálása és figyelése nagy méretekben a Azure Portal használatával](../iot-hub/iot-hub-auto-device-config.md)
- [IoT-eszközök konfigurálása és figyelése méretezéssel az Azure CLI használatával](../iot-hub/iot-hub-auto-device-config-cli.md)

A fejlesztők egy automatikus Eszközkezelő konfigurációt hoznak létre és tesztelnek egy fejlesztési környezetben. Ha elkészült, importálhatja a konfigurációt a távoli figyelési megoldás-gyorsító szolgáltatásba.

## <a name="export-a-configuration"></a>Konfiguráció exportálása

A Azure Portal használatával exportálja az automatikus eszközkezelés konfigurációját a fejlesztési környezetből:

1. A Azure Portal Navigáljon arra a IoT hub-ra, amelyet a IoT-eszközök fejlesztéséhez és teszteléséhez használ. Kattintson a **IoT eszköz konfigurálása**elemre:

    [![IoT-eszköz konfigurációja](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kattintson a használni kívánt konfigurációra. Megjelenik az **eszköz konfigurációjának részletei** lap:

    [![IoT-eszköz konfigurációjának részletei](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kattintson a **konfigurációs fájl letöltése**elemre:

    [![Konfigurációs fájl letöltése](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Mentse a JSON-fájlt egy **configuration.js**nevű helyi fájlként.

Most már rendelkezik egy olyan fájllal, amely tartalmazza az automatikus eszközkezelés konfigurációját. A következő szakaszban importálja ezt a konfigurációt csomagként a távoli figyelési megoldásba.

## <a name="import-a-package"></a>Csomag importálása

Az alábbi lépéseket követve importálhat egy automatikus eszközkezelés konfigurációt csomagként a megoldásba:

1. Navigáljon a távoli figyelés webes felhasználói felületének **csomagok** lapjára:  ![ csomagok lap](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kattintson az **+ új csomag**elemre, válassza a **konfiguráció** lehetőséget a csomag típusaként, majd kattintson a **Tallózás** gombra, és válassza ki a **configuration.js** az előző szakaszban mentett fájlon:

    ![Konfiguráció kiválasztása](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kattintson a **feltöltés** gombra a csomag távoli figyelési megoldáshoz való hozzáadásához:

    ![Feltöltött csomag](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Mostantól csomagként feltöltött egy automatikus eszközkezelés-konfigurációt. A **központi telepítések** lapon telepítheti ezt a csomagot a csatlakoztatott eszközökre.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan hozhat létre egy konfigurációs csomagot, és hogyan importálhatja azt a távoli figyelési megoldásból, a következő lépés a [távoli figyeléshez kapcsolódó eszközök tömeges](iot-accelerators-remote-monitoring-bulk-configuration-update.md)felügyeletének megismerése.
