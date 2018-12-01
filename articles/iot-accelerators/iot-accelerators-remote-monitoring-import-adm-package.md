---
title: Távoli megfigyelési megoldás importálása ADM-csomag – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet egy automatikus felügyeleti csomag importálása a távoli figyelési megoldásgyorsító
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8fd6e733f3e80ba2a3ec632c088d070252e260cc
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684991"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító egy automatikus felügyeleti csomag importálása

Az automatikus Eszközkezelési konfiguráció határozza meg a konfigurációs módosítások egy csoporthoz az eszközök telepítése. Ez a cikk feltételezi, hogy a szervezet fejlesztői már létrehozott egy automatikus Eszközkezelési konfiguráció. Hogyan hozza létre a fejlesztők olyan konfigurációs kapcsolatos további információkért lásd: egyet az alábbi az IoT Hub-útmutatók:

- [Konfigurálhatja és figyelheti az Azure portal használatával nagy mennyiségű IoT-eszközök](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurálhatja és figyelheti az Azure CLI használatával nagy mennyiségű IoT-eszközök](../iot-hub/iot-hub-auto-device-config-cli.md)

Egy fejlesztő hoz létre, és a egy automatikus Eszközkezelési konfiguráció teszteli a fejlesztői környezetben. Amikor készen áll, a konfiguráció importálhatja a távoli figyelési megoldásgyorsító.

## <a name="export-a-configuration"></a>Konfiguráció exportálása

Az Azure portal használatával a fejlesztési környezetet az automatikus Eszközkezelési konfiguráció exportálása:

1. Az Azure Portalon lépjen az IoT hub használatával fejlesztheti és tesztelheti az IoT-eszközökről. Kattintson a **IoT-eszköz konfigurációs**:

    [![IoT-eszközök konfigurálása](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kattintson a használni kívánt konfigurációját. A **eszköz konfigurációs adatait** lapon jelenik meg:

    [![IoT-eszköz konfiguráció részletei](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kattintson a **letöltési konfigurációs fájl**:

    [![Konfigurációs fájl letöltése](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. A JSON-fájl mentése nevű helyi fájl **configuration.json**.

Most már az automatikus Eszközkezelési konfiguráció tartalmazó fájl. A következő szakaszban Ön így importálja a konfigurációs csomag a távoli figyelési megoldás.

## <a name="import-a-package"></a>Csomag importálása

Az automatikus Eszközkezelési konfiguráció csomag importálásához a megoldás az alábbi lépésekkel:

1. Keresse meg a **csomagok** oldal a távoli figyelési webes felhasználói felületen: ![csomagok lap](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kattintson a **+ új csomag**, válassza a **konfigurációs** csomag típusa, és kattintson **Tallózás** válassza ki a **configuration.json** fájl az előző szakaszban mentett:

    ![Válassza ki a konfiguráció](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kattintson a **feltöltése** , adja hozzá a csomagot a távoli figyelési megoldás:

    ![Feltöltött csomagban](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Most már feltöltötte egy automatikus Eszközkezelési konfiguráció-csomagként. Az a **központi telepítések** lapon telepítheti ezt a csomagot a csatlakoztatott eszközökre.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozzon létre egy csomagot, és importálja a távoli figyelési megoldást,-e a következő lépésben megtudhatja, hogyan [tömeges távoli figyelési csatlakoztatott eszközök kezelése](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
