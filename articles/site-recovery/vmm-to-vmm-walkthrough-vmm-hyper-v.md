---
title: "A VMM és a Hyper-V beállítása az Azure Site Recovery egy másodlagos helyre replikációt |} Microsoft Docs"
description: "Ismerteti, hogyan állíthat be a System Center VMM-kiszolgáló és a Hyper-V gazdagépek a VMM egy másodlagos hely replikáció."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>4. lépés: Állítsa be a VMM és a Hyper-V a Hyper-V virtuális gép replikációs egy másodlagos helyre 

Után előkészítése után a hálózatkezeléshez, a System Center Virtual Machine Manager (VMM) kiszolgálók és Hyper-V gazdagépeken a Hyper-V virtuális gép (VM) replikáció egy másodlagos hely beállítása használatával [Azure Site Recovery](site-recovery-overview.md) az Azure portálon. 

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.



## <a name="prepare-vmm-servers"></a>VMM-kiszolgáló előkészítése 

A telepítés előkészítéséhez:


1. Győződjön meg arról, hogy a VMM-kiszolgálók megfelelnek a [igényeinek támogatására](site-recovery-support-matrix-to-sec-site.md#on-premises-servers), és [telepítésének előfeltételei](vmm-to-vmm-walkthrough-prerequisites.md).
2. Ellenőrizze, hogy a VMM-kiszolgáló csatlakozik az internethez, és elérheti az alábbi URL-címek.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Ha IP-címeken alapuló tűzfalszabályokat használ, ellenőrizze, hogy engedélyezik-e az Azure-ral való kommunikációt.
    - Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
    - Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).
3. Győződjön meg arról, hogy a VMM-kiszolgáló [a hálózatleképezés előkészítése](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)


## <a name="prepare-hyper-v-hostsclusters"></a>Hyper-V gazdagép vagy fürt előkészítése

