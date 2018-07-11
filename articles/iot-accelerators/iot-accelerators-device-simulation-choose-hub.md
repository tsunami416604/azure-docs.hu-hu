---
title: Egy meglévő IoT hub használata a Eszközszimuláció megoldással – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Eszközszimuláció megoldásgyorsító használandó egy meglévő IoT hubbal.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967547"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Egy meglévő IoT hub használata a Eszközszimuláció megoldásgyorsító

Az Eszközszimuláció megoldásgyorsító üzembe helyezésekor, kiválaszthatja, üzembe helyezéséhez egy IoT hubot az erőforráscsoportban a megoldásgyorsító használandó a szimuláció.

Ha nem kíván telepíteni a választható IoT Hub, a saját hub bármely szimulációkhoz futtatása kell használnia. Ha úgy dönt, az opcionális IoT Hub üzembe helyezéséhez, lehet váltani, ez nem kötelező központ vagy a saját központ használja.

Ha nem rendelkezik egy IoT hubot, bármikor létrehozhat egy újat a [az Azure portal](https://portal.azure.com).

Egy már meglévő IoT hub használandó kapcsolati karakterláncot kell a **iothubowner** megosztott hozzáférési szabályzat. Megtekintheti a kapcsolati karakterláncot, a [az Azure portal](https://portal.azure.com):

1. A portálon a hub konfigurációs lapon kattintson **megosztott elérési házirendek**.
1. Kattintson a **iothubowner**.
1. Vagy az elsődleges vagy másodlagos kapcsolati karakterlánc másolása.

[![Kapcsolati sztring lekérése](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

A szimuláció konfigurálásakor kimásolt kapcsolati karakterláncot használja:

[![Szimuláció konfigurálása](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>További lépések

Ez az útmutató hogyan használható egy meglévő IoT hubbal egy bemutattuk. Következő lépésként érdemes lehet további hogyan [konfigurálása egyéni eszközmodell](iot-accelerators-device-simulation-custom-model.md) a szimuláció.
