---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e427a7b80b28f1cc70a02890152f9f2247a8bcd2
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "68360280"
---
A következő táblázat felsorolja az Azure IoT Hub Device Provisioning Service-erőforrásokra vonatkozó korlátozásokat.

| Resource | Korlát |
| --- | --- |
| Azure-előfizetések maximális eszköz-kiépítési szolgáltatásai | 10 |
| Regisztrációk maximális száma | 1,000,000 |
| Regisztrációk maximális száma | 1,000,000 |
| Beléptetési csoportok maximális száma | 100 |
| Hitelesítésszolgáltatók maximális száma | 25 |
| Csatolt IoT-hubok maximális száma | 10 |
| Üzenet maximális mérete | 96 KB|


> [!NOTE]
> Az előfizetésben lévő példányok számának növeléséhez [](https://azure.microsoft.com/support/options/)forduljon Microsoft ügyfélszolgálatahoz.

> [!NOTE]
> A kiépítési szolgáltatásban beléptetések és regisztrációk számának növeléséhez forduljon Microsoft ügyfélszolgálatahoz [](https://azure.microsoft.com/support/options/).

Az eszköz kiépítési szolgáltatása a következő kvóták túllépése esetén szabályozza a kérelmeket.

| Szabályozás | Egységnyi érték |
| --- | --- |
| Műveletek | 200/perc/szolgáltatás |
| Eszközregisztrációk | 200/perc/szolgáltatás |
| Eszköz lekérdezési művelete | 5/10 mp/eszköz |
