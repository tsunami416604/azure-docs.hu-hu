---
title: DNS-rekordkészletek és -rekordok kezelése az Azure DNS-sel
description: Az Azure DNS lehetővé teszi a DNS-rekordkészletek és rekordok kezelését a tartomány üzemeltetése során.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936839"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-rekordok és rekordkészletek kezelése az Azure Portal használatával

Ez a cikk bemutatja, hogyan kezelheti a DNS-zóna rekordkészleteit és rekordjait az Azure Portal használatával.

Fontos megérteni a DNS-rekordkészletek és az egyes DNS-rekordok közötti különbséget. A rekordhalmaz olyan rekordok gyűjteménye egy zónában, amelyek azonos nevűek és azonos típusúak. További információt a [DNS-rekordkészletek és rekordok létrehozása az Azure Portal használatával című témakörben talál.](dns-getstarted-create-recordset-portal.md)

## <a name="create-a-new-record-set-and-record"></a>Új rekordkészlet és rekord létrehozása

Ha rekordkészletet szeretne létrehozni az Azure Portalon, olvassa [el a DNS-rekordok létrehozása az Azure Portal használatával című témakört.](dns-getstarted-create-recordset-portal.md)

## <a name="view-a-record-set"></a>Rekordkészlet megtekintése

1. Az Azure Portalon nyissa meg a **DNS-zóna** panel.
2. Keresse meg a rekordkészletet, és jelölje ki. Ezzel megnyitja a rekordkészlet tulajdonságait.

    ![Rekordkészlet keresése](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Új rekord hozzáadása rekordkészlethez

Bármely rekordhalmazhoz legfeljebb 20 rekordot adhat hozzá. Egy rekordhalmaz nem tartalmazhat két azonos rekordot. Üres rekordhalmazok (nulla rekorddal) hozhatók létre, de nem jelennek meg az Azure DNS-névkiszolgálóin. A CNAME típusú rekordhalmazok legfeljebb egy rekordot tartalmazhatnak.

1. A **DNS-zóna Rekordkészlet tulajdonságai** paneljén kattintson arra a rekordkészletre, amelyhez rekordot szeretne hozzáadni.

    ![Rekordkészlet kiválasztása](./media/dns-operations-recordsets-portal/selectset500.png)

2. Adja meg a rekordkészlet tulajdonságait a mezők kitöltésével.

    ![Rekord felvétele](./media/dns-operations-recordsets-portal/addrecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra. Ezután csukja be a pengét.
4. A sarokban látni fogja, hogy a rekord mentés.

    ![Rekordkészlet mentése](./media/dns-operations-recordsets-portal/saving150.png)

A rekord mentése után a **DNS-zóna** panelen lévő értékek az új rekordot fogják tükrözni.

## <a name="update-a-record"></a>Rekord frissítése

Ha egy meglévő rekordkészletben frissít egy rekordot, a frissíthető mezők attól függnek, hogy milyen típusú bejegyzéssel dolgozik.

1. A **Rekordhalmaz tulajdonságainak** panelen keresse meg a rekordot.
2. Módosítsa a rekordot. Amikor módosít egy rekordot, módosíthatja a bejegyzés elérhető beállításait. A következő példában az **IP-cím** mező van kiválasztva, és az IP-cím módosítása folyamatban van.

    ![Rekordok módosítása](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra. A jobb felső sarokban megjelenik az értesítés arról, hogy a bejegyzés mentve lett.

    ![Mentett rekordkészlet](./media/dns-operations-recordsets-portal/saved150.png)

A rekord mentése után a **DNS-zóna** panelen beállított rekord értékei a frissített rekordot fogják tükrözni.

## <a name="remove-a-record-from-a-record-set"></a>Rekord eltávolítása rekordhalmazból

Az Azure Portal segítségével rekordokat távolíthat el egy rekordkészletből. Ne feledje, hogy az utolsó rekord rekordkészletből való eltávolítása nem törli a rekordkészletet.

1. A **Rekordhalmaz tulajdonságainak** panelen keresse meg a rekordot.
2. Kattintson az eltávolítani kívánt rekordra. Ezután válassza **az Eltávolítás lehetőséget.**

    ![Rekord eltávolítása](./media/dns-operations-recordsets-portal/removerecord500.png)

3. A beállítások mentéséhez kattintson a panel tetején található **Mentés** gombra.
4. A rekord eltávolítása után a **DNS-zóna** panelen lévő rekord értékei tükrözik az eltávolítást.

## <a name="delete-a-record-set"></a><a name="delete"></a>Rekordkészlet törlése

1. A **Rekordhalmaz tulajdonságai** nak panelen kattintson a **Törlés gombra.**

    ![Rekordkészlet törlése](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Megjelenik egy üzenet, amely megkérdezi, hogy törölni szeretné-e a rekordkészletet.
3. Ellenőrizze, hogy a név megegyezik-e a törölni kívánt rekordkészletével, majd kattintson az **Igen**gombra.
4. A **DNS-zóna** panelen ellenőrizze, hogy a rekordkészlet már nem látható-e.

## <a name="work-with-ns-and-soa-records"></a>NS- és SOA-rekordok kal való kapcsolat

Az automatikusan létrehozott NS- és SOA-rekordok kezelése a többi bejegyzéstípustól eltérően történik.

### <a name="modify-soa-records"></a>SOA-rekordok módosítása

Az automatikusan létrehozott SOA rekordkészletből nem lehet rekordokat hozzáadni\@vagy eltávolítani a zóna csúcsán (név = " "). A SOA rekord (kivéve a "Host") és a Rekordkészlet TTL paramétereit azonban módosíthatja.

### <a name="modify-ns-records-at-the-zone-apex"></a>NS-rekordok módosítása a zóna csúcsán

A zóna csúcsán beállított NS rekord automatikusan létrejön az egyes DNS-zónákkal. A zónához rendelt Azure DNS-névkiszolgálók nevét tartalmazza.

A ns rekordkészlethez további névkiszolgálókat is hozzáadhat, így több DNS-szolgáltatóval is támogathatja a tartományok közös üzemeltetését. A rekordkészlet TTL- és metaadatait is módosíthatja. Az előre kitöltött Azure DNS-névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Ne feledje, hogy ez csak a zóna csúcsán beállított NS rekordra vonatkozik. A zónában lévő egyéb ns rekordkészletek (a gyermekzónák delegálásához használt) korlátozás nélkül módosíthatók.

### <a name="delete-soa-or-ns-record-sets"></a>SOA- vagy NS-rekordkészletek törlése

A zóna csúcsán (name = "\@") a SOA és az NS rekordkészletek nem törölhetők, amelyek a zóna létrehozásakor automatikusan létrejönnek. A zóna törlésekor a program automatikusan törli őket.

## <a name="next-steps"></a>További lépések

* Az Azure DNS-ről az Azure DNS áttekintésében olvashat [bővebben.](dns-overview.md)
* A DNS automatizálásáról a [DNS-zónák és rekordkészletek létrehozása a .NET SDK használatával című](dns-sdk.md)témakörben talál további információt.
* A fordított DNS-rekordokról a [Fordított DNS és támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md)című témakörben olvashat bővebben.
* Az Azure DNS-aliasrekordokról az [Azure DNS-aliasrekordok – áttekintés című témakörben olvashat bővebben.](dns-alias.md)
