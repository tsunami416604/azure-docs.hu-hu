---
title: Egy meglévő IoT hub használata a Eszközszimuláció megoldással – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Eszközszimuláció megoldásgyorsító használandó egy meglévő IoT hubbal.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753917"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Egy meglévő IoT hub használata a Eszközszimuláció megoldásgyorsító

Eszközszimuláció üzembe helyezése, amikor is beállíthatja az IoT hub használata a szimuláció üzembe helyezéséhez. Ez a beállítás telepíti egy [S2 szint az IoT hub és a egy egyetlen skálázási egység](../iot-hub/iot-hub-scaling.md). Ha ez nem kötelező az IoT hub telepít, továbbra is választhat, amelyekre egy másik IoT hubot, a szimuláció futtatása.

Ha nem kíván telepíteni a választható IoT Hub, a saját hub bármely szimulációkhoz futtatása kell használnia.

Ha nem rendelkezik egy IoT hubot, bármikor létrehozhat egy újat a [az Azure portal](https://portal.azure.com).

A kapcsolati karakterláncát kell egy már meglévő IoT hubot használni, a **iothubowner** megosztott hozzáférési szabályzat. Megtekintheti a kapcsolati karakterláncot, a [az Azure portal](https://portal.azure.com):

1. A portálon a hub konfigurációs lapon kattintson **megosztott elérési házirendek**.

1. Kattintson a **iothubowner**.

1. Vagy az elsődleges vagy másodlagos kapcsolati karakterlánc másolása.

[![Kapcsolati sztring lekérése](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

A szimuláció konfigurálásakor kimásolt kapcsolati karakterláncot használja:

![Szimuláció konfigurálása](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>További lépések

Ez az útmutató hogyan használható egy meglévő IoT hubbal egy bemutattuk. Következő lépésként érdemes lehet további hogyan [hozzon létre egy speciális eszközmodell](iot-accelerators-device-simulation-advanced-device.md) a szimuláció.
