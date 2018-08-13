---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6502ea1733e37e06172833c944c58101b3c049f2
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043889"
---
Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforrások és -erőforráscsoportok törlése nem vonható vissza. Ezeknek az elemeknek a törlésével az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre a következő parancsot. Cserélje le a \<YourIoTHub> kifejezést az IoT Hub nevével, a \<TestResources> kifejezést pedig az erőforráscsoport nevével:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


A teljes erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Szűrés név alapján** mezőbe írja be az IoT Hub-központot tartalmazó erőforráscsoport nevét. 

3. Az erőforráscsoporttól jobbra, az eredménylistában válassza a három pont (**...**), majd az **Erőforráscsoport törlése** lehetőséget.

    ![Erőforráscsoport törlése](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését kéri. A megerősítéshez írja be újra az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.






