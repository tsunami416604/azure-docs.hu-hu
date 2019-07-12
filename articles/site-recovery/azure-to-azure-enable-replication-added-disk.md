---
title: Egy Azure virtuális gép replikálása az Azure Site Recovery által hozzáadott lemezt a replikáció engedélyezése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet egy Azure virtuális gép, amelyen engedélyezve van az Azure Site Recovery vész-helyreállítási hozzáadott lemezt a replikáció engedélyezése
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671872"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Egy lemezt hozzá egy Azure virtuális gép replikálásának engedélyezése


Ez a cikk bemutatja, hogyan engedélyezni a replikációt, egy Azure virtuális Gépen már engedélyezve van egy másik Azure-régióba vész-helyreállítási hozzáadott adatlemezek használata [Azure Site Recovery](site-recovery-overview.md).

Egy lemezt ad hozzá egy virtuális gép replikálásának engedélyezése a felügyelt lemezek az Azure-beli virtuális gépek is támogatja.

Amikor egy új lemezt ad hozzá egy Azure virtuális Gépen, amely egy másik Azure-régióban való replikálásához van, az alábbiak történnek:

-   A virtuális gép replikációs állapotát jeleníti meg a figyelmeztetést, és egy megjegyzés, a portál értesíti, hogy egy vagy több lemezt érhetők el védelemre.
-   Ha engedélyezte a védelmet a hozzáadott lemezek, a figyelmeztetés fiókérvényesítés után megjelenik a lemez kezdeti replikálását.
-   Ha nem szeretné a lemez replikációja választja, kiválaszthatja a figyelmeztetés bezárása.

![Új lemez hozzáadása](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Előkészületek

Ez a cikk azt feltételezi, hogy már beállította vész-helyreállítási a virtuális gép, amelyhez adja hozzá a lemezt. Ha még nem, kövesse a [Azure-bA vész-helyreállítási oktatóanyag](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Replikáció engedélyezése a hozzáadott lemezen 

Egy hozzáadott lemezt replikálásának engedélyezéséhez, tegye a következőket:

1. A tárolóban > **replikált elemek**, kattintson a virtuális Gépre, amelyhez hozzáadta a lemezt.
2. Kattintson a **lemezek**, majd válassza ki az adatlemezt, amelynek szeretné engedélyezni a replikációt (ezek a lemezek rendelkezik egy **nem védett** állapot).
3.  A **Háttértárakról**, kattintson a **engedélyezze a replikációt**.

    ![Hozzáadott lemezt a replikáció engedélyezése](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

A replikáció engedélyezése-feladatot futtat, és a kezdeti replikálás befejezése, miután a rendszer eltávolítja a replikációs állapot figyelmeztetés a lemezhiba.



# <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
