---
title: A Windows Server/System Center VMM 2012 R2 frissítése Windows Server 2016-Azure site recovery-re
description: Ismerje meg, hogyan állíthatja be a vészhelyreállítást az Azure For Azure Stack virtuális gépekhez az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954405"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>A Windows Server Server/System Center 2012 R2 VMM frissítése Windows Server/VMM 2016 rendszerre 

Ez a cikk bemutatja, hogyan frissíthető konkrea Windows Server 2012 R2 & SCVMM 2012 R2, amely az Azure Site Recovery szolgáltatással konfigurálva van, windows Server 2016 & SCVMM 2016-ra

A Site Recovery hozzájárul az üzletmenet folytonosságára és a vészhelyreállításra (BCDR) vonatkozó stratégiához. A szolgáltatás biztosítja, hogy a virtuális gép számítási feladatai továbbra is elérhetőek maradnak, ha várható és váratlan kimaradások fordulnak elő.

> [!IMPORTANT]
> Ha frissíti a Windows Server 2012 R2 állomások, amelyek már konfigurálva vannak a replikáció az Azure Site Recovery, kövesse a jelen dokumentumban említett lépéseket. A frissítéshez választott bármely alternatív elérési út nem támogatott állapotokat eredményezhet, és a replikáció megszakadását vagy feladatátvételi képességet eredményezheti.


Ebből a cikkből megtudhatja, hogyan frissítheti a következő konfigurációkat a környezetben:

> [!div class="checklist"]
> * **Az SCVMM által nem kezelt Windows Server 2012 R2 állomások** 
> * **Önálló SCVMM 2012 R2 kiszolgáló által kezelt Windows Server 2012 R2 állomások** 
> * **A magas rendelkezésre állású SCVMM 2012 R2 kiszolgáló által kezelt Windows Server 2012 R2 állomások**


## <a name="prerequisites--factors-to-consider"></a>Előfeltétel & figyelembe vemandó tényezők

A frissítés előtt vegye figyelembe a következőket: -

- Ha olyan Windows Server 2012 R2 állomásokkal rendelkezik, amelyeket nem az SCVMM kezel, és ez egy önálló környezetbeállítás, a frissítés végrehajtása kor megszakad a replikáció.
- Ha az SCVMM 2012 R2 telepítésekor a *"nem tárolja a kulcsaimat az Active Directoryban az Elosztott kulcskezelés" lehetőséget*választotta, a frissítések nem lesznek sikeresek.

- Ha a System Center 2012 R2 VMM-et használja, 

    - A VMM adatbázis-adatainak ellenőrzése: **VMM** -> **konzolbeállítások** -> **Általános** -> **adatbázis-kapcsolat**
    - A System Center virtuálisgép-kezelő ügynök szolgáltatásához használt szolgáltatásfiókok ellenőrzése
    - Győződjön meg arról, hogy rendelkezik a VMM-adatbázis biztonsági másolatával.
    - Jegyezze fel az érintett SCVMM-kiszolgálók adatbázisnevét. Ez a **VMM konzol** -> **beállítások** -> **általános** -> **adatbázis-kapcsolatára** való navigálással érhető el
    - Jegyezze fel a VMM-azonosítót mind a 2012R2 elsődleges és helyreállítási VMM-kiszolgálók. A VMM-azonosító a "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup" beállításjegyzékben található.
    - Győződjön meg arról, hogy a fürthöz hozzáadott új SCVMM-ek neve megegyezik a korábban volt nevekkel. 

- Ha két, mindkét oldalon SCVMM által kezelt hely között replikál, győződjön meg arról, hogy először frissíti a helyreállítási oldalt az elsődleges oldal frissítése előtt.
  > [!WARNING]
  > Az SCVMM 2012 R2 frissítése közben az Elosztott kulcskezelés csoportban válassza a **titkosítási kulcsok Active Directoryban való tárolásához.** Gondosan válassza ki a szolgáltatásfiók beállításait és az elosztott kulcskezelést. A választás alapján előfordulhat, hogy a titkosított adatok, például a sablonokban lévő jelszavak nem érhetők el a frissítés után, és potenciálisan befolyásolhatják a replikációt az Azure Site Recovery szolgáltatással

