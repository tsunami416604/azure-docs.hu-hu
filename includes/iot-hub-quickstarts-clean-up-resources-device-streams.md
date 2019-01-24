---
title: adatfájl (eszköz Streamek)
description: fájl belefoglalása
services: iot-hub
author: rezas
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 979ca857a4410a8efd1b211afc09a0563d76a1ba
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830149"
---
Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat.

Ellenkező esetben, a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

    ![Törlés](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.