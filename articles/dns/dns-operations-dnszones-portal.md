---
title: DNS-zónák kezelése az Azure DNS-ben – Azure portal | Microsoft dokumentumok
description: A DNS-zónák az Azure Portal használatával kezelhetők. Ez a cikk bemutatja, hogyan frissíthetők, törölhetők és hozhatnak létre DNS-zónákat az Azure DNS-ben
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936786"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>A DNS-zónák kezelése az Azure Portalon

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Azure klasszikus parancssori felület](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónák az Azure Portal használatával. A DNS-zónákat a platformfüggetlen [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md)használatával is kezelheti.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A Központ menüben keresse meg az **Erőforrás létrehozása > hálózatok > DNS-zónában** a **DNS-zóna létrehozása** panel megnyitásához.

    ![DNS-zóna](./media/dns-operations-dnszones-portal/openzone650.png)

4. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:


   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Név**|contoso.com|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a DNS-zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoDNSRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Helyen**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="list-dns-zones"></a>DNS-zónák listázása

Az Azure Portalon keresse meg a **További szolgáltatások** > **hálózati** > **DNS-zónák at.** Minden DNS-zóna saját erőforrás, és ebből a nézetből megtekinthetők a rekordhalmazok és a névkiszolgálók száma. A **NAME SERVERS** oszlop nincs az alapértelmezett nézetben. A hozzáadáshoz kattintson **az Oszlopok gombra,** válassza **a Névkiszolgálók**lehetőséget, majd kattintson a **Kész**gombra.

![lista dns-zónák](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Keresse meg a portál DNS-zónáját. A **DNS-zóna** panelen kattintson a **Zóna törlése**gombra. Ezután a rendszer kéri, hogy ellenőrizze, hogy törölni kívánja-e a DNS-zónát. A DNS-zóna törlésével a zónában található összes rekord is törlődik.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan dolgozhat a DNS-zónával és a rekordokkal az [Azure DNS használatának első lépései az Azure Portalon](dns-getstarted-portal.md)című webhelyen.