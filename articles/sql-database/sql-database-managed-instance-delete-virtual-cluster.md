---
title: Alhálózat törlése felügyelt példány törlése után
description: Ismerje meg, hogyan törölheti az Azure virtuális hálózatot az Azure SQL Database által felügyelt példány törlése után.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820465"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Alhálózat törlése az Azure SQL Database felügyelt példányának törlése után

Ez a cikk útmutatást nyújt az alhálózat manuális törléséhez az abban található utolsó Azure SQL Database felügyelt példány törlése után.

A felügyelt példányok [virtuális fürtökbe](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)vannak telepítve. Minden virtuális fürt egy alhálózathoz van társítva. A virtuális fürt az utolsó példány törlése után 12 órán keresztül megmarad, így gyorsabban hozhat létre felügyelt példányokat ugyanabban az alhálózatban. Az üres virtuális fürt megtartása díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és inkább hamarabb törli a virtuális fürtöt és annak alhálózatát, ezt manuálisan is megteheti. Törölje a virtuális fürt manuálisan az Azure Portalon vagy a virtuális fürtök API használatával.

> [!IMPORTANT]
> - A virtuális fürt nem tartalmazhat felügyelt példányokat a törlés sikereslegyen. 
> - A virtuális fürt törlése egy körülbelül 1,5 órán át tartó hosszú ideig futó művelet (lásd: [Felügyelt példánykezelési műveletek](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) a virtuális fürt törléséhez), amely nek megfelelően a virtuális fürt továbbra is látható marad a portálon, amíg ez a folyamat be nem fejeződik.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése az Azure Portalról

Virtuális fürt törlése az Azure Portalhasználatával, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép az Azure Portalról, kiemelve a keresőmezővel](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, jelölje ki ezt az erőforrást, és válassza a **Törlés**lehetőséget. A rendszer kéri a virtuális fürt törlésének megerősítését.

![Képernyőkép az Azure Portal virtuális fürtök irányítópultjáról, kiemelve a Törlés lehetőséget](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Az Azure Portal értesítései egy megerősítést jelenít meg arról, hogy a virtuális fürt törlésére vonatkozó kérés sikeresen el lett küldve. Maga a törlési művelet körülbelül 1,5 óráig tart, amely alatt a virtuális fürt továbbra is látható lesz a portálon. A folyamat befejezése után a virtuális fürt már nem lesz látható, és a hozzá társított alhálózat újra felhasználható lesz.

> [!TIP]
> Ha a virtuális fürtben nincsenek felügyelt példányok, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nincs folyamatban lévő példány üzembe helyezése folyamatban. Ez magában foglalja az elindított és megszakított központi telepítések, amelyek még folyamatban vannak. Ennek az az oka, hogy ezek a műveletek továbbra is a virtuális fürt zárolását fogják használni a törlésből. A példány által üzembe helyezett erőforráscsoport Központi telepítései lapjának áttekintése jelzi a folyamatban lévő központi telepítéseket. Ebben az esetben várja meg, amíg a központi telepítés befejeződik, törölje a felügyelt példányt, majd a virtuális fürtöt.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha virtuális fürtöt szeretne törölni az API-n keresztül, használja a [virtuális fürtök törlési metódusában](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>További lépések

- Az áttekintést a [Mi a felügyelt példány?](sql-database-managed-instance.md)
- Ismerje meg [a felügyelt példány kapcsolódási architektúráját.](sql-database-managed-instance-connectivity-architecture.md)
- További információ a [felügyelt példány meglévő virtuális hálózatának módosításáról.](sql-database-managed-instance-configure-vnet-subnet.md)
- A virtuális hálózat létrehozásáról, felügyelt példány létrehozásáról és az adatbázis adatbázis-biztonsági mentésből történő visszaállításáról az [Azure SQL Database Managed Instance létrehozása című](sql-database-managed-instance-get-started.md)témakörben található.
- A DNS-sel kapcsolatos problémákról az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakörben van.
