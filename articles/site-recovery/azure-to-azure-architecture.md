---
title: Azure-bA replikációs architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk az Azure virtuális gépek, az Azure Site Recovery szolgáltatással az Azure-régiók között vész-helyreállítási beállításakor használt összetevőkről és architektúráról áttekintést nyújt.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: b19b6fcb3e5e939f057b063ef2dd8b946a31abfe
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838956"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure-bA vész-helyreállítási architektúra


Ez a cikk ismerteti a vész-helyreállítási a replikálása, feladatátvétele és helyreállítása Azure virtuális gépeken (VM) Azure-régiók használatával között telepítéséhez használt architektúra a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.




## <a name="architectural-components"></a>Az architektúra összetevői

A következő ábra egy adott régióban (az ebben a példában az USA keleti régiójában) egy Azure virtuális gép környezet magas szintű nézetét jeleníti meg. Egy Azure-beli Virtuálisgép-környezetben:
- Alkalmazások felügyelt lemezekkel rendelkező virtuális gépeken is fut, vagy nem felügyelt lemezek elosztva a storage-fiókok.
- Egy virtuális hálózaton belül egy vagy több alhálózatot a virtuális gépek is szerepelnek.


**Azure-Azure közötti replikáció**

![ügyfél-környezet](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Replikációs folyamat

### <a name="step-1"></a>1. lépés

Ha engedélyezi az Azure virtuális gép replikációja, a következő erőforrások automatikusan jönnek létre a célrégióban a forrás területi beállítások alapján. Testre szabhatja a céloldali erőforrások beállításokat szükség szerint.

![Replikációs folyamat, 1. lépés engedélyezése](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Erőforrás** | **Részletek**
--- | ---
**Céloldali erőforráscsoport** | Az erőforráscsoport, a replikált virtuális gépek tartozik, amelyhez a feladatátvételt követően. Ez az erőforráscsoport helyét is lehet bármely Azure-régiót kivéve az Azure-régió, amelyben a forrás virtuális gépek futnak.
**Cél virtuális hálózattal** | A virtuális hálózatot, amelyben a replikált virtuális gépek találhatók a feladatátvételt követően. A hálózatleképezés létrejön a forrás- és virtuális hálózatok között, és fordítva.
**Gyorsítótárfiókok** | Forrás virtuális gép változásai replikálódnak a célként megadott tárfiókhoz, mielőtt ezeket nyomon követi és a forráshelyen lévő gyorsítótárfiókot küldött. Ez a lépés biztosítja, hogy éles üzemi alkalmazások pedig a virtuális gépen futó csak minimális hatással van.
**Cél tárfiókok (Ha a forrás virtuális gép nem használ felügyelt lemezeket)**  | Storage-fiókok a célhelyen, amelyhez a rendszer replikálja az adatokat.
** Replika felügyelt lemezek (Ha a forrás virtuális gép a felügyelt lemezek) **  | A felügyelt lemezek, amelyhez adatokat a rendszer replikálja a célhelyen.
**Cél rendelkezésre állási csoportok**  | Rendelkezésre állási csoportok, amelyek a replikált virtuális gépek a feladatátvételt követően találhatók.

### <a name="step-2"></a>2. lépés

Replikálás engedélyezve van, mert a Site Recovery-bővítmény a mobilitási szolgáltatás automatikusan települ a virtuális gépen:

1. A virtuális gép Site Recovery regisztrálva van.

2. A folyamatos replikáció a virtuális gép van konfigurálva. A Virtuálisgép-lemezeken lévő adatírásokat folyamatosan átkerülnek a gyorsítótárfiókot, a forráshelyen.

   ![Replikációs folyamat, 2. lépés engedélyezése](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 A Site Recovery soha nem kell a virtuális gép bejövő kapcsolatot. Csak a kimenő kapcsolatok a következőkre van szükség.

 - Site Recovery szolgáltatás URL-címek vagy IP-címe
 - Az Office 365 hitelesítési URL-címek vagy IP-címe
 - Gyorsítótár tárolási fiók IP-címek

Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt.

> [!IMPORTANT]
Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.

### <a name="step-3"></a>3. lépés

Miután a folyamatos replikáció folyamatban van, Lemezírások azonnal átkerülnek a gyorsítótárfiókba. A Site Recovery feldolgozza az adatokat, és elküldi azt a célként megadott tárfiók vagy replika felügyelt lemezeket. Az adatok feldolgozása után helyreállítási pontok létrejönnek a célként megadott tárfiók néhány perces időközönként.

## <a name="failover-process"></a>Feladatátvételi folyamatot

Kezdeményezzen feladatátvételt, ha a virtuális gépek létrehozása a céloldali erőforráscsoport, célként megadott virtuális hálózat, alhálózat, és a cél rendelkezésre állási csoportban. A feladatátvétel során a bármelyik helyreállítási pontot is használhat.

![Feladatátvételi folyamatot](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális Gépen egy másodlagos régióba.
