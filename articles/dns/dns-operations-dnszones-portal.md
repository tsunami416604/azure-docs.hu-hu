---
title: Az Azure DNS - Azure-portálon DNS-zónák kezelése |} Microsoft Docs
description: DNS-zónákat az Azure portál használatával kezelheti. Ez a cikk ismerteti, hogyan frissítés, törlés és DNS-zóna létrehozása az Azure DNS szolgáltatásra
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: kumud
ms.openlocfilehash: 3fbf59010e690ac022e4363eddebe1cfbba53d13
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
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
2. A központ menüben kattintson, majd **hozzon létre egy erőforrást > Hálózat >** , majd **DNS-zóna** hozzon létre DNS-zóna panel megnyitásához.

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

Az Azure-portálon lépjen a **további szolgáltatások** > **hálózati** > **DNS-zónák**. Minden DNS-zóna, saját erőforrás, például-rekordhalmazok száma és a név kiszolgálók ebben a nézetben látható. Az oszlop **NÉVKISZOLGÁLÓK** nincs az alapértelmezett nézet felvételéhez kattintson a **oszlopok**, jelölje be **névkiszolgálók** kattintson **végzett**.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Nyissa meg a DNS-zónát a portálon. Az a **DNS-zóna** panelen kattintson a **zóna törlése**. Győződjön meg arról, hogy törölni kívánja a DNS-zónát kéri. A zónában lévő összes rekordot is egy DNS-zóna törlése törli.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan működnek együtt a DNS-zóna és a rekordok látogasson el [Ismerkedés az Azure DNS az Azure portál használatával](dns-getstarted-portal.md).