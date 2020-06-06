---
title: Rendszertémakörök a Azure Event Grid
description: A Azure Event Grid rendszertémaköreinek ismertetése.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456929"
---
# <a name="system-topics-in-azure-event-grid"></a>Rendszertémakörök a Azure Event Grid
A rendszertémakörök az Azure-szolgáltatásokhoz, például az Azure Storage-hoz és az Azure Event Hubs-hoz készült témakörök. Rendszertémaköröket a Azure Portal, a PowerShell, a CLI vagy a Azure Resource Manager sablon használatával hozhat létre.  

> [!NOTE]
> Ez a funkció jelenleg nincs engedélyezve Azure Government felhőben. 

## <a name="create-system-topics"></a>Rendszertémakörök létrehozása
A rendszertémakört kétféleképpen hozhatja létre: 

- Hozzon létre egy rendszertémakört az Azure-erőforrásokhoz, majd hozzon létre egy esemény-előfizetést az adott rendszertémakörhöz.
- Hozzon létre egy esemény-előfizetést egy Azure-erőforráson, amely belsőleg létrehoz egy rendszertémakört.

Az első módszer használatakor a rendszer nem törli automatikusan a rendszertémakört, ha a rendszer a legutóbbi esemény-előfizetést törli. A második módszer használatakor a rendszer automatikusan törli a rendszertémakört az utolsó esemény-előfizetés törlésekor. 

A rendszertémakörök Azure Portal, PowerShell vagy parancssori felülettel történő létrehozásával kapcsolatos részletes utasításokért tekintse meg a következő cikkeket:

- [Rendszertémakörök létrehozása, megtekintése és kezelése Azure Portal használatával](create-view-manage-system-topics.md).
- [Event Grid rendszertémakörök létrehozása, megtekintése és kezelése az Azure CLI használatával](create-view-manage-system-topics-cli.md)
- [Event Grid rendszertémakörök létrehozása Azure Resource Manager sablonok használatával](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Rendszertéma neve
Event Grid nem hoztak létre rendszertémaköröket az Azure-források (Azure Storage, Azure Event Hubs stb.) létrehozásához, amelyeket a március 15. és 2020. előtt hozott létre. Ha a 3/15/2020 és 6/2/2020 közötti Azure-források által kiváltott eseményhez hozott létre előfizetést, akkor a Event Grid szolgáltatás automatikusan létrehozta a rendszertémakört egy **véletlenszerűen generált névvel**. 6/2/2020 után megadhatja a rendszertémakör nevét, miközben Azure-forrásra vonatkozó esemény-előfizetést hoz létre. 

## <a name="location-and-resource-group"></a>Hely és erőforráscsoport
Egy adott régióban/helyen található Azure-beli eseményforrás esetén a rendszertémakör ugyanazon a helyen jön létre, mint az Azure-esemény forrása. Ha például egy Azure Blob Storage-hoz hoz létre egy Event-előfizetést az USA keleti régiójában, akkor a rendszer témakör az USA keleti régiójában jön létre. A globális Azure-események, például az Azure-előfizetések, az erőforráscsoportok vagy a Azure Maps esetében a Event Grid a **globális** helyen hozza létre a rendszer témakört. 

Általánosságban elmondható, hogy a rendszertémakör ugyanabban az erőforráscsoportban jön létre, amelyben az Azure-esemény forrása található. Az Azure-előfizetések hatókörében létrehozott esemény-előfizetések esetén a rendszertémakört az erőforráscsoport **alapértelmezett-EventGrid**alatt hozza létre a rendszer. Ha az erőforráscsoport nem létezik, Azure Event Grid létrehozza azt a rendszertémakör létrehozása előtt. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Rendszertémakörök létrehozása, megtekintése és kezelése Azure Portal használatával](create-view-manage-system-topics.md).
- [Event Grid rendszertémakörök létrehozása, megtekintése és kezelése az Azure CLI használatával](create-view-manage-system-topics-cli.md)
- [Event Grid rendszertémakörök létrehozása Azure Resource Manager sablonok használatával](create-view-manage-system-topics-arm.md)
