---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77592422"
---
A következő táblázat felsorolja az Azure IoT Hub Device Provisioning Service-erőforrásokra vonatkozó korlátozásokat.

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetések maximális eszköz-kiépítési szolgáltatásai | 10 |
| Regisztrációk maximális száma | 1,000,000 |
| Regisztrációk maximális száma | 1,000,000 |
| Beléptetési csoportok maximális száma | 100 |
| Hitelesítésszolgáltatók maximális száma | 25 |
| Csatolt IoT-hubok maximális száma | 50 |
| Üzenet maximális mérete | 96 KB|

> [!NOTE]
> A kiépítési szolgáltatásban beléptetések és regisztrációk számának növeléséhez forduljon [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

> [!NOTE]
> A hitelesítésszolgáltatók maximális számának növelése nem támogatott.

Az eszköz kiépítési szolgáltatása a következő kvóták túllépése esetén szabályozza a kérelmeket.

| Szabályozás | Egységnyi érték |
| --- | --- |
| Műveletek | 200/perc/szolgáltatás |
| Eszközregisztrációk | 200/perc/szolgáltatás |
| Eszköz lekérdezési művelete | 5/10 mp/eszköz |
