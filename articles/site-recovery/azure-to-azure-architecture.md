---
title: "Azure Site Recovery architektúrájáról Azure az Azure-bA replikációs |} Microsoft Docs"
description: "Ez a cikk a összetevők és használható, ha az Azure virtuális gépek replikálása az Azure Site Recovery szolgáltatással Azure-régiók közötti architektúra áttekintése."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 409dd26cc1dfcb1c562d175a43e842b213501d03
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-to-azure-replication-architecture"></a>Azure-az Azure-bA replikációs architektúrája


Ez a cikk ismerteti a használható, ha a replikálása, feladatátvétele és helyreállítása Azure virtuális gépek (VM) használó Azure régiók közötti architektúra a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

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

Ha engedélyezi az Azure Virtuálisgép-replikációt, az alábbi erőforrások automatikusan létrejönnek a cél a régióban, a forrás régió beállításai alapján. Testre szabhatja a cél erőforrások beállításokat szükség szerint.

![Engedélyezze a replikálási folyamat, 1. lépés](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Erőforrás** | **Részletek**
--- | ---
**Cél-erőforráscsoport** | Az erőforráscsoport, amelybe a replikált virtuális gépek a feladatátvételt követően tartoznak.
**Virtuális hálózati cél** | A virtuális hálózatot, amelyben replikált virtuális gépek a feladatátvétel után. A hálózatleképezés jön létre a forrás és cél virtuális hálózatok között, és ez fordítva is igaz.
**Gyorsítótár-storage-fiókok** | Ahhoz a forrás virtuális gép változásai replikálódnak a cél tárfiókkal, ezeket nyomon követheti és a gyorsítótár tárfiók a forráshely küldött. Ez a lépés biztosítja a virtuális Gépen futó termelési alkalmazások gyakorolt minimális hatás mellett.
**Cél storage-fiókok**  | Storage-fiók, amelyhez a rendszer replikálja az adatokat a célhelyre.
**Cél rendelkezésre állási csoportok**  | Rendelkezésre állási készletek, amelyek a replikált virtuális gépek a feladatátvétel után.

### <a name="step-2"></a>2. lépés

Replikálás engedélyezve van, mint a Site Recovery bővítmény mobilitási szolgáltatás automatikusan települ a virtuális Gépen:

1. A virtuális gép regisztrálva van a Site Recovery szolgáltatással.

2. A virtuális gép folyamatos replikálásra van konfigurálva. A virtuális gép lemezeken adatírás folyamatosan átkerülnek a gyorsítótár storage-fiókot, a forráshely.

   ![Engedélyezze a replikálási folyamat, 2. lépés](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 A Site Recovery soha nem kell a VM bejövő kapcsolatot. Csak a kimenő kapcsolat a következőkre van szükség.

 - Site Recovery szolgáltatás URL-címek vagy IP-címek
 - Az Office 365 authentication URL-címek vagy IP-címek
 - Gyorsítótár tárolási fiók IP-címek

Ha engedélyezi a virtuális Gépre kiterjedő konzisztencia, a replikációs csoport gépek kommunikálhatnak egymással 20004 porton keresztül. Ellenőrizze, hogy nem blokkolja-e a belső kommunikáció 20004 porton keresztül a virtuális gépek közötti tűzfal készüléket.

> [!IMPORTANT]
Ha azt szeretné, hogy a Linux virtuális gépek a replikációs csoport részeként, győződjön meg arról, a kimenő adatforgalmat a port 20004 manuálisan megnyitott állapotokban az adott Linux verzió útmutatást.

### <a name="step-3"></a>3. lépés

Miután folyamatos replikálásra van folyamatban, folyamat a lemezírásokat azonnal átkerülnek a gyorsítótár tárfiókot. A Site Recovery feldolgozza az adatokat, és elküldi a céloldali tárfiók. Az adatok feldolgozása után helyreállítási pontok létrejönnek a céloldali tárfiók néhány percenként.

## <a name="failover-process"></a>Feladatátvételi folyamat

Kezdeményezzen feladatátvételt, ha a virtuális gépek létrehozása a célként megadott erőforráscsoportja, a cél virtuális hálózat, a cél alhálózathoz, és a célként megadott rendelkezésre állási csoport. A feladatátvétel során bármely helyreállítási pontot is használhat.

![Feladatátvételi folyamat](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális gép távoli másodlagos régióba.
