---
title: Vészhelyreállítás az Azure Site Recovery nagyszámú VMware virtuális gépek vagy fizikai kiszolgálók az Azure beállítása |} A Microsoft Docs
description: Megtudhatja, hogyan állíthat be vészhelyreállítást az Azure Site Recovery a helyszíni VMware virtuális gépek vagy fizikai kiszolgálók nagy számú Azure-bA.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237269"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Nagy számú VMware virtuális gépek/fizikai kiszolgálók vészhelyreállításának beállítása

Ez a cikk bemutatja, hogyan állítható be a nagy számú (> 1000) a helyszíni VMware virtuális gépek vagy fizikai kiszolgálók az éles környezetben az Azure-bA vész-helyreállítási használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="define-your-bcdr-strategy"></a>A BCDR-stratégia kidolgozása

Az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia részeként meghatározhatja a helyreállításipont-célkitűzések (Rpo) és a helyreállítási helyreállításiidő-célkitűzések (RTO) üzleti alkalmazások és számítási feladataihoz. RTO méri időt és a szolgáltatás időtartamára szint, amelyen belül egy üzleti alkalmazás vagy folyamat visszaállított és elérhetőnek kell lenniük, folytonosságát hibák elkerülése érdekében.
- A Site Recovery VMware virtuális gépek és fizikai kiszolgálók folyamatos replikációt biztosít és a egy [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) RTO számára.
- A VMware virtuális gépek és az Azure-erőforrásokat kell vizsgálhatja megtervezésekor nagy méretű vész-helyreállítási, egy kapacitás számításokhoz használt RTO értéket is megadhat.


## <a name="best-practices"></a>Ajánlott eljárások

Nagy méretű vész-helyreállítási általános ajánlott eljárásokat. Ajánlott eljárások a dokumentum a következő szakaszokban részletesebben ismertetik.

- **Cél-követelményeinek azonosítása**: Becsülje meg a kapacitás engedélykiadó az Azure-ban vész-helyreállítási beállítása előtt.
- **Site Recovery-összetevők tervezése**: Döntse el, hogy milyen a Site Recovery-összetevők (konfigurációs kiszolgáló, folyamatkiszolgáló) csak szüksége lehet a becsült kapacitás.
- **Állítsa be legalább egy horizontális felskálázási folyamatkiszolgáló**: Ne használja a folyamatkiszolgálót, alapértelmezés szerint fut, a konfigurációs kiszolgálón. 
- **Futtassa a a legújabb frissítések**: A Site Recovery csapata rendszeres időközönként a Site Recovery-összetevők új verzióinak kiadások, és meg kell győződnie arról, a legújabb verziókat futtatja. Az adott érdekében nyomon [Újdonságok](site-recovery-whats-new.md) frissítések, és [engedélyezése, és telepítse a frissítéseket](service-updates-how-to.md) felszabadításakor.
- **Proaktív monitorozás**: Vész-helyreállítási városoknak kapunk, proaktív módon figyelje az állapot és a replikált gépek és infrastruktúra-erőforrások állapotát.
- **Vészhelyreállítási próba**: Futtassa a vészhelyreállítási próbák rendszeres időközönként. Ezek az éles környezetben ne befolyásolják, de biztosíthatja, hogy a feladatátvétel az Azure-bA fog működni, szükség esetén a vártnak.



## <a name="gather-capacity-planning-information"></a>Kapacitás-tervezési adatainak összegyűjtése

A helyszíni környezetben, mérje fel, és a cél (Azure) kapacitás igényei meghatározásához információt gyűjteni.
- A VMware, a VMware virtuális gépekhez, ehhez a Deployment Planner futtatása.
- A fizikai kiszolgálók esetében manuálisan gyűjtse össze az adatokat.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>A Deployment Planner a VMware virtuális gépek futtatása

A Deployment Planner segít a helyszíni VMware-környezetet információt gyűjteni.

