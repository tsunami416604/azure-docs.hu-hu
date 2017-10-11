---
title: "Egy másodlagos helyre, az Azure Site Recovery szolgáltatással a Hyper-V replikáció engedélyezése |} Microsoft Docs"
description: "Engedélyezze a replikálást a Hyper-V virtuális gépek replikálása az Azure Site Recovery System Center VMM másodlagos hely ismerteti."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>9. lépés: Engedélyezze a Hyper-V virtuális gépek replikálása másodlagos helyre


Miután egy replikációs házirend beállítása a jelen cikk segítségével engedélyezze a replikálást egy másodlagos System Center Virtual Machine Manager (VMM) helyre a helyszíni Hyper-V virtuális gépek (VM), használja [Azure Site Recovery](site-recovery-overview.md).

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.



## <a name="select-vms-to-replicate"></a>Válassza ki a virtuális gépek replikálásához

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. 

    ![A replikáció engedélyezése](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. A **forrás**, válassza ki a VMM-kiszolgáló és a felhőben, amelyben a replikálni kívánt Hyper-V-gazdagépek találhatók. Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. A **cél**, ellenőrizze a másodlagos VMM-kiszolgáló és a felhőben.
4. A **virtuális gépek**, válassza ki a listából védeni kívánt virtuális gépeket.

    ![A virtuális gép védelmének engedélyezése](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

Előrehaladásának nyomon követheti a **Védelemengedélyezési** műveletét **feladatok** > **Site Recovery-feladatok**. Miután a **Védelemvéglegesítési** feladat befejeződik, a kezdeti replikálás befejeződik, és a virtuális gép készen áll a feladatátvételre.

Vegye figyelembe:

- A VMM-konzol virtuális gépek védelmét is engedélyezheti. Kattintson a **Védelemengedélyezési** az eszköztáron a virtuális gép tulajdonságai > **Azure Site Recovery** fülre.
- Replikáció engedélyezése után megtekintheti a virtuális gép a Tulajdonságok **replikált elemek**. Az a **Essentials** irányítópult, láthatja, hogy a virtuális gép és annak állapotát a replikációs házirend kapcsolatos információkat. Kattintson a **tulajdonságok** további részleteket.

## <a name="onboard-existing-vms"></a>A bevezetni meglévő virtuális gépek

Ha meglévő virtuális gépek a VMM Alkalmazásban – a Hyper-V replika replikálja, hogy discoveryt őket az Azure Site Recovery replikációs az alábbiak szerint:

1. Győződjön meg arról, hogy a Hyper-V kiszolgáló, a meglévő virtuális Gépet üzemeltető az elsődleges felhőben található, és, hogy a Hyper-V kiszolgáló, a replika virtuális gépet szolgáltató a másodlagos felhőben található.
2. Győződjön meg arról, hogy a replikációs házirend úgy van konfigurálva, az elsődleges VMM-felhő.
3. Engedélyezze az elsődleges virtuális gép replikálását. Az Azure Site Recovery és VMM győződjön meg arról, hogy az azonos másodpéldány-állomás és a virtuális gép észlel, és Azure Site Recovery lesznek ismét felhasználni, és a megadott beállítások replikálási szolgáltatás használatához.


## <a name="next-steps"></a>Következő lépések

Ugrás a [10. lépés: feladatátvételi teszt futtatása](vmm-to-vmm-walkthrough-test-failover.md).
