---
title: Replikáció engedélyezése egy hozzáadott Azure Virtuálisgép-lemezhez az Azure Site Recovery szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan engedélyezheti a replikációt egy Azure-beli virtuális géphez hozzáadott lemezhez, amely engedélyezve van az Azure Site Recovery szolgáltatással a vészhelyreállításhoz
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973801"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Az Azure-géphez hozzáadott lemez replikációjának engedélyezése


Ez a cikk ismerteti, hogyan engedélyezheti a replikációt az azure-beli virtuális gépekhez hozzáadott adatlemezekhez, amelyek már engedélyezve vannak a vészhelyreállításhoz egy másik Azure-régióban az [Azure Site Recovery](site-recovery-overview.md)használatával.

A virtuális gépekhez hozzáadott lemez replikációjának engedélyezése a felügyelt lemezekkel rendelkező Azure virtuális gépek számára támogatott.

Amikor új lemezt ad hozzá egy azure-beli virtuális géphez, amely egy másik Azure-régióba replikál, a következő történik:

-   A virtuális gép replikációs állapota figyelmeztetést jelenít meg, és a portálon egy megjegyzés tájékoztatja, hogy egy vagy több lemez védelemre alkalmas.
-   Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a lemez kezdeti replikációja után eltűnik.
-   Ha úgy dönt, hogy nem engedélyezi a lemez replikációját, a figyelmeztetés elvetése is kiválasztható.

![Új lemez hozzáadva](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Előkészületek

Ez a cikk feltételezi, hogy már beállította a vészhelyreállítást a virtuális géphez, amelyhez hozzáadja a lemezt. Ha még nem, kövesse az [Azure-azure-ba vész-helyreállítási oktatóanyag.](azure-to-azure-tutorial-enable-replication.md)

## <a name="enable-replication-for-an-added-disk"></a>Replikáció engedélyezése a hozzáadott lemezen

Ha engedélyezni szeretné egy hozzáadott lemez replikációját, tegye a következőket:

1. A tárolóban > **replikált elemek,** kattintson a virtuális gép, amelyhez hozzáadta a lemezt.
2. Kattintson a **Lemezek gombra,** majd jelölje ki azt az adatlemezt, amelynek replikációját engedélyezni szeretné (ezek a lemezek **Nem védett állapotúak).**
3.  A **Lemez részletei csoportban**kattintson a **Replikáció engedélyezése gombra.**

    ![Replikáció engedélyezése a hozzáadott lemezhez](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

A replikációs feladat lehetővé tétele és a kezdeti replikáció befejezése után a rendszer eltávolítja a lemezprobléma replikációs állapotra vonatkozó figyelmeztetését.



## <a name="next-steps"></a>További lépések

[További információ](site-recovery-test-failover-to-azure.md) a tesztfeladat-átvétel futtatásáról.
