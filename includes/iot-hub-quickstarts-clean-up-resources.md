---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: wesmc7777
ms.service: iot-hub
ms.topic: include
ms.date: 06/19/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 2c205c23ac828f350531eabf7adafd98ca2ea940
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36288056"
---
Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat.

Ellenkező esetben, a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

    ![Törlés](./media/iot-hub-quickstarts-clean-up-resources/iot-hub-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.