---
title: A Windows Server 2012 R2-gazdagépek és az Azure Site Recovery a Windows Server 2016-ra konfigurált SCVMM frissítése
description: Ismerje meg, hogyan vészhelyreállítás beállítása az Azure-bA az Azure Stack-beli virtuális gépek az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 351213749dcec2b4c16728c04230c75a12179118
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410983"
---
# <a name="upgrade-windows-server-2012-r2-hosts-scvmm-2012-r2-configured-with-azure-site-recovery-to-windows-server-2016--scvmm-2016"></a>Frissítés a Windows Server 2012 R2-állomások, SCVMM 2012 R2-ben konfigurált az Azure Site Recovery a Windows Server 2016 és az SCVMM 2016-ban

Ez a cikk bemutatja, hogyan frissítse a Windows Server 2012 R2-gazdagépek és az Azure Site Recoveryvel, a Windows Server 2016 és az SCVMM 2016-ban konfigurált SCVMM 2012 R2

A Site Recovery hozzájárul (az üzleti folytonossági és vészhelyreállítási BCDR) stratégia. A szolgáltatás biztosítja, hogy a virtuális gépek számítási feladataihoz és továbbra is elérhető, amikor várhatóan váratlan leállások esetén.

> [!IMPORTANT]
> Amikor frissít a Windows Server 2012 R2-gazdagépek, amelyek már az Azure Site Recoveryvel a replikáció, az ebben a dokumentumban leírt lépéseket kell követnie. Minden kiválasztott frissítés alternatív elérési út nem támogatott állapotok eredményezhet, és a replikáció vagy feladatátvétel végrehajtásához szünet eredményezhet.


Ez a cikk bemutatja a környezetben a következő konfigurációk frissítése:

> [!div class="checklist"]
> * **A Windows Server 2012 R2 olyan gazdagépet, amely nem a SCVMM által felügyelt** 
> * **A Windows Server 2012 R2 olyan gazdagépet, amely egy önálló SCVMM 2012 R2-kiszolgáló által kezelt** 
> * **A Windows Server 2012 R2 olyan gazdagépet, amelyen a magas rendelkezésre állású SCVMM 2012 R2-kiszolgáló által kezelt**


## <a name="prerequisites--factors-to-consider"></a>Előfeltételek és mérlegelendő

Mielőtt frissít, vegye figyelembe a következőket:-

- SCVMM által nem felügyelt Windows Server 2012 R2-állomások rendelkezik, és egy önálló környezet beállítása, számítunk szünet a replikációban, ha a frissítés végrehajtásához.
- Ha korábban bejelölte "*nem tárolja a saját kulcsokat az Active Directory mellett az elosztott kulcskezelés*" során először telepíti az SCVMM 2012 R2, a frissítések nem lesz sikeres.

- Ha a System Center 2012 R2 VMM használata 

    - Ellenőrizze az adatbázis-információ a VMM-ben: **VMM-konzol** -> **beállítások** -> **általános** -> **adatbázis-kapcsolat**
    - A System Center Virtual Machine Manager-ügynök szolgáltatáshoz használt szolgáltatásfiókokat ellenőrzése
    - Győződjön meg arról, hogy a VMM-adatbázis biztonsági másolatának.
    - Jegyezze fel az SCVMM részt vevő kiszolgálók adatbázis nevére. Ezt megteheti az **VMM-konzol** -> **beállítások** -> **általános** -> **adatbázis-kapcsolat**
    - Jegyezze fel az elsődleges 2012 R2 és a helyreállítási VMM-kiszolgálók VMM azonosítója. A VMM-azonosító a beállításjegyzékből "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup" található.
    - Győződjön meg arról, hogy az új SCVMMs a fürthöz hozzáadni kívánt, ugyanazokat a neveket, mielőtt volt. 

- Ha replikál között, két van-e a helyek mindkét oldalon SCVMMs által kezelt, győződjön meg arról, hogy frissítsen a helyreállítási oldalon először az elsődleges oldal frissítése előtt.
> [!WARNING]
> Az SCVMM 2012 R2 rendszerben elosztott kulcskezelés, a frissítés során válassza ki a **tárolni a titkosítási kulcsokat az Active Directory**. A szolgáltatásfiók és az elosztott kulcskezelés beállításait körültekintően válassza. A választott beállítás alapján a titkosított adatok, például a sablonokban található jelszavak előfordulhat, hogy nem érhető el a frissítés után el, és esetleg a befolyásolja az Azure Site Recovery replikációs

