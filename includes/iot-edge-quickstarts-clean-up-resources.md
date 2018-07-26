---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008501"
---
Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforrások és -erőforráscsoportok törlése nem vonható vissza. Az elemek törlését követően az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre a következő parancsot. Cserélje le a \<YourIoTHub> kifejezést az IoT Hub nevével, a \<TestResources> kifejezést pedig az erőforráscsoport nevével:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


A teljes erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Szűrés név alapján** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

3. Az eredménylistában az erőforráscsoport mellett válassza a három pontot (**...**), majd az **Erőforráscsoport törlése** lehetőséget.

    ![Erőforráscsoport törlése](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését kéri. A megerősítéshez írja be újra az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.






