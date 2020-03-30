---
title: VMware/fizikai vészhelyreállítás méretezése az Azure Site Recovery szolgáltatással
description: Megtudhatja, hogyan állíthatja be a vészhelyreállítást az Azure-ba nagyszámú helyszíni vmware virtuális gép vagy fizikai kiszolgálók számára az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 36cc63721fe003934aabfb3ae2a03a4113937ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256938"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Vészhelyreállítás beállítása méretezési szinten vmware virtuális gépekhez/fizikai kiszolgálókhoz

Ez a cikk ismerteti, hogyan állíthatja be a vész-helyreállítási azure-ba nagy számú (> 1000) helyszíni VMware virtuális gépek vagy fizikai kiszolgálók az éles környezetben, az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="define-your-bcdr-strategy"></a>A BCDR stratégia meghatározása

Az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia részeként helyreállítási pont célokat (RRO-k) és helyreállítási idő célokat (RTO-k) határozhat meg az üzleti alkalmazásokhoz és a számítási feladatokhoz. Az RTO azt az időtartamot és szolgáltatási szintet méri, amelyen belül egy üzleti alkalmazást vagy folyamatot vissza kell állítani és elérhetővé kell tenni a folytonossági problémák elkerülése érdekében.
- A Site Recovery folyamatos replikációt biztosít a VMware virtuális gépek és a fizikai kiszolgálók számára, valamint [egy SLA-t](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) az RTO számára.
- A vmware virtuális gépek nagyméretű vész-helyreállítási tervének megtervezésekor és a szükséges Azure-erőforrások kiszámítása során megadhat egy RTO-értéket, amelyet kapacitásszámításokhoz fog használni.


## <a name="best-practices"></a>Ajánlott eljárások

Néhány általános ajánlott eljárások a nagyméretű vész-helyreállítási. Ezeket az ajánlott eljárásokat a dokumentum következő szakaszai részletesebben tárgyaljuk.

- **Célkövetelmények azonosítása:** A vész-helyreállítás beállítása előtt becsülje meg a kapacitást és az erőforrásigényeket az Azure-ban.
- **Hely-helyreállítási összetevők megtervezése:** Annak kivizsgálása, hogy milyen Site Recovery-összetevőkre (konfigurációs kiszolgáló, folyamatkiszolgálók) van szüksége a becsült kapacitás eléréséhez.
- **Egy vagy több kibővített folyamatkiszolgáló beállítása:** Ne használja az alapértelmezés szerint futó folyamatkiszolgálót a konfigurációs kiszolgálón. 
- **Futtassa a legújabb frissítéseket:** A Site Recovery csoport rendszeresen kiadja a Site Recovery összetevőinek új verzióit, és győződjön meg arról, hogy a legújabb verziót futtatja. Ennek érdekében kövesse nyomon a frissítések [újdonságait,](site-recovery-whats-new.md) és [engedélyezze és telepítse a frissítéseket](service-updates-how-to.md) a kiadásukkor.
- **Proaktív figyelés:** A vészhelyreállítás folyamatos működése és működtetése során proaktív módon figyelnie kell a replikált gépek és az infrastruktúra-erőforrások állapotát és állapotát.
- **Vész-helyreállítási gyakorlatok:** Rendszeresen kell futtatnia a vész-helyreállítási gyakorlatokat. Ezek nem befolyásolják az éles környezetben, de nem segít biztosítani, hogy az Azure-ba történő feladatátvétel a várt módon fog működni, ha szükséges.



## <a name="gather-capacity-planning-information"></a>Kapacitástervezési információk gyűjtése

A helyszíni környezettel kapcsolatos információk összegyűjtése a cél (Azure) kapacitásigények felmérése és becslése érdekében.
- A VMware esetében futtassa a VMware virtuális gépek üzembe helyezésének tervezőjét ehhez.
- Fizikai kiszolgálók esetén manuálisan gyűjtse össze az adatokat.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>A VMware virtuális gépek üzembe helyezésének tervezőjének futtatása

A Deployment Planner segít a helyszíni VMware-környezettel kapcsolatos információk összegyűjtésében.

