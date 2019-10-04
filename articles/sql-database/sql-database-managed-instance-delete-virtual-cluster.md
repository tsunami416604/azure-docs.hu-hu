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
ms.openlocfilehash: f6e0b55ad2fbd9b4c45dbd1facaebd4750314c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567542"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Alhálózat törlése Azure SQL Database felügyelt példány törlése után

Ez a cikk útmutatást nyújt ahhoz, hogyan lehet manuálisan törölni az alhálózatot az utolsó Azure SQL Database felügyelt példányának törlése után.

SQL Database egy [virtuális fürtöt](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) használ a törölt felügyelt példány tárolásához. A virtuális fürt a példány törlése után 12 órával továbbra is fennáll, így lehetővé teszi, hogy gyorsan hozzon létre felügyelt példányokat ugyanabban az alhálózatban. Üres virtuális fürt megőrzése díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és azonnal törölni szeretné a virtuális fürtöt és az alhálózatát, manuálisan is megteheti. Törölje manuálisan a virtuális fürtöt a Azure Portal vagy a virtuális fürtök API használatával.

> [!NOTE]
> A virtuális fürtnek nem tartalmazhat felügyelt példányt, hogy a törlés sikeres legyen.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése a Azure Portalból

Ha a Azure Portal használatával szeretne törölni egy virtuális fürtöt, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép a Azure Portalről, a keresőmezőbe kiemelve](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, válassza ki ezt az erőforrást, és válassza a **Törlés**lehetőséget. A rendszer felszólítja, hogy erősítse meg a virtuális fürt törlését.

![Képernyőfelvétel a Azure Portal Virtual Clusters irányítópultról, a törlés lehetőség kiemelve](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

A Azure Portal értesítések részen a virtuális fürt törlésének megerősítése látható. A virtuális fürt sikeres törlése azonnal felszabadítja az alhálózatot az újrafelhasználáshoz.

> [!TIP]
> Ha nem találhatók felügyelt példányok a virtuális fürtben, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem rendelkezik folyamatban lévő példány-telepítéssel. Ez magában foglalja a még folyamatban lévő elindított és megszakított központi telepítéseket is. Azon erőforráscsoport központi telepítések lapjának áttekintése, amelyeken a példány telepítve lett, a rendszer minden folyamatban lévő központi telepítést jelez. Ebben az esetben várja meg a telepítés befejeződését, törölje a felügyelt példányt, majd a virtuális fürtöt.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha a virtuális fürtöt az API-n keresztül szeretné törölni, használja a [virtuális fürtök delete metódusában](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Mi az a felügyelt példány?](sql-database-managed-instance.md)című témakört.
- Ismerje meg a [felügyelt példány kapcsolati architektúráját](sql-database-managed-instance-connectivity-architecture.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot a felügyelt példányok számára](sql-database-managed-instance-configure-vnet-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakört.