- A Deployment Planner futtatása egy időszakban, amely a virtuális gépek átlagos adatváltozás jelöli. Ekkor a pontosabb becslést és javaslatok.
- Azt javasoljuk, hogy, a Deployment Planner futtatása az a konfigurációs kiszolgálón, mivel a Planner átviteli sebességet, amelyen fut a kiszolgálóról számítja ki. [További](site-recovery-vmware-deployment-planner-run.md#get-throughput) kapcsolatos átviteli sebesség mérését.
- Ha még nem rendelkezik a konfigurációs kiszolgáló beállítása:
    - [Áttekintés](vmware-physical-azure-config-process-server-overview.md) a Site Recovery-összetevőit.
    - [Állítsa be a konfigurációs kiszolgáló](vmware-azure-deploy-configuration-server.md), annak érdekében, hogy a Deployment Planner futtatása rajta.

Ezután futtassa a Planner a következő:

1. [Ismerje meg](site-recovery-deployment-planner.md) a Deployment Planner eszközben. Letöltheti a legújabb verzióra a portálról, vagy [töltenie közvetlenül](https://aka.ms/asr-deployment-planner).
2. Tekintse át a [Előfeltételek](site-recovery-deployment-planner.md#prerequisites) és [legújabb frissítéseinek](site-recovery-deployment-planner-history.md) a Deployment Planner a és [letöltéséhez és kibontásához](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) az eszközt.
3. [A Deployment Planner futtatása](site-recovery-vmware-deployment-planner-run.md) a konfigurációs kiszolgálón.
4. [Jelentés készítése](site-recovery-vmware-deployment-planner-run.md#generate-report) becsléseket és javaslatok.
5. Elemezheti a [javaslatok jelentés](site-recovery-vmware-deployment-planner-analyze-report.md) és [becsléseket költség](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Alapértelmezés szerint az eszköz profilja van konfigurálva, és jelentést készít az akár 1000 virtuális gépet. Növelje a MaxVMsSupported kulcs értékét az ASRDeploymentPlanner.exe.config fájlban módosíthatja ezt a korlátot.

## <a name="plan-target-azure-requirements-and-capacity"></a>Cél (Azure) követelményeket és a kapacitás

Használja a gyűjtött becsléseket és javaslatok, megtervezheti a céloldali erőforrások és kapacitások. Ha futtatta a Deployment Planner a VMware virtuális gépekhez, számos használhatja a [javaslatok jelentés](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) segítséget.

- **Kompatibilis virtuális gépek**: Ez a szám, amely készen áll az Azure-bA vész-helyreállítási virtuális gépek azonosításához használja. Ez a szám hálózati sávszélességet és az Azure-magok kapcsolatos ajánlások alapul.
- **Szükséges hálózati sávszélesség**: Megjegyzés: a kompatibilis virtuális gép változásreplikációjához szükséges sávszélesség. 
    - A Planner futtatásakor adja meg a kívánt helyreállítási Időkorlát percben. A javaslatok megjelenítése a sávszélességet, hogy a helyreállítási Időkorlát 100 %-os és az idő 90 %. 
    - A hálózati sávszélességre vonatkozó javaslatok felelnek figyelembe kell venni a konfigurációs és folyamatkiszolgálók ajánlott a Planner teljes számát a sávszélességet.
- **Szükséges Azure-magok**: Megjegyzés: a cél Azure-régió, a magok számát a kompatibilis virtuális gépek száma alapján. Nincs elég mag, ha feladatátvétel során a Site Recovery nem fog tudni a szükséges Azure virtuális gépek létrehozásához.
- **Javasolt Virtuálisgép-köteg mérete**: Az ajánlott kötegméret alapján lehetővé teszi a köteg kezdeti replikáció 72 órán belül Befejezés alapértelmezés szerint egy helyreállítási Időkorlát 100 %-os betartása mellett. Az óra értéke módosítható.

Ezekkel az ajánlásokkal segítségével tervezze meg az Azure-erőforrások, a hálózati sávszélesség és a virtuális gép kötegelés.

## <a name="plan-azure-subscriptions-and-quotas"></a>Tervezze meg az Azure-előfizetések és kvóták

Győződjön meg arról, hogy megfelelőek-e a cél előfizetésben elérhető kvóták kezeli a feladatátvételt, szeretnénk.

**Tevékenység** | **Részletek** | **Művelet**
--- | --- | ---
**Ellenőrizze a magok** | Ha a rendelkezésre álló kvótát magok nem egyenlő vagy a teljes száma túllépi a feladatátvétel időpontjában, feladatátvétel sikertelen lesz. | A VMware virtuális gépeket ellenőrizze, hogy elég mag a célul szolgáló előfizetésben a Deployment Planner alapvető javaslat kielégítése érdekében.<br/><br/> A fizikai kiszolgálók esetében ellenőrizze, hogy az Azure-magok teljesíti-e a manuális becsléseket.<br/><br/> Ellenőrizze a kvótákat, az Azure Portalon > **előfizetés**, kattintson a **használat + kvóták**.<br/><br/> [További](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) kapcsolatos kvóták növelését.
**Feladatátvételi felső korlát ellenőrzése** | Feladatátvételi teszteket száma nem haladhatja meg a Site Recovery feladatátvételi korlátait. |  Ha feladatátvételt túllépni a korlátokat, előfizetéseket, és átadja a feladatokat több előfizetéssel, vagy egy előfizetéshez tartozó kvóta növeléséhez. 


### <a name="failover-limits"></a>Feladatátvételi korlátok

A korlátok a feladatátvételi teszteket, amelyek támogatottak a Site Recovery egy órán belül gépenként három lemezt feltételezve számát jelzi.

Mi felel meg mean? Egy Azure virtuális gép elindításához Azure van szükség az egyes illesztőprogramok rendszerindító kezdő állapotban kell lennie, és szolgáltatásokat, mint például a DHCP automatikus indításra kell beállítani.
- Gépek, amelyek megfelelnek a hely már rendelkezik ezeket a beállításokat.
- Windows rendszerű gépek esetében proaktív módon ellenőrizheti megfelelőségét, és tenni őket a megfelelő, ha szükséges. [További információk](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux rendszerű gépek csak feladatátvétel időpontjában megfelelőségi való importálás.

**Gép megfelel-e az Azure-ral?** | **Az Azure virtuális gépekre korlátai (felügyelt lemez feladatátvétel)**
--- | --- 
Igen | 2000
Nem | 1000

- Korlátok azt feltételezik, hogy minimális más feladat a célrégióban az előfizetés folyamatban van.
- Egyes Azure-régiók mérete kisebb, és előfordulhat, hogy alacsonyabb korlátokkal rendelkeznek.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Tervezze meg az infrastruktúra és a virtuális gépek kapcsolatai

Az Azure-bA a feladatátvételt követően szüksége van a számítási feladatok működnek, mint a helyszíni és a felhasználók számára elérhetővé az Azure virtuális gépeken futó számítási feladatokat.

- [További](site-recovery-active-directory.md#test-failover-considerations) készül átadni a feladatokat az Active Directory vagy a DNS a helyszíni infrastruktúrát az Azure-bA.
- [További](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) előkészítéséről az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően.



## <a name="plan-for-source-capacity-and-requirements"></a>Forrás-kapacitás és a követelmények tervezése

Fontos, hogy elegendő konfigurációs és folyamatkiszolgálók horizontális felskálázás a kapacitásbeli követelmények kielégítése érdekében. A nagy léptékű üzembe helyezés megkezdése előtt, először egyetlen konfigurációs kiszolgálót, és a egy horizontális felskálázási folyamatkiszolgáló. Elérte a által előírt korlátozások, mint vegyen fel további kiszolgálókat.

>[!NOTE]
> A Deployment Planner a VMware virtuális gépek esetében lehetővé teszi a szükséges konfigurációs és folyamatkiszolgálót-kiszolgálókkal kapcsolatos ajánlásokat. Azt javasoljuk, hogy használja-e a táblákat, az alábbi eljárások a Deployment Planner javaslat helyett szerepel. 


## <a name="set-up-a-configuration-server"></a>Konfigurációs kiszolgáló beállítása
 
Konfigurációs kiszolgáló kapacitásának az érintett replikáló gépek száma szerint, és nem a data adatváltozásának. Döntse el, hogy szükséges-e további konfigurációs kiszolgálók, használja ezeket a virtuális gépekre korlátai definiálva.

**CPU** | **Memória** | **Gyorsítótárlemez** | **A replikált gép korlátja**
 --- | --- | --- | ---
8 Vcpu<br> 2 sockets * 4 mag, 2,5 GHz-es @ | 16 GB | 600 TB | 550 gépek<br> Feltételezi, hogy minden gép egyenként 100 GB-os három lemez rendelkezik-e.

- Ezek a korlátok a konfigurációs kiszolgáló OVF-sablon használatával alapulnak.
- A korlátok azt feltételezik, hogy a folyamatkiszolgáló alapértelmezés szerint a konfigurációs kiszolgálón futó nem használ.

Ha szeretne új konfigurációs kiszolgálót, az alábbi lépéseket követve:

- [Állítsa be a konfigurációs kiszolgáló](vmware-azure-deploy-configuration-server.md) VMware virtuális gép vész-helyreállítási egy OVF-sablon használatával.
- [Állítsa be a konfigurációs kiszolgáló](physical-azure-set-up-source.md) manuálisan a fizikai kiszolgálók számára, vagy a VMware-környezetekben, amelyek nem használhatják az OVF-sablonnal.

Konfigurációs kiszolgáló beállítása során vegye figyelembe, hogy:

- Konfigurációs kiszolgáló beállításakor fontos figyelembe venni az előfizetés és a tároló, amelyen belül található, mivel ezek nem módosítható a telepítés után. Ha módosítani szeretné a tárolóhoz, akkor szüntesse meg a konfigurációs kiszolgálót a tárolóból, és regisztrálja azt. Ezzel leállítja a virtuális gépek replikálását a tárolóban.
- Ha vissza szeretné állítani a konfigurációs kiszolgálót több hálózati adapterrel, akkor tegye ezt során. Nem ehhez a regisztrálás után a konfigurációs kiszolgálót a tárolóban.

## <a name="set-up-a-process-server"></a>Állítsa be a folyamatkiszolgáló

Folyamat kiszolgálókapacitás érintett forgalom díjait, és nem engedélyezett a replikáció gépek száma.

- Nagy központi telepítések mindig rendelkeznie kell legalább egy horizontális felskálázási folyamatkiszolgáló.
- Az alábbi táblázat segítségével döntse el, hogy szükséges-e további kiszolgálókat.
- Azt javasoljuk, hogy a legmagasabb specifikációja kiszolgáló hozzáadása. 


**CPU** | **Memória** | **Gyorsítótárlemez** | **Adatváltozásának**
 --- | --- | --- | --- 
12 vcpu-k<br> 2 sockets * 6 mag, 2,5 GHz-es @ | 24 GB | 1 GB | Akár 2 TB naponta

Állítsa be a folyamatkiszolgáló a következőképpen:

1. Tekintse át a [Előfeltételek](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. A kiszolgáló telepítése a [portál](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), vagy a [parancssori](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Replikált gépek az új kiszolgáló használatára konfigurálja. Ha már rendelkezik replikáló gépek:
    - Is [áthelyezése](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) egy teljes folyamat server számítási feladatok az új folyamatkiszolgáló.
    - Lehetőségként [áthelyezése](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) adott az új folyamatkiszolgáló virtuális gépeket.



## <a name="enable-large-scale-replication"></a>Nagy méretű replikáció engedélyezése

Kapacitás megtervezése és a szükséges összetevők és az infrastruktúra üzembe helyezése, után nagyszámú virtuális gépek replikációjának engedélyezéséhez.

1. Rendezés gépek kötegekbe. A kötegbe tartozó virtuális gépek replikálásának engedélyezése, és ezután lépjen a következő köteg.

    - A VMware virtuális gépeket, használhatja a [javasolt Virtuálisgép-köteg mérete](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) a Deployment Planner jelentés.
    - Fizikai gépek javasoljuk, hogy kötegek gépeket, amelyek hasonló adatok mennyisége és mérete, és elérhető hálózati átviteli sebesség alapján azonosítható. A célja, hogy a batch gépek, amelyek valószínűleg körül ugyanannyi időt a azok kezdeti replikációjának befejezéséhez.
    
2. Ha egy gép lemez adatváltozás magas, vagy meghaladja az üzembe helyezési thePlanner korlátozásokat, áthelyezheti a nem kritikus fontosságú fájlokat nem kell replikálni a gép (például naplózási memóriaképek vagy ideiglenes fájlokat). A VMware virtuális gépekhez, áthelyezheti ezeket a fájlokat egy külön lemezt, majd [, hogy a lemez kizárása](vmware-azure-exclude-disk.md) a replikációból.
3. A replikáció engedélyezése előtt ellenőrizze, hogy a számítógépek megfelelnek [replikációs követelményeit](vmware-physical-azure-support-matrix.md#replicated-machines).
4. A replikációs szabályzat konfigurálása [VMware virtuális gépek](vmware-azure-set-up-replication.md#create-a-policy) vagy [fizikai kiszolgálók](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. A replikáció engedélyezése [VMware virtuális gépek](vmware-azure-enable-replication.md) vagy [fizikai kiszolgálók](physical-azure-disaster-recovery.md#enable-replication). Ez elindít a kiválasztott gépek kezdeti replikációja.

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése

Miután az első kötegbe tartozó virtuális gépek replikálása indíthat, figyelni a központi telepítés a következő:  

1. Rendeljen hozzá egy katasztrófa utáni helyreállítás rendszergazdai replikált gépek állapotának monitorozásához.
2. [Események figyelése](site-recovery-monitor-and-troubleshoot.md) replikált elemek és az infrastruktúra.
3. [Az állapotmonitorozás](vmware-physical-azure-monitor-process-server.md) a horizontális felskálázási folyamat kiszolgálók.
4. Regisztráljon, [e-mail-értesítések](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) események megkönnyíteni a felügyeletet.
5. Végezhet rendszeres [vészhelyreállítási próbák](site-recovery-test-failover-to-azure.md), annak érdekében, hogy minden a várt módon működik.


## <a name="plan-for-large-scale-failovers"></a>Nagy méretű feladatátvételek tervezése

A vészhelyreállítási egy eseményt szüksége lehet a gépek vagy a számítási feladatok Azure-ban nagy számú feladatátvételt. A következő készítse elő az ilyen típusú események.

Előkészítheti az előzetesen a feladatátvétel a következő:

- [Az infrastruktúra és a virtuális gépek előkészítése](#plan-infrastructure-and-vm-connectivity) , hogy a feladatátvétel után a számítási feladatok elérhető lesz, és úgy, hogy a felhasználók férhetnek hozzá az Azure virtuális gépek.
- Megjegyzés: a [feladatátvételi korlátok](#failover-limits) a jelen dokumentum korábbi. Ellenőrizze, hogy a feladatátvételek e korlátok fog esni.
- Rendszeresen futtassa [vészhelyreállítási próbák](site-recovery-test-failover-to-azure.md). Gyakorlatok segítséget:
    - A lefedettségi hézagok meghatározása az üzembe helyezés, a feladatátvétel előtt.
    - Becsült teljes körű RTO alkalmazásai számára.
    - A számítási feladatokhoz teljes körű RPO becslése.
    - Azonosítsa az IP-cím-tartomány ütközik.
    - Gyakorlatok futtatásakor azt javasoljuk, hogy nem gyakorlatokat éles hálózati környezetben használja, kerülje ugyanazon alhálózat nevét a termelési és tesztelési hálózatokban, és feladatátvételi teszteket törlése után minden részletes.

A feladatátvételi nagy léptékű futtatásához, a következőket javasoljuk:

1. Számítási feladatok feladatátvétel helyreállítási terveket hozhat létre.
    - Minden helyreállítási terv feladatátvétele 50 gépet is indíthat.
    - [További](recovery-plan-overview.md) helyreállítási tervek ismertetése.
2. Azure Automation-runbook szkriptek hozzáadása helyreállítási tervekhez, az Azure-ban bármely manuális feladatok automatizálására. A leggyakoribb feladatok közé tartozik a konfigurálása a load Balancer terheléselosztók frissítése DNS stb. [További információ](site-recovery-runbook-automation.md)
2. A feladatátvétel előtt Windows-gépek előkészítése, hogy megfelelnek-e az Azure-környezethez. [Feladatátvételi korlátok](#plan-azure-subscriptions-and-quotas) magasabb gépek, amelyek megfelelnek. [További](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) runbookokkal kapcsolatos.
4.  A feladatátvétel aktiválja a [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell-parancsmaggal együtt a helyreállítási terv.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A figyelő a Site Recovery](site-recovery-monitor-and-troubleshoot.md)
