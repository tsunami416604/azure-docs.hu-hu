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
ms.openlocfilehash: b7c3719b1539aa585dbc030bb8dfe732e73c81ac
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185068"
---
## <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások
Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a tűzfalbeállítások megkerülésének engedélyezése** beállítást, a következő szolgáltatások kapnak hozzáférést a Event Hubs erőforrásaihoz.

| Megbízható szolgáltatás | Támogatott használati forgatókönyvek | 
| --------------- | ------------------------- | 
| Azure Event Grid | Lehetővé teszi, hogy a Azure Event Grid eseményeket küldjön az esemény-huboknak a Event Hubs-névtérben. |
| Azure Monitor (diagnosztikai beállítások) | Lehetővé teszi a Azure Monitor számára diagnosztikai információk küldését az Event Hubs névterében lévő Event hubokba. |