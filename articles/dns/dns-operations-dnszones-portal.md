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
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829682"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zónák kezelése az Azure Portalon

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Az Azure klasszikus parancssori felület](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónák az Azure portal használatával. Emellett kezelheti a DNS-zónák használata a platformfüggetlen [Azure CLI-vel](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. Lépjen a központ menüben **erőforrás létrehozása > Hálózatkezelés > DNS-zóna** megnyitásához a **DNS-zóna létrehozása** panelen.

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

Az Azure Portalon lépjen **további szolgáltatások** > **hálózatkezelés** > **DNS-zónák**. Minden DNS-zónát a saját erőforrás- és -rekordhalmazok száma például és névkiszolgálók teljesítményobjektumok ebben a nézetben. Az oszlop **NÉVKISZOLGÁLÓK** nem szerepel az alapértelmezett nézet. Adja hozzá, kattintson a **oszlopok**válassza **névkiszolgálók**, és kattintson a **kész**.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Keresse meg a DNS-zóna, a portálon. Az a **DNS-zóna** panelen kattintson a **zóna törlése**. Ezután a rendszer kéri, győződjön meg arról, hogy térni a DNS-zóna törlése. DNS-zóna törlése is törli az összes rekordot a zónában található.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan dolgozhat meglátogatják a DNS-zóna és -rekordok [Ismerkedés az Azure DNS az Azure portal használatával](dns-getstarted-portal.md).