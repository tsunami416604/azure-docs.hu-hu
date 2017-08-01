---
title: "Hogyan működik a helyszíni gépek replikációja egy másodlagos helyszíni helyre az Azure Site Recoveryben? | Microsoft Docs"
description: "Ez a cikk áttekintést ad a helyszíni virtuális gépek és fizikai kiszolgálók másodlagos helyre történő, az Azure Site Recovery szolgáltatással végzett replikálásakor használt összetevőkről és architektúráról."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.contentlocale: hu-hu
ms.lasthandoff: 06/15/2017


---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Hogyan működik a helyszíni gépek replikációja egy másodlagos helyre a Site Recoveryben?

Ez a cikk bemutatja a helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba történő, az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással végzett replikálásakor használt összetevőket és folyamatokat.

A következőket replikálhatja egy másodlagos helyszíni helyre:
- System Center Virtual Machine Manager-felhőkben (VMM-felhőkben) felügyelt helyszíni Hyper-V virtuális gépek, Hyper-V virtuális gépek Hyper-V fürtökön és különálló gazdagépek.
- Helyszíni VMware virtuális gépek és Windows/Linux fizikai kiszolgálók. Ebben az esetben a replikációt a Scout kezeli.

Megjegyzéseit a cikk alján, vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Hyper-V virtuális gépek replikálása másodlagos helyszíni helyre


### <a name="architectural-components"></a>Az architektúra összetevői

A következőkre lesz szüksége a Hyper-V-alapú virtuális gépek egy másodlagos helyre való replikálásához.

**Összetevő** | **Hely** | **Részletek**
--- | --- | ---
**Azure** | Ehhez szüksége lesz egy Microsoft-fiókra. |
**VMM-kiszolgáló** | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen | Mindegyik VMM-kiszolgálónak csatlakoznia kell az internethez.<br/><br/> Minden kiszolgálón legyen legalább egy VMM-magánfelhő beállított Hyper-V-kapacitásprofillal.<br/><br/> Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A Provider és az Azure közötti kommunikáció biztonságos, titkosított csatornákon történik.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben.<br/><br/> A kiszolgálóknak csatlakozniuk kell az internethez.<br/><br/> A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A forrás Hyper-V gazdakiszolgálón található. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

### <a name="replication-process"></a>Replikációs folyamat

1. Beállítja az Azure-fiókot.
2. Létrehoz egy replikációsszolgáltatás-tárolót a Site Recoveryhez, és konfigurálja a tároló beállításait, például:

    - A replikációs forrást és célt (elsődleges és másodlagos helyek).
    - Az Azure Site Recovery Provider és a Microsoft Azure Recovery Services ügynök telepítése. A Provider VMM-kiszolgálókon, az ügynök pedig az egyes Hyper-V gazdagépeken van telepítve.
    - Létrehoz egy replikációs házirendet a forrás VMM-felhőhöz. A házirendet ezután a rendszer minden, a felhőben lévő gazdagépen található virtuális gépre alkalmazza.
    - Engedélyezi a replikációt a Hyper-V virtuális gépek számára. A kezdeti replikálás a replikációs házirend beállításainak megfelelően történik.
4. Az adatváltozásokat a rendszer nyomon követi, és a változáskülönbözetek replikálása a kezdeti replikálás befejezése után kezdődik meg. Az elemek nyomon követett módosításait a rendszer .hrl fájlokban tárolja.
5. Egy teszt feladatátvétel futtatásával ellenőrzi, hogy minden jól működik-e.

**1. ábra: VMM és VMM közötti replikáció**

![Két helyszíni hely közötti replikálás](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. Ha elvégez egy nem tervezett feladatátvételt egy másodlagos helyre, a feladatátvétel után a másodlagos hely gépei nem engedélyezettek védelemhez vagy replikáláshoz. Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
5. Ekkor véglegesíti a feladatátvételt, hogy hozzáférhessen a replika virtuális gép számítási feladataihoz.
6. Amikor az elsődleges hely újra elérhetővé válik, fordított replikálást hajt végre a másodlagos helyről az elsődleges helyre való replikáláshoz. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
7. Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>VMware virtuális gépek/fizikai kiszolgálók replikálása másodlagos helyre

A VMware-alapú virtuális gépeket és a fizikai kiszolgálókat az InMage Scout használatával replikálhatja egy másodlagos helyre a következő architektúra-összetevők használatával:


### <a name="architectural-components"></a>Az architektúra összetevői

**Összetevő** | **Hely** | **Részletek**
--- | --- | ---
**Azure** | InMage Scout. | Az InMage Scout beszerzéséhez Azure-előfizetésre van szüksége.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket az üzembe helyezés előkészítéséhez.
**Folyamatkiszolgáló** | Az elsődleges helyen található | Helyezze üzembe a folyamatkiszolgálót, amely kezeli az adatok gyorsítótárazását, tömörítését és optimalizálását.<br/><br/> Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**Konfigurációs kiszolgáló** | A másodlagos helyen található | A konfigurációs kiszolgáló végzi az üzemelő példány felügyeleti webhelyen vagy a vContinuum-konzolban végzett felügyeletét, konfigurálását és megfigyelését.
**vContinuum-kiszolgáló** | Választható. Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót. | Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet.
**Fő célkiszolgáló** | A másodlagos helyen található | A fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat.<br/><br/> Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani.<br/><br/> Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. A Unified Agent ügynök nincs telepítve ezen a kiszolgálón.
**VMware ESX/ESXi- és vCenter-kiszolgáló** |  A virtuális gépek ESX-/ESXi-gazdagépeken futnak. A gazdagépeket egy vCenter-kiszolgáló felügyeli | A VMware virtuális gépek replikálásához VMware-infrastruktúrára van szükség.
**Virtuális gépek/fizikai kiszolgálók** |  A replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon telepített Unified Agent. | Ez az ügynök valósítja meg az összetevők közötti kommunikációt.


### <a name="replication-process"></a>Replikációs folyamat

1. Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt.
2. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak.
3. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

**2. ábra: VMware és VMware közötti replikáció**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Következő lépések

A [támogatási mátrix](site-recovery-support-matrix-to-sec-site.md) áttekintése

