---
title: Virtuális hálózat törlése után törlése az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ismerje meg a virtuális hálózat törlése után törlése az Azure SQL Database felügyelt példánya.
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
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411986"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Alhálózat törlése az Azure SQL Database felügyelt példány törlése

A cikk ismerteti iránymutatásokat manuálisan törölni az alhálózatot, miután törli a legutóbbi Azure SQL Database felügyelt példány szereplő, hogyan.

A [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) rendelkezik, amely tartalmazza a törölt felügyelt példány folyamatosan 12 órán át a példány törlését. A virtuális fürt ugyanazon az alhálózaton lévő felügyelt példányok gyorsabb létrehozását lehetővé elvárt másolatok életben. Ebben az időszakban a virtuális fürthöz társított alhálózat nem lehet törölni.

Azonnali kiadásában az üres virtuális fürt által használt alhálózat a virtuális fürt manuális törlését keresztül. A virtuális fürt törlése az Azure Portalon vagy a virtuális fürtök API érhető el.

> [!NOTE]
> A virtuális fürt egyetlen felügyelt példány a törlés sikeres tartalmaznia kell.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Virtuális fürt törlése az Azure Portal használatával

Töröl egy virtuális fürt Azure portal használatával, keresse meg a virtuális fürt erőforrásai a beépített keresési.

![Keresse meg a virtuális fürt.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Ha megtalálta a törölni kívánt virtuális fürt, jelölje be ezt az erőforrást, és válassza a Törlés lehetőséget. A virtuális fürt törlés megerősítésére kéri.

![Virtuális fürt törlése.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Jóváhagyás, hogy a virtuális fürt törölve lett az Azure portal értesítések megtalálható. A virtuális fürt sikeres törléséhez azonnal kiadja az alhálózat további ismételt felhasználásra.

## <a name="delete-virtual-cluster-using-api"></a>API-val virtuális fürt törlése

Törli a virtuális fürt API használatával a megadott URI-paraméterek a [virtuális fürtök törlése metódus](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Ismerje meg [a felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Ismerje meg, hogyan [módosíthatja egy meglévő virtuális hálózatot a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).
- Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális hálózatot, hozzon létre egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
