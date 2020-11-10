---
title: fájlbefoglalás
description: fájlbefoglalás
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426568"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Service Bus erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön a Service Bus névterében lévő várólistákba vagy témakörökbe. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése egy témakörhöz vagy tartományhoz</li><li>Az identitás hozzáadása a Azure Service Bus adatfeladói szerepkörhöz a Service Bus névtérben</li><li>Ezt követően konfigurálja azt az esemény-előfizetést, amely egy Service Bus-várólistát vagy-témakört használ végpontként a rendszer által hozzárendelt identitás használatára.</li></ul> <p>További információ: [esemény kézbesítése felügyelt identitással](../articles/event-grid/managed-service-identity.md)</p>|
