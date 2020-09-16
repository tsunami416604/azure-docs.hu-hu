---
author: baanders
description: fájl belefoglalása az Azure Digital Twinsba – a legújabb IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606709"
---
Először futtassa ezt a parancsot a már telepített bővítmények listájának megtekintéséhez.

```azurecli
az extension list
```

A kimenet a jelenleg meglévő bővítmények tömbje. A `"name"` bővítmények neveinek megtekintéséhez keresse meg az egyes listaelemek mezőjét.

A kimenet használatával határozza meg, hogy a következő parancsok melyike fusson a bővítmény telepítéséhez (több is futtatható).
* Ha a lista a `azure-iot` következőket tartalmazza: már rendelkezik a kiterjesztéssel. A parancs futtatásával győződjön meg arról, hogy rendelkezik a legújabb frissítéssel, és nincs több elérhető frissítés:

   ```azurecli
   az extension update --name azure-iot
   ```

* Ha a lista **nem** tartalmazza a következőt `azure-iot` : telepítenie kell a bővítményt. Használja a következő parancsot:

    ```azurecli
    az extension add --name azure-iot
    ```

* Ha a lista tartalmazza a `azure-iot-cli-ext` következőt: Ez a bővítmény örökölt verziója. Egyszerre csak egy bővítményt kell telepíteni, ezért el kell távolítania az örökölt bővítményt. Használja a következő parancsot:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```