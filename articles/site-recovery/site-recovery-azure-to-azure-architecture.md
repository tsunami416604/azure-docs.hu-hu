---
title: "Hogyan működik az Azure virtuális gép replikálása Azure-régiók közötti az Azure Site Recovery?  | Microsoft Docs"
description: "Ez a cikk a összetevők és használható, ha az Azure virtuális gépek replikálása az Azure Site Recovery szolgáltatás használatával Azure-régiók közötti architektúra áttekintése."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Hogyan működik a Azure virtuális gép replikációs a Site Recovery?


Ez a cikk ismerteti a összetevőiről és folyamataitól részt replikálásához és helyreállítása Azure virtuális gépek (VM) több régióban másik használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

>[!NOTE]
>A Site Recovery szolgáltatásban az Azure virtuális gép replikációs jelenleg előzetes verzió.

Megjegyzéseket a cikk alján tehet, kérdéseket pedig az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi ábrán egy Azure virtuális környezetben (a példában az USA keleti régiója helye) egy adott régióban magas szintű áttekintést nyújt. Egy Azure virtuális környezetben:
- Storage-fiókok elosztva lemezek alkalmazásokat futtathat virtuális gépeken.
- A virtuális gépeket tartalmazhat egy vagy több alhálózatot a virtuális hálózaton belül.

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Tudnivalók a telepítési előfeltételek és a követelmények a [támogatási mátrix](site-recovery-support-matrix-azure-to-azure.md).

## <a name="replication-process"></a>Replikációs folyamat

### <a name="step-1"></a>1. lépés

Ha engedélyezi az Azure virtuális gép replikációs az Azure portálon, a következő ábra és táblázat megjelenített erőforrások automatikusan létrejönnek a cél régióban. Alapértelmezés szerint erőforrások forrásbeállítások régió alapján hozzák létre. Testre szabhatja a célként megadott beállításokat, szükség szerint. [További információk](site-recovery-replicate-azure-to-azure.md).

![Engedélyezze a replikálási folyamat, 1. lépés](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

**Erőforrás** | **Részletek**
--- | ---
**Cél-erőforráscsoport** | Az erőforráscsoport, amelybe a replikált virtuális gépek a feladatátvételt követően tartoznak.
**Virtuális hálózati cél** | A virtuális hálózatot, amelyben replikált virtuális gépek a feladatátvétel után. A hálózatleképezés jön létre a forrás és cél virtuális hálózatok között, és ez fordítva is igaz.
**Gyorsítótár-storage-fiókok** | A forrás virtuális gépeken változásai replikálódnak a cél tárfiókkal, mielőtt azok nyomon vagy a gyorsítótár storage-fiókot a célhely számára küldött. Ez biztosítja, hogy a virtuális gépen az üzemi alkalmazások gyakorolt minimális hatás mellett.
**Cél storage-fiókok**  | Storage-fiók, amelyhez a rendszer replikálja az adatokat a célhelyre.
**Cél rendelkezésre állási csoportok**  | Rendelkezésre állási készletek, amelyek a replikált virtuális gépek a feladatátvétel után.

### <a name="step-2"></a>2. lépés

Replikálás engedélyezve van, mint a Site Recovery bővítmény mobilitási szolgáltatás automatikusan települ a virtuális Gépen. A következő történik:

1. A virtuális gép regisztrálva van a Site Recovery szolgáltatással.

2. A virtuális gép folyamatos replikálásra van konfigurálva. A virtuális gép lemezeken adatírás folyamatosan átkerülnek a forráshely a gyorsítótár storage-fiókot.

   ![Engedélyezze a replikálási folyamat, 2. lépés](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > A Site Recovery soha nem kell a VM bejövő kapcsolatot. A virtuális gép van szüksége a Site Recovery szolgáltatás URL-címek vagy IP-címek, az Office 365 authentication URL-címek vagy IP-címek és a gyorsítótár tárolási fiók IP-címek csak a kimenő kapcsolat. További információkért lásd: a [hálózati útmutatót az Azure virtuális gépek replikálásához](site-recovery-azure-to-azure-networking-guidance.md) cikk.

## <a name="continuous-replication-process"></a>Folyamatos replikálási folyamat

Miután folyamatos replikálás működik-e, folyamat a lemezírásokat azonnal átkerülnek a gyorsítótár tárfiókot. A Site Recovery feldolgozza az adatokat, és elküldi a céloldali tárfiók. Az adatok feldolgozása után helyreállítási pontok létrejönnek a céloldali tárfiók néhány percenként.

## <a name="failover-process"></a>Feladatátvételi folyamat

Kezdeményezzen feladatátvételt, ha a célerőforrás-csoport virtuális célhálózat, célként megadott alhálózat, a virtuális gépek jönnek létre, és a célként megadott rendelkezésre állási csoport. A feladatátvétel során bármely helyreállítási pontot is használhat.

![Feladatátvételi folyamat](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Következő lépések

- További tudnivalók [hálózati](site-recovery-azure-to-azure-networking-guidance.md) az Azure Virtuálisgép-replikációt.
- A bemutató lépéseit követve [Azure virtuális gépek replikálása.](site-recovery-azure-to-azure.md)
