---
title: "Az Azure DNS használatának első lépései az Azure Portal használatával | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure Portal használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 93b24e3d9fbb3fbb3ea995271fd63d1e82eb9c9e
ms.contentlocale: hu-hu
ms.lasthandoff: 05/18/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Bevezetés az Azure DNS Azure Portallal való használatába

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Ez a cikk végigvezeti az első DNS-zóna és -rekord létrehozásának lépésein az Azure Portalon. Ezek a lépések az Azure PowerShell-lel vagy a platformfüggetlen Azure CLI-vel is elvégezhetőek.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson az **Új > Hálózatkezelés >** elemre, majd kattintson a **DNS-zóna** elemre a DNS-zóna létrehozása panel megnyitásához.

    ![DNS-zóna](./media/dns-getstarted-portal/openzone650.png)

4. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:


   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|contoso.com|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a DNS-zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoDNSRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

A következő példa végigvezeti egy új „A” rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört. 

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az Összes erőforrás panelen kattintson a **contoso.com** DNS-zónára. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a **contoso.com** nevet a **Szűrés név alapján...** mezőbe.

1. A **DNS-zóna** panel tetején válassza a **+ Rekordhalmaz** elemet a **Rekordhalmaz hozzáadása** panel megnyitásához.

1. A **Rekordhalmaz hozzáadása** panelen adja meg az alábbi értékeket, majd kattintson az **OK** gombra. Ebben a példában egy A rekordot hozhat létre.

   |**Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|www|A rekord neve|
   |**Típus**|A| A létrehozni kívánt DNS-rekord típusa. Az elfogadható értékek: A, AAAA, CNAME, MX, NS, SRV, TXT és PTR.  A rekordok típusaival kapcsolatos további információért tekintse meg a [DNS-zónákat és -rekordokat áttekintő](dns-zones-records.md) cikket.|
   |**TTL**|1|A DNS-kérés élettartama.|
   |**TTL mértékegysége**|Óra|A TTL értékének időmértékegysége.|
   |**IP-cím**|ipAddressValue| Ez az érték a DNS-rekord által feloldott IP-cím.|

## <a name="view-records"></a>A rekordok megtekintése

A DNS-zóna panel alsó részén láthatja a DNS-zóna rekordjait. Meg kell jelennie az alapértelmezett DNS és SOA típusú rekordoknak, amelyek minden zónában létrejönnek, valamint az összes új létrehozott rekordnak.

![zóna](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>A névkiszolgálók frissítése

Ha a DNS-zóna és -rekordok megfelelően be lettek állítva, konfigurálnia kell a tartománynevet az Azure DNS-névkiszolgálók használatára. Így más internetes felhasználók megkereshetik a DNS-rekordjait.

A zóna névkiszolgálói az Azure Portalon vannak megadva:

![zóna](./media/dns-getstarted-portal/viewzonens500.png)

Ezeket a névkiszolgálókat a tartományregisztrálóhoz kell konfigurálni (ahol a tartománynevet vásárolta). A regisztráló felajánlja, hogy beállítja a névkiszolgálókat a tartományhoz. További információért lásd: [Tartomány delegálása az Azure DNS-be](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az Összes erőforrás panelen kattintson a **MyResourceGroup** erőforráscsoportra. Ha a kiválasztott előfizetésben már több erőforrás szerepel, az erőforráscsoport egyszerű eléréséhez beírhatja a **MyResourceGroup** nevet a **Szűrés név alapján...** mezőbe.
1. A **MyResourceGroup** panelen kattintson a **Törlés** gombra.
1. A portál megköveteli, hogy az erőforráscsoport törlésének megerősítéséhez beírja annak nevét. Kattintson a **Törlés** elemre, írja be a *MyResourceGroup* nevet az erőforráscsoport nevéhez, majd kattintson a **Törlés** gombra. Az erőforráscsoport törlésével az abban foglalt összes erőforrás törölve lesz, ezért mindenképp ellenőrizze az erőforráscsoportok tartalmát azok törlése előtt. A portál törli az erőforráscsoportban lévő összes erőforrást, majd magát az erőforráscsoportot is. Ez a folyamat több percig is eltarthat.


## <a name="next-steps"></a>Következő lépések

Az Azure DNS-sel kapcsolatos további információért lásd [az Azure DNS áttekintését biztosító](dns-overview.md) cikket.

Az Azure DNS-ben a DNS-rekordok kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.