- Futtassa a Központi telepítés tervező egy olyan időszakban, amely a virtuális gépek tipikus lemorzsolódás. Ez pontosabb becsléseket és ajánlásokat eredményez.
- Azt javasoljuk, hogy futtassa a Központi telepítés tervező a konfigurációs kiszolgáló gépen, mivel a Planner kiszámítja átviteli a kiszolgáló, amelyen fut. [További információ](site-recovery-vmware-deployment-planner-run.md#get-throughput) az átviteli teljesítmény méréséről.
- Ha még nincs beállítva konfigurációs kiszolgáló:
    - [A](vmware-physical-azure-config-process-server-overview.md) Site Recovery összetevőinek áttekintése.
    - [Állítson be egy konfigurációs kiszolgálót,](vmware-azure-deploy-configuration-server.md)hogy futtassa rajta a Központi telepítési tervezőt.

Ezután futtassa a Plannert az alábbiak szerint:

1. További információ a Központi [telepítéstervezőről.](site-recovery-deployment-planner.md) Letöltheti a legújabb verziót a portálról, vagy [közvetlenül letöltheti.](https://aka.ms/asr-deployment-planner)
2. Tekintse át a Központi telepítéstervező [előfeltételeit](site-recovery-deployment-planner.md#prerequisites) és [legújabb frissítéseit,](site-recovery-deployment-planner-history.md) majd [töltse le és bontsa ki](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) az eszközt.
3. [Futtassa a Központi telepítéstervezőt](site-recovery-vmware-deployment-planner-run.md) a konfigurációs kiszolgálón.
4. [Jelentés készítése](site-recovery-vmware-deployment-planner-run.md#generate-report) a becslések és javaslatok összegzésére.
5. Elemezze a [jelentésajánlásait](site-recovery-vmware-deployment-planner-analyze-report.md) és [a költségbecsléseket.](site-recovery-vmware-deployment-planner-cost-estimation.md)

>[!NOTE]
> Alapértelmezés szerint az eszköz profilprofilra van konfigurálva, és legfeljebb 1000 virtuális gépről készít jelentést. Ezt a korlátot az ASRDeploymentPlanner.exe.config fájlBan található MaxVMsSupported kulcsérték növelésével módosíthatja.

## <a name="plan-target-azure-requirements-and-capacity"></a>Cél (Azure) tervkövetelményei és kapacitása

Az összegyűjtött becslések és javaslatok segítségével megtervezheti a célerőforrásokat és a kapacitást. Ha futtatta a Telepítéstervező vMware virtuális gépekhez, számos [jelentésjavaslatok](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) segítségével.

- **Kompatibilis virtuális gépek:** Ezzel a számmal azonosíthatja az Azure-ba történő vész-helyreállítási üzemre kész virtuális gépek számát. A hálózati sávszélességre és az Azure-magokra vonatkozó javaslatok ezen a számon alapulnak.
- **Szükséges hálózati sávszélesség:** Vegye figyelembe a kompatibilis virtuális gépek különbözeti replikációjához szükséges sávszélességet. 
    - A Planner futtatásakor percek ben adja meg a kívánt RPO-t. A javaslatok megmutatják az rpo 100%-os és 90%-os teljesítéséhez szükséges sávszélességet. 
    - A hálózati sávszélességre vonatkozó javaslatok figyelembe veszik a Tervezőben ajánlott konfigurációs kiszolgálók és folyamatkiszolgálók teljes számához szükséges sávszélességet.
- **Szükséges Azure-magok:** Vegye figyelembe, hogy a cél Azure-régióban a kompatibilis virtuális gépek száma alapján szükséges magok száma. Ha nem rendelkezik elegendő maggal, a feladatátvevő hely helyreállítása nem fogja tudni létrehozni a szükséges Azure virtuális gépeket.
- **Ajánlott virtuális gép kötegmérete:** Az ajánlott kötegméret a köteg kezdeti replikációjának alapértelmezés szerint 72 órán belüli befejezésén alapul, miközben 100%-os RPO-t teljesít. Az óra értéke módosítható.

Ezekkel a javaslatokkal megtervezheti az Azure-erőforrások, a hálózati sávszélesség és a virtuális gép kötegelés.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure-előfizetések és kvóták tervezése

Biztosítani szeretnénk, hogy a cél-előfizetésben elérhető kvóták elegendőek legyenek a feladatátvétel kezeléséhez.

**Tevékenység** | **Részletek** | **Művelet**
--- | --- | ---
**Ellenőrizze a magok** | Ha a rendelkezésre álló kvóta magjai nem egyeznek meg vagy haladják meg a feladatátvétel időpontjában a teljes célszámot, a feladatátvétel sikertelen lesz. | VMware virtuális gépek esetén ellenőrizze, hogy elegendő mag van-e a cél-előfizetésben a Deployment Planner alapjavaslatának teljesítéséhez.<br/><br/> Fizikai kiszolgálók esetén ellenőrizze, hogy az Azure-magok megfelelnek-e a manuális becsléseknek.<br/><br/> A kvóták ellenőrzéséhez az Azure Portalon > **Előfizetésben**kattintson a **Használat + kvóták**elemre.<br/><br/> [További információ](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a kvóták növeléséről.
**Feladatátvételi korlátok ellenőrzése** | A feladatátvételek száma nem haladhatja meg a Site Recovery feladatátvételi korlátját. |  Ha a feladatátvételek túllépik a korlátokat, hozzáadhat előfizetéseket, és több előfizetésre is átveheti a feladatátvételt, vagy növelheti az előfizetés kvótáját. 


### <a name="failover-limits"></a>Feladatátvételi korlátok

A korlátok azt jelzik, hogy a Site Recovery egy órán belül hány feladatátvételt támogat, feltételezve, hogy gépenként három lemez.

Mit jelent a megfelelés? Az Azure virtuális gép elindításához az Azure-nak egyes illesztőprogramokat kell rendszerindítási indítási állapotban lennie, és a szolgáltatások, például a DHCP automatikusan elindul.
- A megfelelő gépek már rendelkeznek ezekkel a beállításokkal.
- A Windows rendszert futtató gépek en proaktív módon ellenőrizheti a megfelelőséget, és szükség esetén megfelelőséget is tehet. [További információ](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- A Linux-gépek csak a feladatátvétel időpontjában kerülnek megfelelőségbe.

**A gép megfelel az Azure-nak?** | **Az Azure virtuális gép korlátai (felügyelt lemezfeladatátvétel)**
--- | --- 
Igen | 2000
Nem | 1000

- Korlátok feltételezik, hogy minimális más feladatok vannak folyamatban az előfizetés célrégióban.
- Egyes Azure-régiók kisebbek, és előfordulhat, hogy valamivel alacsonyabb korlátokkal rendelkeznek.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Infrastruktúra és virtuális gép kapcsolatának megtervezése

Az Azure-ba való feladatátvétel után a számítási feladatoknak ugyanúgy kell működniük, mint a helyszíni, és lehetővé kell tennie a felhasználók számára az Azure-beli virtuális gépeken futó számítási feladatok elérését.

- [További információ](site-recovery-active-directory.md#test-failover-considerations) az Active Directory vagy a helyszíni DNS-infrastruktúra Azure-ba történő fel- és feleslegének fel- és fel- és felkelődéséről.
- [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) az Azure-beli virtuális gépekhez való csatlakozás előkészítéséről feladatátvétel után.



## <a name="plan-for-source-capacity-and-requirements"></a>A forráskapacitás és -követelmények megtervezése

Fontos, hogy elegendő konfigurációs kiszolgálóval és kibővített folyamatkiszolgálóval álljon rendelkezésre a kapacitáskövetelmények teljesítéséhez. A nagyméretű telepítés megkezdésekor kezdje egyetlen konfigurációs kiszolgálóval és egyetlen horizontális felskálázási folyamatkiszolgálóval. Ahogy eléri az előírt határértékeket, adjon hozzá további kiszolgálókat.

>[!NOTE]
> VMware virtuális gépek, a Központi telepítési tervező néhány javaslatot tesz a konfigurációs és folyamatkiszolgálók van szüksége. Azt javasoljuk, hogy a következő eljárásokban szereplő táblákat használja a Deployment Planner javaslat követése helyett. 


## <a name="set-up-a-configuration-server"></a>Konfigurációs kiszolgáló beállítása
 
A konfigurációs kiszolgáló kapacitását befolyásolja a replikáló gépek száma, és nem az adatváltozási arány. Annak kidöntéséhez, hogy szüksége van-e további konfigurációs kiszolgálókra, használja ezeket a definiált virtuálisgép-korlátokat.

**Cpu** | **Memory (Memória)** | **Lemez gyorsítótára** | **Replikált gépkorlát**
 --- | --- | --- | ---
8 vCPU<br> 2 foglalat * 4 mag @ 2,5 Ghz | 16 GB | 600 GB | Akár 550 gép<br> Feltételezi, hogy minden gép három, egyenként 100 GB-os lemezzel rendelkezik.

- Ezek a korlátok egy OVF-sablonnal beállított konfigurációs kiszolgálón alapulnak.
- A korlátok feltételezik, hogy nem a konfigurációs kiszolgálón alapértelmezés szerint futó folyamatkiszolgálót használja.

Ha új konfigurációs kiszolgálót kell hozzáadnia, kövesse az alábbi utasításokat:

- [Hozzon létre egy konfigurációs kiszolgálót](vmware-azure-deploy-configuration-server.md) a VMware VM vész-helyreállítási egy OVF sablon használatával.
- [Állítson be egy konfigurációs kiszolgálót](physical-azure-set-up-source.md) manuálisan a fizikai kiszolgálókhoz, illetve az OVF-sablont nem használó VMware-telepítésekhez.

A konfigurációs kiszolgáló beállításakor vegye figyelembe a következőket:

- Konfigurációs kiszolgáló beállításakor fontos figyelembe venni azt az előfizetést és a tárolót, amelyen belül található, mivel ezeket a beállítások után nem szabad módosítani. Ha módosítania kell a tárolót, el kell választania a konfigurációs kiszolgálót a tárolóról, és újra regisztrálnia kell azt. Ez leállítja a virtuális gépek replikációját a tárolóban.
- Ha több hálózati adapterrel rendelkező konfigurációs kiszolgálót szeretne beállítani, ezt a beállítás során kell megtennie. Ezt nem teheti meg a konfigurációs kiszolgáló regisztrálása után a tárolóban.

## <a name="set-up-a-process-server"></a>Folyamatkiszolgáló beállítása

A folyamatkiszolgáló kapacitását az adatváltozási arányok befolyásolják, nem pedig a replikációra engedélyezett gépek száma.

- Nagy méretű telepítések esetén mindig rendelkeznie kell legalább egy horizontális felskálázási folyamatkiszolgálóval.
- Annak ellenőrzéséhez, hogy szükség van-e további kiszolgálókra, használja az alábbi táblázatot.
- Javasoljuk, hogy adjon hozzá egy kiszolgálót a legmagasabb specifikációval. 


**Cpu** | **Memory (Memória)** | **Lemez gyorsítótára** | **Lemorzsolódási arány**
 --- | --- | --- | --- 
12 vCPU<br> 2 foglalat*6 mag @ 2,5 Ghz | 24 GB | 1 GB | Akár 2 TB naponta

A folyamatkiszolgáló beállítása a következőképpen:

1. Tekintse át az [előfeltételeket](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Telepítse a kiszolgálót a [portálra](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)vagy a [parancssorból.](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)
3. Konfigurálja a replikált gépeket az új kiszolgáló használatára. Ha már vannak replikáló gépek:
    - A teljes folyamatkiszolgáló [munkaterhelését áthelyezheti](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) az új folyamatkiszolgálóra.
    - Másik lehetőségként [áthelyezhet](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) bizonyos virtuális gépeket az új folyamatkiszolgálóra.



## <a name="enable-large-scale-replication"></a>Nagyméretű replikáció engedélyezése

A kapacitás megtervezése és a szükséges összetevők és infrastruktúra üzembe helyezése után engedélyezze a nagyszámú virtuális gép replikációját.

1. A gépeket kötegekbe kell rendezni. Egy kötegen belül engedélyezi a virtuális gépek replikációját, majd továbblép a következő kötegre.

    - VMware virtuális gépek esetén használhatja az [ajánlott virtuális gép kötegméretét](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) a Deployment Planner jelentésben.
    - Fizikai gépek esetén azt javasoljuk, hogy azonosítsa a kötegeket a hasonló méretű és mennyiségű adatmennyiséggel rendelkező gépek, valamint a rendelkezésre álló hálózati átviteli teljesítmény alapján. A cél az, hogy köteggépek, amelyek valószínűleg befejezni a kezdeti replikáció körülbelül azonos idő alatt.
    
2. Ha egy gép lemezes változása magas, vagy meghaladja a központi telepítés thePlanner, áthelyezheti a nem kritikus fájlokat nem kell replikálni (például a napló dumps vagy temp fájlok) a gépről. VMware virtuális gépek esetén áthelyezheti ezeket a fájlokat egy külön lemezre, majd [kizárhatja a lemezt](vmware-azure-exclude-disk.md) a replikációból.
3. A replikáció engedélyezése előtt ellenőrizze, hogy a gépek megfelelnek-e a [replikációs követelményeknek.](vmware-physical-azure-support-matrix.md#replicated-machines)
4. Konfiguráljon replikációs házirendet [vmware virtuális gépekhez](vmware-azure-set-up-replication.md#create-a-policy) vagy [fizikai kiszolgálókhoz.](physical-azure-disaster-recovery.md#create-a-replication-policy)
5. A [VMware virtuális gépek](vmware-azure-enable-replication.md) vagy [fizikai kiszolgálók replikációjának](physical-azure-disaster-recovery.md#enable-replication)engedélyezése. Ezzel elindítja a kijelölt gépek kezdeti replikációját.

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése

A virtuális gépek első kötegének replikációjának megkezdése után kezdje el a telepítés figyelését az alábbiak szerint:  

1. Rendeljen hozzá egy vész-helyreállítási rendszergazdát a replikált gépek állapotának figyeléséhez.
2. A replikált elemek és az infrastruktúra [eseményeinek figyelése.](site-recovery-monitor-and-troubleshoot.md)
3. A kibővített folyamatkiszolgálók [állapotának figyelése.](vmware-physical-azure-monitor-process-server.md)
4. Iratkozzon fel, hogy [e-mail értesítéseket](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) kapjon az eseményekről a könnyebb figyelés érdekében.
5. Végezzen rendszeres [vész-helyreállítási gyakorlatokat](site-recovery-test-failover-to-azure.md), hogy minden a várt módon működjön.


## <a name="plan-for-large-scale-failovers"></a>Nagyméretű feladatátvételek tervezése

Katasztrófa esetén előfordulhat, hogy nagy számú gépet/számítási feladatokat kell átadnia az Azure-ba. Készüljön fel az ilyen típusú eseményre az alábbiak szerint.

A feladatátvételre az alábbiak szerint készülhet fel előre:

- [Készítse elő az infrastruktúrát és a virtuális gépeket,](#plan-infrastructure-and-vm-connectivity) hogy a számítási feladatok feladatátvétel után elérhetők legyenek, és hogy a felhasználók hozzáférhessenek az Azure virtuális gépekhez.
- Vegye figyelembe a [feladatátvételi korlátokat](#failover-limits) a dokumentum korábbi részén. Győződjön meg arról, hogy a feladatátvételek e korlátok on belül lesznek.
- Rendszeres [vész-helyreállítási gyakorlatok futtatása](site-recovery-test-failover-to-azure.md). A fúrók segítenek:
    - Keresse meg a hiányosságokat a központi telepítés feladatátvétel előtt.
    - Becsülje meg az alkalmazások végpontok között futó RTO-ját.
    - Becslés idáig a számítási feladatok.
    - Az IP-címtartomány ütközésének azonosítása.
    - A fúrók futtatásakor azt javasoljuk, hogy ne használjon éles hálózatokat a fúrókhoz, ne használja ugyanazokat az alhálózati neveket az éles és teszthálózatokban, és minden egyes gyakorlat után törölje a tesztfeladat-átvételeket.

Nagyszabású feladatátvétel futtatásához a következőket javasoljuk:

1. Hozzon létre helyreállítási terveket a számítási feladatok feladatátvételhez.
    - Minden helyreállítási terv legfeljebb 50 gép feladatátvételét válthatja ki.
    - [További információ](recovery-plan-overview.md) a helyreállítási tervekről.
2. Azure Automation runbook-parancsfájlok hozzáadása helyreállítási tervekhez, az Azure-beli manuális feladatok automatizálásához. A tipikus feladatok közé tartozik a terheléselosztók konfigurálása, a DNS frissítése stb. [További információ](site-recovery-runbook-automation.md)
2. Feladatátvétel előtt készítse elő a Windows-gépeket, hogy azok megfeleljenek az Azure-környezetnek. [A feladatátvételi korlátok](#plan-azure-subscriptions-and-quotas) magasabbak a megfelelő gépek esetében. [További információ](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) a runbookokról.
4.  Indítsa el a feladatátvételt a [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell-parancsmaggal együtt egy helyreállítási tervvel.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Site Recovery monitorozása](site-recovery-monitor-and-troubleshoot.md)
