---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85839032"
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
