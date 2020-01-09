---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a306a05899a644f2ee8ea738789b84ee98221531
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392429"
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

Az eszköz kiépítési szolgáltatása a következő kvóták túllépése esetén szabályozza a kérelmeket.

| Szabályozás | Egységnyi érték |
| --- | --- |
| Műveletek | 200/perc/szolgáltatás |
| Eszközregisztrációk | 200/perc/szolgáltatás |
| Eszköz lekérdezési művelete | 5/10 mp/eszköz |
