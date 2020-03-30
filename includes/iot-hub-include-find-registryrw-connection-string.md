---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883714"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

A **rendszerleíró adatbázis ReadWrite házirendjének** IoT Hub-kapcsolati karakterláncának lekérnie, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforráscsoportok**lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki a hubot az erőforrások listájából.

2. A központ bal oldali ablaktábláján válassza a **Megosztott hozzáférési házirendek**lehetőséget.

3. A házirendek listájából válassza ki a **registryReadWrite** házirendet.

4. A **Megosztott hozzáférési kulcsok csoportban**válassza ki a Kapcsolat karakterlánc másolatikonját - elsődleges **kulcs,** és mentse az értéket.

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Az IoT Hub megosztott hozzáférési szabályzatairól és engedélyeiről a [Hozzáférés-vezérlés és -engedélyek című](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)témakörben talál további információt.
