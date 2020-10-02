---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654458"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Event Hubs erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön az esemény-huboknak a Event Hubs-névtérben. A következő lépéseket is végre kell hajtania: <ul><li>A rendszer által hozzárendelt identitás engedélyezése egy témakörhöz vagy tartományhoz</li><li>Az identitás hozzáadása az Azure Event Hubs adatfeladói szerepkörhöz a Event Hubs névtérben</li><li>Ezt követően állítsa be azt az esemény-előfizetést, amely az Event hub-t használja végpontként a rendszer által hozzárendelt identitás használatára.</li></ul> <p>További információ: [esemény kézbesítése felügyelt identitással](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (diagnosztikai beállítások) | Lehetővé teszi a Azure Monitor számára diagnosztikai információk küldését az Event Hubs névterében lévő Event hubokba. |