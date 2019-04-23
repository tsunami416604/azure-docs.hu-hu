---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 65c89730e7d3d492b91daa8aba50e5606ca700a1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804167"
---
Az alábbi táblázat a vonatkozó korlátokat tartalmazza az Azure IoT Hub Device Provisioning Service-erőforrások.

| Erőforrás | Korlát |
| --- | --- |
| Maximális eszközregisztrációs szolgáltatások Azure-előfizetésenként | 10 |
| Regisztrációk maximális száma | 1,000,000 |
| A regisztrációk maximális száma | 1,000,000 |
| Regisztrációs csoportok maximális száma | 100 |
| Hitelesítésszolgáltatók maximális száma | 25 |

> [!NOTE]
> Az előfizetés-példányok számának növeléséhez forduljon [Support](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Növelje a beléptetések és az eszközkiépítési szolgáltatás a regisztrációk számát, lépjen kapcsolatba a [Support](https://azure.microsoft.com/support/options/).

A Device Provisioning Service szabályozza a kéréseket, amikor az alábbi kvóták átlépése.

| Szabályozás | Egységenkénti érték |
| --- | --- |
| Műveletek | 200-as/perc/szolgáltatás |
| Eszközregisztrációk | 200-as/perc/szolgáltatás |
| Eszköz lekérdezési művelet | 5/10/mp/eszköz |
