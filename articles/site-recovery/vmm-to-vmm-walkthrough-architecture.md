---
title: "A Hyper-V-ről az Azure Site Recovery használatával egy másodlagos helyre történő replikáció architektúrájának áttekintése | Microsoft Docs"
description: "Ez a cikk áttekintést ad a helyszíni Hyper-V virtuális gépek másodlagos System Center VMM-helyre történő, az Azure Site Recoveryvel végzett replikálásakor használt architektúráról."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.contentlocale: hu-hu
ms.lasthandoff: 08/01/2017

---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>1. lépés: Az architektúra áttekintése a Hyper-V másodlagos helyre történő replikációjához

Ez a cikk azokat az összetevőket és folyamatokat ismerteti, amelyek részt vesznek a System Center Virtual Machine Manager- (VMM-) felhőkben lévő helyszíni Hyper-V virtuális gépeknek (VM-eknek) az Azure Portalon elérhető [Azure Site Recovery](site-recovery-overview.md) szolgáltatással egy másodlagos VMM-helyre történő replikációjában.

Megjegyzéseit a cikk alján, vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.



## <a name="architectural-components"></a>Az architektúra összetevői

A Hyper-V-alapú virtuális gépek másodlagos VMM-helyre való replikálásához a következőkre lesz szüksége.

**Összetevő** | **Hely** | **Részletek**
--- | --- | ---
**Azure** | Előfizetés az Azure-ban. | Létre kell hoznia egy Recovery Services-tárolót az Azure-előfizetésében a VMM-helyek közti replikáció vezényléséhez és felügyeletéhez.
**VMM-kiszolgáló** | Szüksége lesz egy elsődleges és egy másodlagos VMM-helyre. | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen 
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben. | A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

## <a name="replication-process"></a>Replikációs folyamat

1. Az Azure-fiók beállítása, egy Recovery Services-tároló létrehozása, és a replikálni kívánt elemek megadása.
2. A replikáció forrásának és céljának konfigurálása, ami magában foglalja az Azure Site Recovery Provider telepítését a VMM-kiszolgálókra, valamint a Microsoft Azure Recovery Services-ügynök telepítését a Hyper-V gazdakiszolgálókra.
3. Egy replikációs házirend létrehozása a forrás VMM-felhőhöz. A házirendet ezután a rendszer minden, a felhőben lévő gazdagépen található virtuális gépre alkalmazza.
4. Az egyes VMM-ek replikálásának engedélyezése után és a virtuális gépek kezdeti replikációja lezajlik a megadott beállítások szerint.
5. A kezdeti replikáció után megkezdődik a változáskülönbözetek replikációja. Az elemek nyomon követett módosításait a rendszer .hrl fájlokban tárolja.


![Két helyszíni hely közötti replikálás](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. Ha elvégez egy nem tervezett feladatátvételt egy másodlagos helyre, a feladatátvétel után a másodlagos hely gépei nem engedélyezettek védelemhez vagy replikáláshoz. Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
5. Ekkor véglegesíti a feladatátvételt, hogy hozzáférhessen a replika virtuális gép számítási feladataihoz.
6. Amikor az elsődleges hely újra elérhetővé válik, fordított replikálást hajt végre a másodlagos helyről az elsődleges helyre való replikáláshoz. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
7. Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.



## <a name="next-steps"></a>Következő lépések

Folytassa a [2. lépés: Az előfeltételek és korlátozások áttekintése](vmm-to-vmm-walkthrough-prerequisites.md) művelettel.

