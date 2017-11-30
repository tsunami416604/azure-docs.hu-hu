---
title: "Futtassa a vész-helyreállítási részletezési Azure virtuális gépek egy másodlagos Azure-régió, az Azure Site Recovery (előzetes verzió)"
description: "Útmutató: Azure virtuális gépek futtatásához a vész-helyreállítási részletezési egy másodlagos Azure-régió, az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d763865bb0b86b1bdf15258a3caf36561dd3385c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>A vész-helyreállítási részletezési futtassa az Azure virtuális gépek egy másodlagos Azure régióra (előzetes verzió)

A [Azure Site Recovery](../site-recovery-overview.md) szolgáltatás által az üzleti alkalmazások, és az elérhető futtató tervezett és nem tervezett leállások során hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégiát. A Site Recovery koordinálja a vész-helyreállítási a helyi gép és az Azure virtuális gépek (VM), beleértve a replikáció, feladatátvétel és helyreállítás, valamint a kezelésére.

Az oktatóanyag bemutatja, hogyan futtathat egy vész-helyreállítási részletezési egy Azure virtuális gép, egy Azure-régióban a másikra, a feladatátvételi teszt. A részletezés érvényesíti a replikációs stratégiájának adatvesztés vagy leállás nélkül, és nincs hatással az éles környezetben. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Előfeltételek ellenőrzése
> * Egy virtuális a feladatátvételi teszt futtatása

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy a feladatátvételi tesztet futtatni, azt javasoljuk, hogy ellenőrizze a virtuális gép tulajdonságait győződjön meg arról, hogy minden a várt módon működik.  A virtuális gép tulajdonságainak hozzáférést **replikált elemek**. A **Essentials** panel gépek beállítások és állapotával kapcsolatos adatokat jeleníti meg.
- Azt javasoljuk, hogy egy külön Azure Virtuálisgép-hálózatot a feladatátvételi tesztet, és nem a replikációs engedélyezésekor be lett állítva alapértelmezett hálózati használja.


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A **beállítások** > **replikált elemek**, kattintson a virtuális gép **+ feladatátvételi teszt** ikonra.

2. A **feladatátvételi teszt**, válasszon ki egy helyreállítási pontot a a feladatátvételre használni:

   - **Legújabb feldolgozott**: a virtuális gép átadja a feladatokat a legutóbbi helyreállítási pontot, a Site Recovery szolgáltatás által feldolgozott. Az időbélyeg jelenik meg. Ezzel a beállítással nem van feldolgozásával töltött idő adatokat, így biztosít egy alacsony RTO (helyreállítási idő célkitűzése)
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak. Az időbélyeg jelenik meg.
   - **Egyéni**: válassza ki a helyreállítási pontot.

3. Jelölje ki a cél Azure csatlakoznak másodlagos régióban mely Azure virtuális gépek virtuális hálózati, a feladatátvételt követően.

4. A feladatátvételi elindításához kattintson **OK**. Nyomon követni, kattintson a virtuális gép tulajdonságainak megnyitásához. Másik lehetőségként kattinthat a **feladatátvételi teszt** feladat a tároló neve > **beállítások** > **feladatok** > **Site Recovery-feladatok**.
5. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure portálon > **virtuális gépek**. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése, és a megfelelő hálózathoz csatlakozik.
6. A virtuális gép feladatátvételi tesztje során létrehozott törléséhez kattintson **karbantartása a feladatátvételi teszt** a replikált cikk vagy a helyreállítási terv. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Éles feladatátvétel futtatása](azure-to-azure-tutorial-failover-failback.md)