> [!IMPORTANT]
> Kérjük, olvassa el az [Előfeltételek](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations) részletes SCVMM dokumentációját

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Az SCVMM által nem kezelt Windows Server 2012 R2 állomások 
Az alábbi lépések listája a [Hyper-V-állomások](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) és az Azure felhasználói konfigurációjára vonatkozik, amelyet az oktatóanyag követésével hajt [végre.](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Az előfeltételekben említettek szerint ezek a lépések csak fürtözött környezetben forgatókönyv, és nem egy önálló Hyper-V állomás konfigurációk.

1. A [működés közbeni fürtfrissítés](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) végrehajtásához kövesse a lépéseket. a működés közbeni fürtfrissítési folyamat végrehajtásához.
2. A fürtben bevezetett összes új Windows Server 2016-állomással távolítsa el a Windows Server 2012 R2 állomás hivatkozását az Azure Site Recovery webhely-helyreállítási szolgáltatásból az [itt] említett lépések végrehajtásával. Ez legyen az a gazdagép, amelyet a fürtből való & kizárásának levezetésére választott.
3. Miután az *Update-VMVersion* parancs végrehajtása megtörtént az összes virtuális gépen, a frissítések befejeződtek. 
4. Az [itt](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) említett lépésekkel regisztrálhatja az új Windows Server 2016-állomást az Azure Site Recovery szolgáltatásba. Kérjük, vegye figyelembe, hogy a Hyper-V hely már aktív, és csak regisztrálnia kell az új állomást a fürtben. 
5.  Nyissa meg az Azure Portalt, és ellenőrizze a replikált állapotot a helyreállítási szolgáltatásokon belül

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>A Windows Server 2012 R2 állomások frissítése az önálló SCVMM 2012 R2 kiszolgáló által kezelt
A Windows Sever 2012 R2 gazdagépeinek frissítése előtt frissítenie kell az SCVMM 2012 R2-t SCVMM 2016-ra. Kövesse az alábbi lépéseket: -

**Az önálló SCVMM 2012 R2 frissítése SCVMM 2016-ra**

1.  Az ASR-szolgáltató eltávolítása a Vezérlőpult> programok -> programok és szolgáltatások ->a Microsoft Azure Site Recovery elemre navigálva, majd kattintson az Eltávolítás gombra
2. [Az SCVMM adatbázis megőrzése és az operációs rendszer frissítése](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. A **Programok eltávolítása segédprogramban**válassza a **VMM** > **Eltávolítás**lehetőséget. b. Válassza **a Szolgáltatások eltávolítása**lehetőséget, majd a V MM felügyeleti kiszolgáló és a**VMM konzol lehetőséget.** c. Az **Adatbázis beállításai párbeszédpanelen**válassza **az Adatbázis megtartása**lehetőséget. d. Tekintse át az összegzést, és kattintson az **Eltávolítás gombra.**

4. [A VMM 2016 telepítése](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Indítsa el az SCVMM-et, és ellenőrizze **Refresh** az egyes állomások állapotát a **Szövetek** lapon. Meg kell látni állapotát "Figyelmet igényel". 
17. Telepítse a legújabb [Microsoft Azure webhely-helyreállítási szolgáltatót](https://aka.ms/downloaddra) az SCVMM-re.
16. Telepítse a legújabb [Microsoft Azure Recovery Service (MARS) ügynök](https://aka.ms/latestmarsagent) a fürt minden gazdagépére. Frissítsen annak biztosítására, hogy az SCVMM sikeresen le tudja kérdezni az állomásokat.

**A Windows Server 2012 R2 állomásainak frissítése Windows Server 2016-ra**

1. Kövesse az [itt](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) említett lépéseket a működés közbeni fürtfrissítési folyamat végrehajtásához. 
2. Miután hozzáadja az új állomást a fürthöz, frissítse az állomást az SCVMM konzolról, és telepítse a VMM-ügynököt erre a frissített állomásra.
3. Az *Update-VMVersion* végrehajtása a virtuális gépek virtuális gépverzióinak frissítéséhez. 
4.  Nyissa meg az Azure Portalon, és ellenőrizze a helyreállítási szolgáltatások trezorban lévő virtuális gépek replikált állapotát. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>A Frissítés Windows Server 2012 R2 állomásokat a magas rendelkezésre állású SCVMM 2012 R2 kiszolgáló kezeli
A Windows Sever 2012 R2 gazdagépeinek frissítése előtt frissítenie kell az SCVMM 2012 R2-t SCVMM 2016-ra. A következő frissítési módok támogatottak az Azure Site Recovery – Mixed mode-tal konfigurált SCVMM 2012 R2 kiszolgálók frissítése során, további VMM-kiszolgálók nélkül & vegyes módú további VMM-kiszolgálókkal.

**Frissítés SCVMM 2012 R2-re SCVMM 2016-ra**

1.  Az ASR-szolgáltató eltávolítása a Vezérlőpult> programok -> programok és szolgáltatások ->a Microsoft Azure Site Recovery elemre navigálva, majd kattintson az Eltávolítás gombra
2. Kövesse az [itt](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) említett lépéseket a végrehajtani kívánt frissítési mód alapján.
3. Indítsa el az SCVMM konzolt, és ellenőrizze **Refresh** az egyes állomások állapotát a **Fabrics** lapon. Meg kell látni állapotát "Figyelmet igényel".
4. Telepítse a legújabb [Microsoft Azure webhely-helyreállítási szolgáltatót](https://aka.ms/downloaddra) az SCVMM-re.
5. Frissítse a legújabb [Microsoft Azure Recovery Service (MARS) ügynök](https://aka.ms/latestmarsagent) a fürt minden gazdagépén. Frissítsen annak biztosítására, hogy az SC VMM sikeresen le tudja kérdezni az állomásokat.


**A Windows Server 2012 R2 állomásainak frissítése Windows Server 2016-ra**

1. Kövesse az [itt](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) említett lépéseket a működés közbeni fürtfrissítési folyamat végrehajtásához.
2. Miután hozzáadja az új állomást a fürthöz, frissítse az állomást az SCVMM konzolról, és telepítse a VMM-ügynököt erre a frissített állomásra.
3. Az *Update-VMVersion* végrehajtása a virtuális gépek virtuális gépverzióinak frissítéséhez. 
4.  Nyissa meg az Azure Portalon, és ellenőrizze a helyreállítási szolgáltatások trezorban lévő virtuális gépek replikált állapotát. 

## <a name="next-steps"></a>További lépések
Az állomások frissítése után elvégezheti a [teszt feladatátvételt](tutorial-dr-drill-azure.md) a replikáció és a vész-helyreállítási állapot állapotának teszteléséhez.

