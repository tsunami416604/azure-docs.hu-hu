---
title: Architektúra-Hyper-V vész-helyreállítási egy másodlagos helyen az Azure Site Recovery
description: Ez a cikk áttekintést nyújt a helyszíni Hyper-V virtuális gépek vész-helyreállítási architektúrájának áttekintését egy másodlagos System Center VMM-webhelyre az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133012"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektúra – Hyper-V replikáció másodlagos helyre

Ez a cikk azokat az összetevőket és folyamatokat ismerteti, amelyek részt vesznek a System Center Virtual Machine Manager- (VMM-) felhőkben lévő helyszíni Hyper-V virtuális gépeknek (VM-eknek) az Azure Portalon elérhető [Azure Site Recovery](site-recovery-overview.md) szolgáltatással egy másodlagos VMM-helyre történő replikációjában.
a

## <a name="architectural-components"></a>Az architektúra összetevői

Az alábbi táblázat és ábra magas szintű nézetet biztosít a Hyper-V replikációhoz használt összetevőkről egy másodlagos helyre.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés | Létre kell hoznia egy Recovery Services-tárolót az Azure-előfizetésében a VMM-helyek közti replikáció vezényléséhez és felügyeletéhez.
**VMM-kiszolgáló** | Szüksége lesz egy elsődleges és egy másodlagos VMM-helyre. | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben. | A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

**A helyszíni és a helyszíni architektúrához**

![Két helyszíni hely közötti replikálás](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikációs folyamat

1. A kezdeti replikáció aktiválásakor egy [Hyper-V VM pillanatkép pillanatkép](https://technet.microsoft.com/library/dd560637.aspx) készül.
2. Virtuális merevlemezek a virtuális gépen replikálódik egyenként, a másodlagos helyre.
3. Ha a lemez változások a kezdeti replikáció közben történnek, a Hyper-V replikációs követő hyper-V replikációs naplóként (.hrl) követi a módosításokat. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy társított .hrl fájl, amelyet a rendszer a másodlagos helyre küld. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. Amikor a kezdeti replikáció befejeződik, a virtuális gép pillanatképe törlődik, és a különbözeti replikáció megkezdődik.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

- Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó helyreállítási terveket is.
- Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
    - Ha nem tervezett feladatátvételt hajt végre egy másodlagos helyre, miután a másodlagos helyen lévő feladatátvételi gépek nem védettek.
    - Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
- A kezdeti feladatátvétel futtatása után véglegesíti azt, hogy indítsa el a számítási feladatok elérését a replika virtuális gép.
- Ha az elsődleges hely ismét elérhető, visszaléphet.
    - Kezdeményezi a fordított replikációt, hogy elkezdje a replikálást a másodlagos helyről az elsődleges re. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
    - Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.



## <a name="next-steps"></a>További lépések


Az [oktatóanyag követéséhez](hyper-v-vmm-disaster-recovery.md) engedélyezze a Hyper-V replikációt a VMM-felhők között.
