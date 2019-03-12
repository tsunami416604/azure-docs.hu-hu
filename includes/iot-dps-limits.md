---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 1ae6c6ee59b66e6c61714c0ece9f306f1a885096
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553505"
---
Az alábbi táblázat a vonatkozó korlátokat tartalmazza az Azure IoT Hub Device Provisioning Service-erőforrások.

| Erőforrás | Korlát |
| --- | --- |
| Maximális eszközregisztrációs szolgáltatások Azure-előfizetésenként | 10 |
| Regisztrációk maximális száma | 500,000 |
| A regisztrációk maximális száma | 500,000 |
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
