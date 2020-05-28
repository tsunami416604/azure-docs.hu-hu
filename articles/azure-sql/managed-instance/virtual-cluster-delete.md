---
title: Alhálózat törlése egy felügyelt Azure SQL-példány törlése után
description: Ismerje meg, hogyan törölhet Azure-beli virtuális hálózatokat egy felügyelt Azure SQL-példány törlése után.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 73150ee893ca23ed7996b001bd02acaabbf89dce
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116689"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Alhálózat törlése egy felügyelt Azure SQL-példány törlése után
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan törölni az alhálózatot az utolsó Azure SQL felügyelt példány törlése után.

A felügyelt példányok [virtuális fürtökbe](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)vannak telepítve. Minden virtuális fürt társítva van egy alhálózattal. A virtuális fürt az utolsó példány törlése után 12 órával megőrzi a tervet, így gyorsabban hozhat létre felügyelt példányokat ugyanabban az alhálózatban. Üres virtuális fürt megőrzése díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és hamarabb szeretné törölni a virtuális fürtöt és alhálózatát, manuálisan is megteheti. Törölje manuálisan a virtuális fürtöt a Azure Portal vagy a virtuális fürtök API használatával.

> [!IMPORTANT]
> - A virtuális fürtnek nem tartalmazhat felügyelt példányt, hogy a törlés sikeres legyen. 
> - A virtuális fürt törlése hosszú ideig tartó működés, amely körülbelül 1,5 órát vesz igénybe (lásd az [SQL felügyelt példányok felügyeleti műveleteinek](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) naprakész virtuális fürthöz való törlésének idejét), amely alatt a virtuális fürt továbbra is látható lesz a portálon, amíg a folyamat be nem fejeződik.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése a Azure Portalból

Ha a Azure Portal használatával szeretne törölni egy virtuális fürtöt, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép a Azure Portalről, a keresőmezőbe kiemelve](./media/virtual-cluster-delete/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, válassza ki ezt az erőforrást, és válassza a **Törlés**lehetőséget. A rendszer felszólítja, hogy erősítse meg a virtuális fürt törlését.

![Képernyőfelvétel a Azure Portal Virtual Clusters irányítópultról, a törlés lehetőség kiemelve](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal értesítések megerősítik, hogy a virtuális fürt törlésére vonatkozó kérelem sikeresen elküldve. Maga a törlési művelet körülbelül 1,5 óráig tart, amikor a virtuális fürt továbbra is látható lesz a portálon. A folyamat befejezése után a virtuális fürt többé nem lesz látható, és a hozzá társított alhálózat újra fel lesz szabadítva.

> [!TIP]
> Ha nem találhatók felügyelt példányok a virtuális fürtben, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem rendelkezik folyamatban lévő példány-telepítéssel. Ez magában foglalja a még folyamatban lévő elindított és megszakított központi telepítéseket is. Ennek az az oka, hogy ezek a műveletek továbbra is a virtuális fürtöt fogják használni a törlésből. Azon erőforráscsoport központi telepítések lapjának áttekintése, amelyeken a példány telepítve lett, a rendszer minden folyamatban lévő központi telepítést jelez. Ebben az esetben várjon, amíg a telepítés befejeződik, törölje a felügyelt példányt, majd a virtuális fürtöt.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha a virtuális fürtöt az API-n keresztül szeretné törölni, használja a [virtuális fürtök delete metódusában](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Mi az a felügyelt példány?](sql-managed-instance-paas-overview.md)című témakört.
- Ismerje meg a [felügyelt példány kapcsolati architektúráját](connectivity-architecture-overview.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot a felügyelt példányok számára](vnet-existing-add-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért tekintse meg [Az Azure SQL felügyelt példányának létrehozása](instance-create-quickstart.md)című témakört.
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
