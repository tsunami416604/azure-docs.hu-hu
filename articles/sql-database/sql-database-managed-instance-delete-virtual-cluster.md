---
title: Alhálózat törlése után törlése az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ismerje meg, hogyan törlése az Azure SQL Database felügyelt példány törlése, ha Azure-beli virtuális hálózathoz.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: ec5d99e160e739f59e2bf2ea369fe83e9900a1f1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295304"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Alhálózat törlése után törlése az Azure SQL Database felügyelt példánya

A cikk ismerteti iránymutatásokat manuálisan törölje a egy alhálózatot, ha törli a legutóbbi Azure SQL Database felügyelt példány szereplő, hogyan.

Az SQL Database adatbázisaihoz egy [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) , a törölt felügyelt példányt tartalmaz. A virtuális fürt továbbra is fennáll, ahhoz, hogy gyorsan hozzon létre felügyelt példányok ugyanazon az alhálózaton, a példány törlése után 12 órán át. Nem jár, hogy egy üres virtuális fürt. Ebben az időszakban a virtuális fürthöz társított alhálózat nem lehet törölni.

Várjon, 12 órán át, és azonnal törli a virtuális fürt és az alhálózat inkább nem szeretné, ha manuálisan teheti. A virtuális fürt manuális módszerrel törölje az Azure Portalon vagy a virtuális fürtök API használatával.

> [!NOTE]
> A virtuális fürt egyetlen felügyelt példány a törlés sikeres tartalmaznia kell.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése az Azure Portalról

Virtuális fürtök törlése az Azure Portalon keresse meg a virtuális fürt erőforrásait.

![A keresőmezőbe, kiemelve az Azure portal képernyőképe](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürt, jelölje be ezt az erőforrást, és válassza **törlése**. A virtuális fürt törlés megerősítésére kéri.

![Képernyőkép az Azure Portal virtuális fürtök az irányítópult kiemelt törlése lehetőséggel](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Az Azure portál értesítési területén látható megerősítése, hogy a virtuális fürt törölve lett. A virtuális fürt sikeres törléséhez azonnal kiadja az alhálózat ismételt felhasználásra.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API-val

Az API-n keresztül a virtuális fürt törléséhez használja a megadott URI-paraméterek a [virtuális fürtök törlése metódus](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Ismerje meg [a felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Ismerje meg, hogyan [módosíthatja egy meglévő virtuális hálózatot a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).
- Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális hálózatot, hozzon létre egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
