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
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: f9cd57e47a8463440148b2bcc6c21beacd54382a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Azure-az Azure-bA replikációs architektúrája


Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és helyreállítása Azure virtuális gépek (VM) közötti Azure-régiók, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

>[!NOTE]
>A Site Recovery szolgáltatásban az Azure virtuális gép replikációs jelenleg előzetes verzió.



## <a name="architectural-components"></a>Az architektúra összetevői

A következő ábra egy Azure virtuális környezetben (a példában az USA keleti régiója helye) egy adott régióban magas szintű áttekintést nyújt. Egy Azure virtuális környezetben:
- Storage-fiókok elosztva lemezek alkalmazásokat futtathat virtuális gépeken.
- A virtuális gépeket tartalmazhat egy vagy több alhálózatot a virtuális hálózaton belül.


**Azure-az Azure-bA replikáció**

![ügyfél-környezet](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Replikációs folyamat

### <a name="step-1"></a>1. lépés

Ha engedélyezi az Azure virtuális gép replikációs, az alábbi erőforrások automatikusan létrejönnek a cél régióban forrás régió beállításai alapján. Testre szabhatja a cél erőforrások beállításokat szükség szerint. 

![Engedélyezze a replikálási folyamat, 1. lépés](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Erőforrás** | **Részletek**
--- | ---
**Cél-erőforráscsoport** | Az erőforráscsoport, amelybe a replikált virtuális gépek a feladatátvételt követően tartoznak.
**Virtuális hálózati cél** | A virtuális hálózatot, amelyben replikált virtuális gépek a feladatátvétel után. A hálózatleképezés jön létre a forrás és cél virtuális hálózatok között, és ez fordítva is igaz.
**Gyorsítótár-storage-fiókok** | Mielőtt a forrás virtuális gépek változásai replikálódnak a cél tárfiókkal, ezeket nyomon követheti és a gyorsítótár storage-fiókot a célhely számára küldött. Ez biztosítja, hogy a virtuális gépen az üzemi alkalmazások gyakorolt minimális hatás mellett.
**Cél storage-fiókok**  | Storage-fiók, amelyhez a rendszer replikálja az adatokat a célhelyre.
**Cél rendelkezésre állási csoportok**  | Rendelkezésre állási készletek, amelyek a replikált virtuális gépek a feladatátvétel után.

### <a name="step-2"></a>2. lépés

Replikálás engedélyezve van, mint a Site Recovery bővítmény mobilitási szolgáltatás automatikusan települ a virtuális Gépen:

1. A virtuális gép regisztrálva van a Site Recovery szolgáltatással.

2. A virtuális gép folyamatos replikálásra van konfigurálva. A virtuális gép lemezeken adatírás folyamatosan átkerülnek a gyorsítótár storage-fiókot, a forráshely.

   ![Engedélyezze a replikálási folyamat, 2. lépés](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 A Site Recovery soha nem kell a VM bejövő kapcsolatot. Csak kimenő kapcsolatra van szükség, a Site Recovery szolgáltatás URL-címek vagy IP-címek, az Office 365 authentication URL-címek vagy IP-címek és a gyorsítótár tárolási fiók IP-címek.

### <a name="step-3"></a>3. lépés

Miután folyamatos replikálásra van folyamatban, folyamat a lemezírásokat azonnal átkerülnek a gyorsítótár tárfiókot. A Site Recovery feldolgozza az adatokat, és elküldi a céloldali tárfiók. Az adatok feldolgozása után helyreállítási pontok létrejönnek a céloldali tárfiók néhány percenként.

## <a name="failover-process"></a>Feladatátvételi folyamat

Kezdeményezzen feladatátvételt, ha a virtuális gépek létrehozása a célként megadott erőforráscsoportja, a cél virtuális hálózat, a cél alhálózathoz, és a célként megadott rendelkezésre állási csoport. A feladatátvétel során bármely helyreállítási pontot is használhat.

![Feladatátvételi folyamat](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Következő lépések

Tekintse át a támogatási mátrix oktatóanyag az Azure virtuális gép replikáláshoz másodlagos régióba.
Futtassa a feladatátvételi és a feladat-visszavétel.