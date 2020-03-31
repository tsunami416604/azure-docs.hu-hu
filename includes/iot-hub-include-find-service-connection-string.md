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
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558448"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Az IoT Hub kapcsolati karakterláncának lekérnie a **szolgáltatásszabályzathoz** kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforráscsoportok**lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki a hubot az erőforrások listájából.

1. Az IoT hub bal oldali ablaktábláján válassza a **Megosztott hozzáférési szabályzatok**lehetőséget.

1. A házirendek listájából válassza ki a **szolgáltatási** házirendet.

1. A **Megosztott hozzáférési kulcsok csoportban**válassza ki a Kapcsolat karakterlánc másolatikonját - elsődleges **kulcs,** és mentse az értéket.

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Az IoT Hub megosztott hozzáférési szabályzatairól és engedélyeiről a [Hozzáférés-vezérlés és -engedélyek című](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)témakörben talál további információt.
