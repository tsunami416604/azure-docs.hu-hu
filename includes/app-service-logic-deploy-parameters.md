---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179538"
---
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor használandó értékeket a paraméterek. A sablon tartalmaz egy `parameters` összes paraméter értékét tartalmazó szakasz. Minden egyes paraméterérték használják a sablont, amely számára telepíteni kívánja az erőforrások definiálása.

> [!NOTE]
> Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. Határozza meg a paraméterek csak az értékeket, amelyek eltérőek lehetnek, a projekt végzi vagy telepítéséhez a környezet alapján.

Ha paraméterek meghatározása:

* A felhasználó általi üzembe helyezés során az engedélyezett értékek megadásához használja a **allowedValues** mező.

* Szeretne hozzárendelni, paraméterben alapértelmezett értékeket, ha nincs fel van tüntetve, üzembe helyezés során, használja a **defaultValue** mező. 
