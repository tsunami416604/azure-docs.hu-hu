---
title: "Az Azure DNS - Azure-portálon DNS-zónák kezelése |} Microsoft Docs"
description: "DNS-zónákat az Azure portál használatával kezelheti. Ez a cikk ismerteti, hogyan frissítés, törlés és DNS-zóna létrehozása az Azure DNS szolgáltatásra"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 69a509612e6204fc93dd42bf2fe69cb165b5777c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zónák kezelése az Azure-portálon

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan a DNS-zónák kezelése az Azure portál használatával. Emellett kezelhetők a DNS-zónák használata a platformok közötti [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson az **Új > Hálózatkezelés >** elemre, majd kattintson a **DNS-zóna** elemre a DNS-zóna létrehozása panel megnyitásához.

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

## <a name="list-dns-zones"></a>Lista DNS-zónák

Az Azure-portálon lépjen a **további szolgáltatások** > **hálózati** > **DNS-zónák**. Minden DNS-zóna, saját erőforrás, például-rekordhalmazok száma és a név kiszolgálók ebben a nézetben látható. Az oszlop **NÉVKISZOLGÁLÓK** nincs az alapértelmezett nézet felvételéhez kattintson a **oszlopok**, jelölje be **névkiszolgálók** kattintson **végzett**.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>A DNS-zóna törlése

Nyissa meg a DNS-zónát a portálon. Az a **DNS-zóna** panelen kattintson a **zóna törlése**. Győződjön meg arról, hogy törölni kívánja a DNS-zónát kéri. A zónában lévő összes rekordot is egy DNS-zóna törlése törli.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan működnek együtt a DNS-zóna és a rekordok látogasson el [Ismerkedés az Azure DNS az Azure portál használatával](dns-getstarted-portal.md).