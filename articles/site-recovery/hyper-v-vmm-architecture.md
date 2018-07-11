---
title: Hyper-V replikálás másodlagos helyre architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk áttekintést ad a helyszíni Hyper-V virtuális gépek másodlagos System Center VMM-helyre történő, az Azure Site Recoveryvel végzett replikálásakor használt architektúráról.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 0319e4d1cdbd20c39772c659a3e5a1580d8b925b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919648"
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Hyper-V replikálás másodlagos helyre

Ez a cikk azokat az összetevőket és folyamatokat ismerteti, amelyek részt vesznek a System Center Virtual Machine Manager- (VMM-) felhőkben lévő helyszíni Hyper-V virtuális gépeknek (VM-eknek) az Azure Portalon elérhető [Azure Site Recovery](site-recovery-overview.md) szolgáltatással egy másodlagos VMM-helyre történő replikációjában.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő táblázat és grafikus adja meg a másodlagos helyre Hyper-V-replikációhoz használt összetevők magas szintű nézetét.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés | Létre kell hoznia egy Recovery Services-tárolót az Azure-előfizetésében a VMM-helyek közti replikáció vezényléséhez és felügyeletéhez.
**VMM-kiszolgáló** | Szüksége lesz egy elsődleges és egy másodlagos VMM-helyre. | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben. | A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

**A helyi helyszíni architektúra**

![Két helyszíni hely közötti replikálás](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikációs folyamat

1. Kezdeti replikáció akkor aktiválódik, amikor egy [a Hyper-V virtuális gép pillanatképét](https://technet.microsoft.com/library/dd560637.aspx) pillanatkép készítésének időpontjában.
2. A rendszer a virtuális gép virtuális merevlemezeket, a replikált egy másodlagos helyre.
3. Ha lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V Replica Replication Tracker eszköz Hyper-V replikálási naplók (.hrl) formájában nyomon követi a módosításokat. Ezek a naplófájlok a lemezek ugyanabban a mappában találhatók. Minden lemezhez tartozik egy .hrl fájl, amelyet elküld a másodlagos helyen. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét, és a változásreplikációhoz kezdődik.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

- Egyetlen gép feladatátvételét, vagy több gép összehangolt feladatátadását helyreállítási terveket hozhat létre.
- Tervezett vagy nem tervezett feladatátvétel a helyszíni helyek között futtatható. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
    - Ha egy nem tervezett feladatátvételt a másodlagos helyen, hajtsa végre a feladatátvételt a másodlagos hely gépei után nem védett.
    - Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
- A kezdeti feladatátvétel futtatása után véglegesítheti, a munkaterhelés elérése a replika virtuális gép elindításához.
- Ha az elsődleges hely megint elérhetővé válik, visszaadhatja a.
    - Visszirányú replikálás indítása replikálása a másodlagos helyről az elsődleges kezdeményezheti. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
    - Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.



## <a name="next-steps"></a>További lépések


Hajtsa végre a [ebben az oktatóanyagban](hyper-v-vmm-disaster-recovery.md) között a VMM-felhőkben futó Hyper-V-replikáció engedélyezéséhez.
