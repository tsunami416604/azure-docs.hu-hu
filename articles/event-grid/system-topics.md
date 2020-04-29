---
title: Rendszertémakörök a Azure Event Grid
description: A Azure Event Grid rendszertémaköreinek ismertetése.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393153"
---
# <a name="system-topics-in-azure-event-grid"></a>Rendszertémakörök a Azure Event Grid
A Azure Event Grid szolgáltatás rendszertémaköröket hoz létre, amikor létrehoz egy első esemény-előfizetést egy Azure-esemény forrásához. Jelenleg Event Grid nem hoz létre rendszertémaköröket a március, 15, 2020. előtt létrehozott témakör-forrásokhoz. Az ezen dátumon vagy azt követően létrehozott összes témakörhöz Event Grid automatikusan létrehozza a rendszertémaköröket. Ez a cikk a Azure Event Grid **rendszertémaköreit** ismerteti.

> [!NOTE]
> Ez a funkció jelenleg nincs engedélyezve Azure Government felhőben. 

## <a name="overview"></a>Áttekintés
Ha az első esemény-előfizetést egy Azure-eseményforrás, például az Azure Storage-fiókhoz hozza létre, az előfizetés létesítési folyamata egy további, **Microsoft. EventGrid/systemTopics**típusú erőforrást hoz létre. Ha az Azure-eseményforrás utolsó eseményének előfizetését törli, a rendszer automatikusan törli a rendszertémakört.

A rendszertémakör nem alkalmazható egyéni témakör-forgatókönyvekre, azaz Event Grid témakörökre és Event Grid tartományokra. 

## <a name="location"></a>Hely
Egy adott régióban/helyen található Azure-beli eseményforrás esetén a rendszertémakör ugyanazon a helyen jön létre, mint az Azure-esemény forrása. Ha például egy Azure Blob Storage-hoz hoz létre egy Event-előfizetést az USA keleti régiójában, akkor a rendszer témakör az USA keleti régiójában jön létre. A globális Azure-események, például az Azure-előfizetések, az erőforráscsoportok vagy a Azure Maps esetében a Event Grid a **globális** helyen hozza létre a rendszer témakört. 

## <a name="resource-group"></a>Erőforráscsoport 
Általánosságban elmondható, hogy a rendszertémakör ugyanabban az erőforráscsoportban jön létre, amelyben az Azure-esemény forrása található. Az Azure-előfizetések hatókörében létrehozott esemény-előfizetések esetén a rendszertémakört az erőforráscsoport **alapértelmezett-EventGrid**alatt hozza létre a rendszer. Ha az erőforráscsoport nem létezik, Azure Event Grid létrehozza azt a rendszertémakör létrehozása előtt. 

Amikor megpróbálja törölni az erőforráscsoportot a Storage-fiókkal, az érintett erőforrások listájában megjelenik a rendszer témakör.  

![Erőforráscsoport törlése](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Egyéni témakörök](custom-topics.md)
- [Tartományok](event-domains.md)