> [!IMPORTANT]
> Tekintse meg a részletes SCVMM dokumentációját [Előfeltételek](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>A Windows Server 2012 R2 olyan gazdagépet, amely nem a SCVMM által felügyelt 
Az alábbi lépéseket listája a felhasználói konfigurációt az érvényes [Hyper-V gazdagépek Azure-bA](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) ennek végrehajtása [oktatóanyag](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)

> [!WARNING]
> Ahogy már említettük, az előfeltételeket, ezeket a lépéseket egy fürtözött környezetben forgatókönyvet, és nem egy önálló Hyper-V-gazdagép konfiguráció csak a alkalmazni.

1. A lépések végrehajtásához kövesse a [működés közbeni Fürtfrissítés.](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) a fürt működés közbeni frissítési folyamat végrehajtásához.
2. Minden új Windows Server 2016-gazdagéppel a fürt bemutatott távolítsa el a hivatkozást a Windows Server 2012 R2-gazdagép az Azure Site Recovery szolgáltatásból [itt] említett lépéseket követve. Ez legyen a fogadó úgy döntött, hogy kiürítési & kizárása a fürtből.
3. Miután a *Update-VMVersion* parancsot az összes virtuális gép hajtott végre, a frissítések befejeződtek. 
4. Az említett lépésekkel [Itt](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) regisztrálni az Azure Site Recovery a Windows Server 2016 új gazdagépre. Vegye figyelembe, hogy a Hyper-V hely már aktív, és csak regisztrálnia kell az új gazdagéppel a fürtben. 
5.  Nyissa meg az Azure Portalra, és ellenőrizze a replikált állapotinformációit belül a Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Frissítse a különálló SCVMM 2012 R2-kiszolgáló által kezelt Windows Server 2012 R2-állomások
Mielőtt frissít a Windows Server 2012 R2-állomások, frissítenie az SCVMM 2012 R2-ben az SCVMM 2016-ban. Kövesse az alábbi lépéseket:-

**Önálló SCVMM 2012 R2 frissítése a SCVMM 2016-ban**

1.  Eltávolítás az ASR-szolgáltató navigáljon a Vezérlőpult -> Programok -> Programok és szolgáltatások -> Microsoft Azure Site Recovery, és kattintson az Eltávolítás
2. [Az SCVMM-adatbázis megtartását, és az operációs rendszer verziófrissítéséhez](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. A **Hozzáadás programok**válassza **VMM** > **Eltávolítás**. b. Válassza ki **szolgáltatások eltávolítása**, majd válassza ki a V**MM felügyeleti kiszolgáló és a VMM-konzol**. c. A **adatbázis-beállítások**válassza **adatbázis megőrzése**. d. Tekintse át az összefoglalást, és kattintson a **Eltávolítás**.

4. [Telepítse a VMM 2016-ban](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Indítsa el az SCVMM, és minden gazdagépre állapotának ellenőrzéséhez **hálók** fülre. Kattintson a **frissítése** beolvasni a legfrissebb állapotát. Megtekintheti az állapotát, a "Figyelmet". 
17. Telepítse a legújabb [a Microsoft Azure Site Recovery Provider](http://aka.ms/downloaddra) az SCVMM a.
16. Telepítse a legújabb [a Microsoft Azure Recovery Service (MARS) ügynöke](http://aka.ms/latestmarsagent) a fürt minden gazdagépen. Annak biztosítása érdekében, SCVMM sikerül lekérdeznie a gazdagépek frissítése.

**Frissítés a Windows Server 2012 R2-állomások a Windows Server 2016-ra**

1. Kövesse a lépéseket [Itt](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) a fürt működés közbeni frissítési folyamat végrehajtásához. 
2. Miután hozzáadta az új állomást a fürthöz, frissítse az SCVMM-konzolon a gazdagépet a VMM-ügynök telepítése a frissített gazdagépen.
3. Hajtsa végre *Update-VMVersion* frissíteni a virtuális gépek a virtuális gép verzióját. 
4.  Azure portal megnyitása, és ellenőrizze a virtuális gépeket a helyreállítási tár belül replikált állapotát. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Frissítés a Windows Server 2012 R2-állomások magas rendelkezésre állású SCVMM 2012 R2-kiszolgáló által kezelt
Mielőtt frissít a Windows Server 2012 R2-állomások, frissítenie az SCVMM 2012 R2-ben az SCVMM 2016-ban. Frissítés a következő módok használata támogatott az Azure Site Recovery - vegyes üzemmód további VMM-kiszolgáló nélkül & vegyes üzemmód további VMM-kiszolgálókkal konfigurált SCVMM 2012 R2-kiszolgálók frissítése közben.

**SCVMM 2012 R2 frissítése az SCVMM 2016-ban**

1.  Eltávolítás az ASR-szolgáltató navigáljon a Vezérlőpult -> Programok -> Programok és szolgáltatások -> Microsoft Azure Site Recovery, és kattintson az Eltávolítás
2. Kövesse a lépéseket [Itt](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) a végrehajtani kívánt frissítési módtól függ.
3. Indítsa el az SCVMM-konzolon, és minden gazdagépre állapotának ellenőrzéséhez **hálók** fülre. Kattintson a **frissítése** beolvasni a legfrissebb állapotát. Megtekintheti az állapotát, a "Figyelmet".
4. Telepítse a legújabb [a Microsoft Azure Site Recovery Provider](http://aka.ms/downloaddra) az SCVMM a.
5. A legújabb frissítés [a Microsoft Azure Recovery Service (MARS) ügynöke](http://aka.ms/latestmarsagent) a fürt minden gazdagépen. Annak érdekében, SC a VMM nem tudja lekérdezni a gazdagépek frissítése.


**Frissítés a Windows Server 2012 R2-állomások a Windows Server 2016-ra**

1. Kövesse a lépéseket [Itt](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) a fürt működés közbeni frissítési folyamat végrehajtásához.
2. Miután hozzáadta az új állomást a fürthöz, frissítse az SCVMM-konzolon a gazdagépet a VMM-ügynök telepítése a frissített gazdagépen.
3. Hajtsa végre *Update-VMVersion* frissíteni a virtuális gépek a virtuális gép verzióját. 
4.  Azure portal megnyitása, és ellenőrizze a virtuális gépeket a helyreállítási tár belül replikált állapotát. 

## <a name="next-steps"></a>További lépések
Elvégzése után a gazdagépek frissítését hajtja végre, egy [feladatátvételi teszt](tutorial-dr-drill-azure.md) állapotát, a replikálás és vészhelyreállítás recovey állapotának ellenőrzéséhez.

