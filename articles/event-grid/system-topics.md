---
title: Rendszertémakörök az Azure Event Gridben
description: Az Azure Event Grid rendszertémaköreit ismerteti.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501191"
---
# <a name="system-topics-in-azure-event-grid"></a>Rendszertémakörök az Azure Event Gridben
Az Azure Event Grid szolgáltatás rendszertémaköröket hoz létre, amikor létrehoz egy első esemény-előfizetést egy Azure-eseményforráshoz. Jelenleg az Event Grid nem hoz létre rendszertémaköröket a 2020. Az ezen időpontban vagy azt követően létrehozott összes témakörforráshoz az Event Grid automatikusan rendszertémaköröket hoz létre. Ez a cikk az Azure Event Grid **rendszertémaköreit** ismerteti.

> [!NOTE]
> Ez a funkció jelenleg nincs engedélyezve az Azure Government felhőben. 

## <a name="overview"></a>Áttekintés
Amikor első esemény-előfizetést hoz létre egy Azure-eseményforráshoz, például az Azure Storage-fiókhoz, az előfizetés kiépítési folyamata egy **Microsoft.EventGrid/systemTopics**típusú további erőforrást hoz létre. Ha az Azure-eseményforrás utolsó esemény-előfizetése törlődik, a rendszertémakör automatikusan törlődik.

A Rendszertémakör nem alkalmazható egyéni témakör-forgatókönyvek, azaz Event Grid-témakörök és Eseményrács-tartományok esetén. 

## <a name="location"></a>Hely
Egy adott régióban/helyen lévő Azure-eseményforrások esetén a rendszertémakör ugyanazon a helyen jön létre, mint az Azure eseményforrása. Ha például az USA keleti részén egy Azure blob-tárterülethez hoz létre esemény-előfizetést, a rendszertémakör az USA keleti részén jön létre. Globális Azure-eseményforrások, például az Azure-előfizetések, erőforráscsoportok vagy az Azure Maps esetében az Event Grid **globális** helyen hozza létre a rendszertémakört. 

## <a name="resource-group"></a>Erőforráscsoport 
Általában a rendszertémakör ugyanabban az erőforráscsoportban jön létre, amelyben az Azure-eseményforrás található. Az Azure-előfizetési hatókörben létrehozott esemény-előfizetések esetén a rendszertémakör a **Default-EventGrid**erőforráscsoport alatt jön létre. Ha az erőforráscsoport nem létezik, az Azure Event Grid létrehozza azt a rendszertémakör létrehozása előtt. 

Amikor megpróbálja törölni az erőforráscsoportot a tárfiókkal, megjelenik a rendszertémakör az érintett erőforrások listájában.  

![Erőforráscsoport törlése](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Egyéni témakörök](event-sources.md#custom-topics)
- [Tartományok](event-domains.md)