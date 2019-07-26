---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385747"
---
A Azure Resource Manager segítségével paramétereket adhat meg a sablon telepítésekor használandó értékekhez. A sablon tartalmaz egy `parameters` szakaszt, amely tartalmazza az összes paraméter értékét. A sablon minden paraméter értékét felhasználja a telepíteni kívánt erőforrások definiálásához.

> [!NOTE]
> Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. Csak olyan értékekhez adjon meg paramétereket, amelyek az üzembe helyezett projekt alapján vagy az üzembe helyezett környezet alapján változnak.

Paraméterek megadásakor:

* A felhasználók által az üzembe helyezés során megadható engedélyezett értékek megadásához használja a **allowedValues** mezőt.

* Ha alapértelmezett értékeket szeretne hozzárendelni a paraméterhez, ha az üzembe helyezés során nincs megadva érték, használja a **defaultValue** mezőt. 
