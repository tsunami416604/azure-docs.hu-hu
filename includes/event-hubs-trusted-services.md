---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015577"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Event Hubs erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön az esemény-huboknak a Event Hubs-névtérben. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése egy témakörhöz vagy tartományhoz</li><li>Az identitás hozzáadása az Azure Event Hubs adatfeladói szerepkörhöz a Event Hubs névtérben</li><li>Ezt követően állítsa be azt az esemény-előfizetést, amely az Event hub-t használja végpontként a rendszer által hozzárendelt identitás használatára.</li></ul> <p>További információ: [esemény kézbesítése felügyelt identitással](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (diagnosztikai beállítások) | Lehetővé teszi a Azure Monitor számára diagnosztikai információk küldését az Event Hubs névterében lévő Event hubokba. |
| Azure Stream Analytics | Lehetővé teszi, hogy egy Azure Stream Analytics feladatból beolvassa az adatokat ([input](../articles/stream-analytics/stream-analytics-add-inputs.md)), vagy adatokat írjon ([output](../articles/stream-analytics/event-hubs-output.md)) az Event Hubs névtérbe. |
| Azure IoT Hub | Lehetővé teszi, hogy a IoT Hub üzeneteket küldjön az Event hub-névtérbe. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése az IoT hub számára</li><li>Adja hozzá az identitást az Azure Event Hubs adatfeladói szerepkörhöz a Event Hubs névtérben.</li><li>Ezután konfigurálja a IoT Hub, amely az Event hub-t használja egyéni végpontként az identitás-alapú hitelesítés használatához.</li></ul>
