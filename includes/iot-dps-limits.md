---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 610f5fcf80db8ffa0c7207d459b98087cbb4773b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455196"
---
Az alábbi táblázat a vonatkozó korlátokat tartalmazza az Azure IoT Hub Device Provisioning Service-erőforrások.

| Resource | Korlát |
| --- | --- |
| Maximális eszközregisztrációs szolgáltatások Azure-előfizetésenként | 10 |
| Regisztrációk maximális száma | 1,000,000 |
| A regisztrációk maximális száma | 1,000,000 |
| Regisztrációs csoportok maximális száma | 100 |
| Hitelesítésszolgáltatók maximális száma | 25 |
| Üzenet maximális mérete | 96 KB|

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
