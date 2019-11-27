---
title: DNS-zónák kezelése a Azure DNS-Azure Portalban | Microsoft Docs
description: A DNS-zónák a Azure Portal használatával kezelhetők. Ez a cikk ismerteti, hogyan lehet frissíteni, törölni és létrehozni a DNS-zónákat Azure DNS
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211915"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zónák kezelése a Azure Portal

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure klasszikus parancssori felület](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónákat a Azure Portal használatával. A DNS-zónákat a platformfüggetlen [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md)használatával is kezelheti.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben navigáljon az **erőforrás létrehozása > hálózatkezelés > DNS-zóna** elemre a **DNS-zóna létrehozása** panel megnyitásához.

    ![DNS-zóna](./media/dns-operations-dnszones-portal/openzone650.png)

4. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:


   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|contoso.com|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a DNS-zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoDNSRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="list-dns-zones"></a>DNS-zónák listázása

A Azure Portalban navigáljon a **További szolgáltatások** > **hálózatkezelés** > **DNS-zónákhoz**. A DNS-zónák a saját erőforrásai, és a nézetből megtekinthető információk, például a rekordhalmazok és a névkiszolgálók száma. Az oszlopnév- **kiszolgálók** nem az alapértelmezett nézetben vannak. A hozzáadásához kattintson az **oszlopok**elemre **, válassza a névkiszolgálók lehetőséget**, majd kattintson a **kész**gombra.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Navigáljon egy DNS-zónához a portálon. A **DNS-zóna** panelen kattintson a **zóna törlése**elemre. Ekkor a rendszer felszólítja, hogy erősítse meg, hogy törölni szeretné a DNS-zónát. A DNS-zónák törlésével a zónában található összes rekord is törlődik.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja a DNS-zónát és-rekordokat [a Azure DNS használatának első lépéseiben a Azure Portal használatával](dns-getstarted-portal.md).