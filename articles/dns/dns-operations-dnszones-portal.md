---
title: Az Azure DNS – Azure portal DNS-zónák kezelése |} A Microsoft Docs
description: DNS-zónák az Azure portal használatával kezelheti. Ez a cikk bemutatja, hogyan frissítése, törlése, és hozzon létre DNS-zónák az Azure DNS-ben
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: ca9d03cb14e79b23ccc2021e0a31650eb9bbd95b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171238"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zónák kezelése az Azure Portalon

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónák az Azure portal használatával. Emellett kezelheti a DNS-zónák használata a platformfüggetlen [Azure CLI-vel](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson, és kattintson a **erőforrás létrehozása > Hálózatkezelés >** majd **DNS-zóna** a hozzon létre DNS-zóna panel megnyitásához.

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

Az Azure Portalon lépjen **további szolgáltatások** > **hálózatkezelés** > **DNS-zónák**. Minden DNS-zóna saját erőforrás, például-rekordhalmazok számának és azok ebben a nézetben látható a névkiszolgálókat. Az oszlop **NÉVKISZOLGÁLÓK** nem szerepel, hozzáadása, kattintson az alapértelmezett nézet **oszlopok**, jelölje be **névkiszolgálók** kattintson **kész**.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Keresse meg a DNS-zóna, a portálon. Az a **DNS-zóna** panelen kattintson a **zóna törlése**. Szeretne térni a DNS-zóna törlése megerősítésére kéri. Is DNS-zónák törlésével a zónában lévő összes rekordját törli.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan dolgozhat meglátogatják a DNS-zóna és -rekordok [Ismerkedés az Azure DNS az Azure portal használatával](dns-getstarted-portal.md).