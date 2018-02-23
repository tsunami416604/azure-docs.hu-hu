---
title: "Hyper-V replikálás az Azure Site Recovery másodlagoshely-architektúra |} Microsoft Docs"
description: "Ez a cikk áttekintést ad a helyszíni Hyper-V virtuális gépek másodlagos System Center VMM-helyre történő, az Azure Site Recoveryvel végzett replikálásakor használt architektúráról."
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: raynew
ms.openlocfilehash: a6e20b3f1f804617f78413413509d1b5bd476d23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Hyper-V replikáció egy másodlagos helyre

Ez a cikk azokat az összetevőket és folyamatokat ismerteti, amelyek részt vesznek a System Center Virtual Machine Manager- (VMM-) felhőkben lévő helyszíni Hyper-V virtuális gépeknek (VM-eknek) az Azure Portalon elérhető [Azure Site Recovery](site-recovery-overview.md) szolgáltatással egy másodlagos VMM-helyre történő replikációjában.


## <a name="architectural-components"></a>Az architektúra összetevői

A következő tábla és a kép adja meg a Hyper-V replikáció egy másodlagos helyen használt összetevők áttekintése.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Azure-előfizetés | Létre kell hoznia egy Recovery Services-tárolót az Azure-előfizetésében a VMM-helyek közti replikáció vezényléséhez és felügyeletéhez.
**VMM-kiszolgáló** | Szüksége lesz egy elsődleges és egy másodlagos VMM-helyre. | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben. | A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

**A helyszíni helyszíni-architektúra**

![Két helyszíni hely közötti replikálás](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikációs folyamat

1. Kezdeti replikálás kiváltásakor a [Hyper-V virtuális gép pillanatkép](https://technet.microsoft.com/library/dd560637.aspx) pillanatkép készítésének időpontjában.
2. A virtuális Gépen lévő virtuális merevlemezekhez replikált egy másodlagos helyre.
3. Ha a lemezen változások történnek, amíg a kezdeti replikáció folyamatban van. 
4. Ha a kezdeti replikáció befejezését követően, változásreplikálására kezdődik. A Hyper-V Replica Replication Tracker eszköz a módosításokat, mint a Hyper-V replikálási naplók (.hrl). Ezekben a naplófájlokban a lemezek ugyanabban a mappában találhatók. Minden lemezhez tartozik egy .hrl-fájl, a másodlagos helyre küldött. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

- Egyetlen gép feladatátvételt, vagy hozzon létre helyreállítási tervek, több gép feladatainak átvétele számít.
- A tervezett vagy nem tervezett feladatátvétel a helyszíni helyek közötti futtatása. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
    - Ha egy másodlagos helyre, nem tervezett feladatátvétel után a feladatátvevő gépekhez, a másodlagos helyen hajtsa végre a nem védett.
    - Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
- A kezdeti feladatátvétel futtatása után véglegesítheti, az alkalmazások és szolgáltatások eléréséhez a replika virtuális gép elindításához.
- Az elsődleges hely újra nem érhető el, akkor is feladat-visszavételt.
    - Fordított replikáció, az elsődleges a másodlagos hely replikálást indítani elindította. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
    - Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.



## <a name="next-steps"></a>További lépések


Hajtsa végre a [ebben az oktatóanyagban](hyper-v-vmm-disaster-recovery.md) VMM-felhők között a Hyper-V replikáció engedélyezése.
