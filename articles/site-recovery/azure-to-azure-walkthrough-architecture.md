---
title: "Tekintse át az Azure virtuális gépek Azure-régiók közötti replikáció architektúrája |} Microsoft Docs"
description: "Ez a cikk a összetevők és használható, ha az Azure virtuális gépek replikálása az Azure Site Recovery szolgáltatással Azure-régiók közötti architektúra áttekintése."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>1. lépés:, Tekintse át az Azure-régiók közötti Azure virtuális gép replikációs architektúrája


Miután megtekintette a [áttekintése lépéseket](azure-to-azure-walkthrough-overview.md) ehhez a központi telepítéshez, a cikkből megtudhatja, hogy az összetevők és használható, ha a replikálása, és az Azure virtuális gépek (VM) helyreállítani egy Azure-régió, egy másik folyamat használatával [ Azure Site Recovery](site-recovery-overview.md).

- A cikk befejezése után kell egy Azure virtuális gép replikációs egy másik régióban működése egyértelműen érthető.
- Megjegyzéseket a cikk alján tehet, kérdéseket pedig az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

>[!NOTE]
>A Site Recovery szolgáltatásban az Azure virtuális gép replikációs jelenleg előzetes verzió.



## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi ábrán egy Azure virtuális környezetben (a példában az USA keleti régiója helye) egy adott régióban magas szintű áttekintést nyújt. Egy Azure virtuális környezetben:
- Storage-fiókok elosztva lemezek alkalmazásokat futtathat virtuális gépeken.
- A virtuális gépeket tartalmazhat egy vagy több alhálózatot a virtuális hálózaton belül.

![ügyfél-környezet](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>Replikációs folyamat

### <a name="step-1"></a>1. lépés

Ha engedélyezi az Azure virtuális gép replikációs az Azure portálon, a következő ábra és táblázat megjelenített erőforrások automatikusan létrejönnek a cél régióban. Alapértelmezés szerint erőforrások forrásbeállítások régió alapján hozzák létre. Testre szabhatja a célként megadott beállításokat, szükség szerint. [További információk](site-recovery-replicate-azure-to-azure.md).

![Engedélyezze a replikálási folyamat, 1. lépés](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

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

   ![Engedélyezze a replikálási folyamat, 2. lépés](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Vegye figyelembe, hogy a Site Recovery soha nem kell-e a bejövő kapcsolatot a virtuális géphez. Csak a Site Recovery szolgáltatás URL-címek vagy IP-címek, az Office 365 authentication URL-címek vagy IP-címek és a gyorsítótár tárolási fiók IP-címek kimenő kapcsolatra van szükség. 

## <a name="continuous-replication-process"></a>Folyamatos replikálási folyamat

Miután folyamatos replikálás működik-e, folyamat a lemezírásokat azonnal átkerülnek a gyorsítótár tárfiókot. A Site Recovery feldolgozza az adatokat, és elküldi a céloldali tárfiók. Az adatok feldolgozása után helyreállítási pontok létrejönnek a céloldali tárfiók néhány percenként.

## <a name="failover-process"></a>Feladatátvételi folyamat

Kezdeményezzen feladatátvételt, ha a célerőforrás-csoport virtuális célhálózat, célként megadott alhálózat, a virtuális gépek jönnek létre, és a célként megadott rendelkezésre állási csoport. A feladatátvétel során bármely helyreállítási pontot is használhat.

![Feladatátvételi folyamat](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>Következő lépések

Ugrás a [2. lépés: Ellenőrizze az Előfeltételek és korlátozások](azure-to-azure-walkthrough-prerequisites.md)