1. Győződjön meg arról, hogy a Hyper-V gazdagép vagy fürt ahhoz a [igényeinek támogatására](site-recovery-support-matrix-to-sec-site.md#on-premises-servers), és [telepítésének előfeltételei](vmm-to-vmm-walkthrough-prerequisites.md).
2. Ellenőrizze a követelmények [Hyper-V virtuális gépek](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
3. Győződjön meg arról [hálózati](site-recovery-support-matrix-to-sec-site.md#network-configuration) és [tárolási](site-recovery-support-matrix-to-sec-site.md#storage) követelményeinek.
4. Ellenőrizze, hogy a Hyper-V-gazdagépek csatlakozik az internethez, és hozzáférhetnek az URL-címek.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Ha IP-címeken alapuló tűzfalszabályokat használ, ellenőrizze, hogy engedélyezik-e az Azure-ral való kommunikációt.
    - Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
    - Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

## <a name="prepare-for-single-server-deployment"></a>Az egykiszolgálós telepítés előkészítése


Ha csak egyetlen VMM-kiszolgáló, virtuális gépek replikálhatja a Hyper-V gazdagépek a VMM-felhőben való [Azure](hyper-v-site-walkthrough-overview.md) vagy másodlagos VMM-felhőhöz, ebben a dokumentumban leírt módon. Az első lehetőség zökkenőmentes felhők közötti replikálása nem ajánlott.

Ha szeretné, hogy bizonyos felhők replikálása, replikálhatja egyetlen önálló VMM-kiszolgálóhoz, vagy felhőbe archivált Windows fürtben telepített egyetlen VMM-kiszolgálóhoz

### <a name="replicate-with-a-standalone-vmm-server"></a>Az önálló VMM-kiszolgáló replikálása

Ebben az esetben az egyetlen VMM-kiszolgáló telepítése virtuális gépként az elsődleges helyen, és a virtuális gép replikálása egy másodlagos helyre, a Site Recovery és a Hyper-V replika használata.

1. **Állítsa be a Hyper-V virtuális gép VMM**. Javasoljuk, hogy az azonos virtuális gépen a VMM által használt SQL Server-példányhoz tartozó-közös elhelyezése. Ez menti a idő csak egy virtuális gépnél létrehozni. Ha a használni kívánt távoli példányát az SQL Server és a nem tervezett kimaradás következik be, kell helyreállítania ezen példányát, mielőtt VMM próbál helyreállítani.
2. **Ellenőrizze a VMM-kiszolgáló rendelkezik-e konfigurálva, legalább két felhők**. Egy felhőalapú tartalmazni fogja a virtuális gépeket szeretne replikálni, és az egyéb felhő erre a célra a másodlagos helyre. A felhőbe, amely tartalmazza a védeni kívánt virtuális gépek meg kell felelnie [Előfeltételek](#prerequisites).
3. Állítsa be a Site Recovery, ebben a cikkben leírtak szerint. Hozzon létre és regisztrálja a VMM-kiszolgálót a tárolóban, replikációs házirend beállítása, és engedélyezze a replikálást. A forrás és cél VMM-nevek ugyanaz lesz. Adja meg, hogy a kezdeti replikáció a hálózaton keresztül történik.
4. A hálózatleképezés beállításához képezze le az elsődleges felhőbe a Virtuálisgép-hálózatot a másodlagos felhőre vonatkozó Virtuálisgép-hálózathoz.
5. A Hyper-V Manager konzolon engedélyezze a Hyper-V replika a Hyper-V gazdagépen, amely tartalmazza a VMM virtuális gép, és engedélyezze a replikálást a virtuális Gépen. Győződjön meg arról, hogy a hely helyreállítását követően annak érdekében, hogy a Hyper-V replika beállításait nem felülbírálják a Site Recovery által védett felhőt a VMM virtuális gép nem tölti fel.
6. A helyreállítási terv feladatátvételi létrehozásakor a forrás és cél használata megegyező VMM-kiszolgálóhoz.
7. Egy teljes leállás feladatátvételt, és a következőképpen helyre:

   1. Nem tervezett feladatátvétel a másodlagos helyen, hogy áthelyezze a feladatokat a másodlagos hely elsődleges VMM virtuális gép a Hyper-V Manager konzolon.
   2. Győződjön meg arról, hogy a VMM virtuális gép működik-e, és fut, és a tárolóban, nem tervezett feladatátvétel az elsődleges, másodlagos a felhőből a virtuális gépek a feladatátvétel. Véglegesítse a feladatátvételt, és válassza ki a másik helyreállítási pontot, ha szükséges.
   3. A nem tervezett feladatátvétel végeztével összes erőforrás elérhető az elsődleges hely újra.
   4. Az elsődleges hely újra, a Hyper-V Manager konzol a másodlagos helyen érhető el, ha a VMM virtuális gép fordított replikáció engedélyezése. A másodlagos az elsődleges replikáció a virtuális gép elindul.
   5. A tervezett feladatátvétel végrehajtása a Hyper-V Manager konzolon a másodlagos helyen, hogy áthelyezze a feladatokat a VMM virtuális gép az elsődleges helyre. Hajtsa végre a feladatátvételt. A fordított replikáció, majd engedélyezze, hogy a VMM virtuális gép újból replikálódik az elsődleges másodlagos.
   6. A Recovery Services-tároló engedélyezheti a munkaterhelési virtuális gépek replikálásához azokat az elsődleges másodlagos elindítani a fordított replikációt.
   7. A Recovery Services-tároló futtassa a tervezett feladatátvételt a feladat-visszavételt a munkaterhelési virtuális gépekhez az elsődleges helyre. A feladatátvételt fejezze be a véglegesítése. Engedélyezze a fordított replikációt az elsődleges, másodlagos történő replikálása a munkaterhelési virtuális gépek indítására.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>Replikálhat a felhőbe archivált VMM-fürthöz

Helyett egy önálló VMM-kiszolgáló telepítése egy másodlagos helyre replikált virtuális gépként, akkor is használhatja a VMM magas rendelkezésre állású Windows feladatátvevő fürtben virtuális gépként telepíti azokat. Így lehetővé teszi a rugalmas munkaterhelést és a hardver meghibásodása elleni védelem. A Site Recovery telepítése a VMM-Virtuálisgép kell telepíteni a felhőbe archiválási fürt földrajzilag különálló helyen. Ehhez tegye a következőket:

1. Telepítse a VMM egy virtuális gépen egy Windows feladatátvevő fürtben, és jelölje be a futtató a kiszolgáló magas rendelkezésre állású telepítés során.
2. A VMM által használt SQL Server-példány replikálni kell az SQL Server AlwaysOn rendelkezésre állási csoportok, úgy, hogy az adatbázis másolatát a másodlagos helyen.
3. Kövesse az utasításokat ebben a cikkben, hozzon létre egy tárolót, a kiszolgáló regisztrálásához és védelmének beállítása. A Recovery Services-tárolónak a fürt minden VMM-kiszolgálóhoz regisztrálnia kell. Ehhez az szükséges, telepítse a szolgáltató aktív csomópontra, és regisztrálja a VMM-kiszolgálót. A szolgáltató telepíti a többi csomóponton.
4. Nem tervezett kimaradás esetén a VMM-kiszolgáló és a megfelelő SQL Server-adatbázist átadja a feladatokat, és a másodlagos hely érhető el.



## <a name="next-steps"></a>Következő lépések

Ugrás a [5. lépés: állítson be egy tároló](vmm-to-vmm-walkthrough-create-vault.md).