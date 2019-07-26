---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402381"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

A **szolgáltatási** házirendhez tartozó IoT hub kapcsolódási karakterlánc beszerzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az IoT hubot a [Azure Portal](https://portal.azure.com).  Az IoT hub kiválasztásának legegyszerűbb módja az **erőforráscsoportok**kiválasztása. Válassza ki azt az erőforráscsoportot, ahol az IoT hub található, majd válassza ki az IoT hubot az erőforrások listájából.

2. Az IoT hub bal oldali ablaktábláján válassza a **megosztott hozzáférési házirendek**elemet.

3. A házirendek listájából válassza ki a **szolgáltatási** házirendet.

4. A **megosztott elérési kulcsok**területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
