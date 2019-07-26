---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403962"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Ha olyan megosztott hozzáférési szabályzatot szeretne létrehozni, amely biztosítja a **szolgáltatás kapcsolódását** és a **beállításjegyzék olvasási** engedélyeit, és lekéri a Szabályzathoz tartozó kapcsolati karakterláncot, kövesse az alábbi lépéseket:

1. Nyissa meg az IoT hubot a [Azure Portal](https://portal.azure.com). Az IoT hub kiválasztásának legegyszerűbb módja az **erőforráscsoportok**kiválasztása. Válassza ki azt az erőforráscsoportot, ahol az IoT hub található, majd válassza ki az IoT hubot az erőforrások listájából.

2. Az IoT hub bal oldali ablaktábláján válassza a **megosztott hozzáférési házirendek**elemet.

3. A szabályzatok listájának felső menüjében válassza a **Hozzáadás**lehetőséget.

4. A **megosztott hozzáférési házirend hozzáadása** panelen adjon meg egy leíró nevet a szabályzatnak; például: *serviceAndRegistryRead*. Az **engedélyek**területen válassza a **beállításjegyzék olvasási** és **szolgáltatás-csatlakozási**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

    ![Új megosztott elérési házirend hozzáadásának megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. A **megosztott hozzáférési szabályzatok** panelen válassza ki az új házirendet a házirendek listájából.

6. A **megosztott elérési kulcsok**területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
