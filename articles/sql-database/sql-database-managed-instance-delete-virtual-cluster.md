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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427976"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Alhálózat törlése után törlése az Azure SQL Database felügyelt példánya

A cikk ismerteti iránymutatásokat manuálisan törölje a egy alhálózatot, ha törli a legutóbbi Azure SQL Database felügyelt példány szereplő, hogyan.

Az SQL Database adatbázisaihoz egy [virtuális fürt](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) , a törölt felügyelt példányt tartalmaz. A virtuális fürt továbbra is fennáll, ahhoz, hogy gyorsan hozzon létre felügyelt példányok ugyanazon az alhálózaton, a példány törlése után 12 órán át. Nem jár, hogy egy üres virtuális fürt. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Várjon, 12 órán át, és azonnal törli a virtuális fürt és az alhálózat inkább nem szeretné, ha manuálisan teheti. A virtuális fürt manuális módszerrel törölje az Azure Portalon vagy a virtuális fürtök API használatával.

> [!NOTE]
> A virtuális fürt egyetlen felügyelt példány a törlés sikeres tartalmaznia kell.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése az Azure Portalról

Virtuális fürtök törlése az Azure Portalon keresse meg a virtuális fürt erőforrásait.

![A keresőmezőbe, kiemelve az Azure portal képernyőképe](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürt, jelölje be ezt az erőforrást, és válassza **törlése**. A virtuális fürt törlés megerősítésére kéri.

![Képernyőkép az Azure Portal virtuális fürtök az irányítópult kiemelt törlése lehetőséggel](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Az Azure portál értesítési területén látható megerősítése, hogy a virtuális fürt törölve lett. A virtuális fürt sikeres törléséhez azonnal kiadja az alhálózat ismételt felhasználásra.

> [!TIP]
> Ha nincsenek felügyelt példányok, a virtuális fürt látható, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem kell egy folyamatban lévő üzembe helyezésének folyamatban van. Ez magában foglalja a elindult, és a visszavont üzemelő példánya, amely még folyamatban van. Központi telepítések fülre az erőforráscsoport, a példány üzembe lett helyezve áttekintése a folyamatban lévő telepítések jelzi. Ebben az esetben await üzembe helyezés befejeződik, a felügyelt példány és a virtuális fürt törlése.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API-val

Az API-n keresztül a virtuális fürt törléséhez használja a megadott URI-paraméterek a [virtuális fürtök törlése metódus](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Ismerje meg [a felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Ismerje meg, hogyan [módosíthatja egy meglévő virtuális hálózatot a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).
- Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális hálózatot, hozzon létre egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
