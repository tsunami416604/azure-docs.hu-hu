---
title: Alhálózat törlése Azure SQL Database felügyelt példány törlése után | Microsoft Docs
description: Megtudhatja, hogyan törölhet egy Azure-beli virtuális hálózatot Azure SQL Database felügyelt példány törlése után.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 7ad09682275b5cc2311b792899a85c1c47eafc0d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431289"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Alhálózat törlése Azure SQL Database felügyelt példány törlése után

Ez a cikk útmutatást nyújt ahhoz, hogyan lehet manuálisan törölni az alhálózatot az utolsó Azure SQL Database felügyelt példányának törlése után.

A felügyelt példányok [virtuális fürtökbe](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)vannak telepítve. Minden virtuális fürt társítva van egy alhálózattal. A virtuális fürt az utolsó példány törlése után 12 órával megőrzi a tervet, így gyorsabban hozhat létre felügyelt példányokat ugyanabban az alhálózatban. Üres virtuális fürt megőrzése díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és hamarabb szeretné törölni a virtuális fürtöt és alhálózatát, manuálisan is megteheti. Törölje manuálisan a virtuális fürtöt a Azure Portal vagy a virtuális fürtök API használatával.

> [!IMPORTANT]
> - A virtuális fürtnek nem tartalmazhat felügyelt példányt, hogy a törlés sikeres legyen. 
> - A virtuális fürt törlése hosszú ideig tartó, körülbelül 1,5 óra időtartamú futási művelet (lásd: [felügyelt példányok felügyeleti műveletei](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) a virtuális fürt naprakészen tartása), amely alatt a virtuális fürt továbbra is látható lesz a portálon a folyamat befejeződött.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése a Azure Portalból

Ha a Azure Portal használatával szeretne törölni egy virtuális fürtöt, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép a Azure Portalről, a keresőmezőbe kiemelve](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, válassza ki ezt az erőforrást, és válassza a **Törlés**lehetőséget. A rendszer felszólítja, hogy erősítse meg a virtuális fürt törlését.

![Képernyőfelvétel a Azure Portal Virtual Clusters irányítópultról, a törlés lehetőség kiemelve](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure Portal értesítések megerősítik, hogy a virtuális fürt törlésére vonatkozó kérelem sikeresen elküldve. Maga a törlési művelet körülbelül 1,5 óráig tart, amikor a virtuális fürt továbbra is látható lesz a portálon. A folyamat befejezése után a virtuális fürt többé nem lesz látható, és a hozzá társított alhálózat újra fel lesz szabadítva.

> [!TIP]
> Ha nem találhatók felügyelt példányok a virtuális fürtben, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem rendelkezik folyamatban lévő példány-telepítéssel. Ez magában foglalja a még folyamatban lévő elindított és megszakított központi telepítéseket is. Ennek az az oka, hogy ezek a műveletek továbbra is a virtuális fürtöt fogják használni a törlésből. Azon erőforráscsoport központi telepítések lapjának áttekintése, amelyeken a példány telepítve lett, a rendszer minden folyamatban lévő központi telepítést jelez. Ebben az esetben várjon, amíg a telepítés befejeződik, törölje a felügyelt példányt, majd a virtuális fürtöt.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha a virtuális fürtöt az API-n keresztül szeretné törölni, használja a [virtuális fürtök delete metódusában](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Mi az a felügyelt példány?](sql-database-managed-instance.md)című témakört.
- Ismerje meg a [felügyelt példány kapcsolati architektúráját](sql-database-managed-instance-connectivity-architecture.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot a felügyelt példányok számára](sql-database-managed-instance-configure-vnet-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakört.
