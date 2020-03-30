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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050411"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Ha olyan megosztott hozzáférési házirendet szeretne létrehozni, amely **szolgáltatás-csatlakozási** és **beállításjegyzék-olvasási** engedélyeket ad, és csatlakozási karakterláncot szeretne kapni ehhez a házirendhez, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforráscsoportok**lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki a hubot az erőforrások listájából.

1. A központ bal oldali ablaktábláján válassza a **Megosztott hozzáférési házirendek**lehetőséget.

1. A házirendek listája feletti felső menüben válassza a **Hozzáadás**gombot.

1. A **Megosztott hozzáférési házirend hozzáadása csoportban**adja meg a házirend leíró nevét, például *serviceAndRegistryRead*. Az **Engedélyek csoportban**válassza a **Rendszerleíróadatbázis olvasása** és **a Szolgáltatás csatlakozás a**lehetőséget, majd a **Létrehozás**lehetőséget.

    ![Új megosztott hozzáférési szabályzat hozzáadásának megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Válassza ki az új szabályzatot a házirendek listájából.

1. A **Megosztott hozzáférési kulcsok csoportban**válassza ki a Kapcsolat karakterlánc másolatikonját - elsődleges **kulcs,** és mentse az értéket.

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Az IoT Hub megosztott hozzáférési szabályzatairól és engedélyeiről a [Hozzáférés-vezérlés és -engedélyek című](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)témakörben talál további információt.
