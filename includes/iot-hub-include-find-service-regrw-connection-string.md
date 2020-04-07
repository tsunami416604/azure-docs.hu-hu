---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756917"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Ha olyan megosztott hozzáférési házirendet szeretne létrehozni, amely **szolgáltatás-csatlakozási** és **beállításjegyzék-írási** engedélyeket ad, és a házirendhez kapcsolati karakterláncot szeretne beszerezni, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforráscsoportok**lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki a hubot az erőforrások listájából.

1. A központ bal oldali ablaktábláján válassza a **Megosztott hozzáférési házirendek**lehetőséget.

1. A házirendek listája feletti felső menüben válassza a **Hozzáadás**gombot.

1. A **Megosztott hozzáférési házirend hozzáadása**csoportban adja meg a házirend leíró nevét, például *serviceAndRegistryReadWrite*. Az **Engedélyek csoportban**válassza a **Rendszerleíróadatbázis írása** és **a Szolgáltatáscsatlakozás**lehetőséget, majd a **Create (Létrehozás) lehetőséget.** (A **rendszerleíró adatbázis olvasási** engedélye automatikusan megjelenik, amikor a **Rendszerleíróadatbázis írása lehetőséget választja.)**

    ![Új megosztott hozzáférési szabályzat hozzáadásának megjelenítése](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Válassza ki az új szabályzatot a házirendek listájából.

1. A **Megosztott hozzáférési kulcsok csoportban**válassza ki a Kapcsolat karakterlánc másolatikonját - elsődleges **kulcs,** és mentse az értéket.

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Az IoT Hub megosztott hozzáférési szabályzatairól és engedélyeiről a [Hozzáférés-vezérlés és -engedélyek című](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)témakörben talál további információt.
